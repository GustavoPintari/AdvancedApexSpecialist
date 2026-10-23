# Advanced Apex Specialist Superbadge

Projeto Salesforce DX que utilizei em 2025 para cumprir o superbadge **Advanced Apex Specialist** do Trailhead, como parte da habilitação da certificação **Salesforce Certified Platform Developer**.

O repositório reúne as classes Apex, triggers e testes desenvolvidos ao longo dos desafios do superbadge, cobrindo triggers, testes, Apex assíncrono, integrações e serviços web.

## Conteúdo

Todo o código está em `force-app/main/default`.

### Triggers e framework de triggers

- `TriggerHandler`: classe base virtual que despacha cada contexto (`beforeInsert`, `afterUpdate`, etc.), com controle de recursão e bypass de handlers.
- `MaintenanceRequestHelper` e `OrderEventTH`: handlers que estendem `TriggerHandler`.
- Triggers: `AccountAddressTrigger`, `AccountDeletion`, `ClosedOpportunityTrigger`, `RestrictContactByName`, `MaintenanceRequest` (Case) e `OrderEventTrigger` (Platform Event `Order_Event__e`).

### Apex assíncrono

| Classe                    | Mecanismo                    |
| ------------------------- | ---------------------------- |
| `AccountProcessor`        | `@future`                    |
| `LeadProcessor`           | `Database.Batchable`         |
| `AddPrimaryContact`       | `Queueable`                  |
| `DailyLeadProcessor`      | `Schedulable`                |
| `WarehouseCalloutService` | `Queueable` com callout HTTP |
| `WarehouseSyncSchedule`   | `Schedulable`                |

### Integrações e serviços web

- **Callouts**: `AnimalLocator` (REST), `ParkLocator` / `ParkService` (SOAP) e `WarehouseCalloutService`, cada um com mocks para os testes.
- **Serviços expostos**:
  - `AccountManager`: REST (`/Accounts/*`).
  - `AssetService`: REST (`/lost`), que marca um ativo como perdido e abre um sinistro quando há seguro ativo.
  - `ProductZoningService`: REST (`/ProductZoning/*`), que consulta a zona de voo permitida em Custom Metadata (`Product_Geo_Mapping__mdt`).
  - `CredentialVerificationService`: SOAP (`webservice`), que valida certificações de contatos.

### Testes e utilitários

- Classes de teste para as classes de negócio, além de `TestDataFactory` e `RandomContactFactory` para criar dados de teste.
- `GenericBuilder`, `AccountBuilder`, `LeadBuilder` e `SchemaUtils`: utilitários para montar registros e consultar metadados. Alguns deles foram escritos por colegas, como indica o cabeçalho de cada arquivo.

### Azure Pipelines

`azure-pipelines.yml` é um pipeline disparado por webhook do Azure Boards. Ele cria automaticamente uma branch no GitHub (`F-<id>` para Features e `T-<id>` / `I-<id>` para itens filhos) e, se configurado, abre um pull request.

## Como usar

Requisitos: [Salesforce CLI](https://developer.salesforce.com/tools/salesforcecli) e uma org com os objetos e metadados do superbadge (por exemplo, a Trailhead Playground usada nos desafios).

```bash
sf org login web --alias superbadge
```

```bash
sf project deploy start --source-dir force-app --target-org superbadge
```

```bash
sf apex run test --target-org superbadge --code-coverage --result-format human
```

## Uso de inteligência artificial

A inteligência artificial **Claude** (Anthropic) foi utilizada apenas para escrever este `README.md`. O código do projeto não foi gerado por IA.
