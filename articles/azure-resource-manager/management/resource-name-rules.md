---
title: Restrições de nomeação de recursos
description: Mostra as regras e restrições para nomear recursos Azure.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: a1dbe409eb5479c4027834cd33e095d12c31e238
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210198"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Regras de nomenclatura e restrições para recursos do Azure

Este artigo resume as regras de nomeação e as restrições para os recursos da Azure. Para recomendações sobre como nomear recursos, consulte [convenções recomendadas de nomeação e marcação.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

Este artigo lista os recursos pelo espaço de nome do fornecedor de recursos. Para obter uma lista de como os fornecedores de recursos correspondem aos serviços da Azure, consulte [os fornecedores de recursos para os serviços Azure](azure-services-resource-providers.md).

Os nomes dos recursos são insensíveis a caso, a menos que indicados na coluna de caracteres válidos.

Nas tabelas seguintes, o termo alfanumérico refere-se:

* **a** através de **z** (letras minúsculas)
* **A** a **Z** (letras maiúsculas)
* **0** a **9** (números)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores | grupo de recursos | 3-63 | Letras minúsculas e números.<br><br>Comece com carta minúscula. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | serviço | global | 1-50 | Alfanuméricos e hífenes.<br><br>Comece com a letra, e termine com alfanumérico. |
> | serviço / apis | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / questões | api | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / problemas / anexos | issue | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / problemas / comentários | issue | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / operações | api | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / operações / tags | operation | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / lançamentos | api | 1-80 | Alfanuméricos, sublinhados e hífens.<br><br>Comece e termine com alfanumérico ou sublinhado. |
> | serviço / apis / esquemas | api | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / tagDescrições | api | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / tags | api | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / api-versão-conjuntos | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / autorizaçãoServadores | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / backends | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / certificados | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / diagnósticos | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / grupos | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / grupos / utilizadores | group | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / identidadeProviders | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / madeireiros | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / notificações | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / notificações / destinatárioSMails | notificação | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / openidConnectProviders | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / políticas | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / produtos | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / produtos / apis | produto | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / produtos / grupos | produto | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / produtos / tags | produto | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / propriedades | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / subscrições | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / tags | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / modelos | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / utilizadores | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | configuraçõesStores | grupo de recursos | 5-50 | Alfanuméricos, sublinhados e hífens. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | fechaduras | âmbito de atribuição | 1-90 | Alfanuméricos, períodos, sublinhados, hífens e parênteses.<br><br>Não pode terminar em período. |
> | políticasAssinsagens | âmbito de atribuição | 1-128 nome de exibição<br><br>Nome de recurso 1-64<br><br>1-24 nome de recurso no âmbito do grupo de gestão | O nome do visor pode conter quaisquer caracteres.<br><br>O nome do recurso não pode incluir `%` e não pode terminar com período ou espaço. |
> | políticasDefinições | âmbito de definição | 1-128 nome de exibição<br><br>Nome de recurso 1-64 | O nome do visor pode conter quaisquer caracteres.<br><br>O nome do recurso não pode incluir `%` e não pode terminar com período ou espaço. |
> | políticasSetDefinitions | âmbito de definição | 1-128 nome de exibição<br><br>Nome de recurso 1-64<br><br>1-24 nome de recurso no âmbito do grupo de gestão | O nome do visor pode conter quaisquer caracteres.<br><br>O nome do recurso não pode incluir `%` e não pode terminar com período ou espaço.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | automação Contas | grupo de recursos & região <br>(Ver nota abaixo) | 6-50 | Alfanuméricos e hífens.<br><br>Comece com a letra, e termine com alfanumérico. |
> | automação Contas/certificados | conta de automação | 1-128 | Não posso usar:<br> `<>*%&:\?.+/` <br><br>Não pode acabar com o espaço.  |
> | automação Contas/ ligações | conta de automação | 1-128 | Não posso usar:<br> `<>*%&:\?.+/` <br><br>Não pode acabar com o espaço. |
> | automação Contas/credenciais | conta de automação | 1-128 | Não posso usar:<br> `<>*%&:\?.+/` <br><br>Não pode acabar com o espaço. |
> | automação Contas / runbooks | conta de automação | 1-63 | Alfanuméricos, sublinhados e hífens.<br><br>Comece com a carta.  |
> | automação Contas/horários | conta de automação | 1-128 | Não posso usar:<br> `<>*%&:\?.+/` <br><br>Não pode acabar com o espaço. |
> | automação Contas/ variáveis | conta de automação | 1-128 | Não posso usar:<br> `<>*%&:\?.+/` <br><br>Não pode acabar com o espaço. |
> | automação Contas/observadores | conta de automação | 1-63 |  Alfanuméricos, sublinhados e hífens.<br><br>Comece com a carta. |
> | automaçãoAcontas / webhooks | conta de automação | 1-128 | Não posso usar:<br> `<>*%&:\?.+/` <br><br>Não pode acabar com o espaço. |

> [!NOTE]
> Os nomes das contas de automação são únicos por região e grupo de recursos. Os nomes das contas de Automação eliminadas podem não estar imediatamente disponíveis.

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | lotAcons | Região | 3-24 | Letras minúsculas e números. |
> | lotacos / aplicações | conta de lote | 1-64 | Alfanuméricos, sublinhados e hífens. |
> | lotacos /certificados | conta de lote | 5-45 | Alfanuméricos, sublinhados e hífens. |
> | lotacos / piscinas | conta de lote | 1-64 | Alfanuméricos, sublinhados e hífens. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | blockchainMembers | global | 2-20 | Letras minúsculas e números.<br><br>Comece com carta minúscula. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | botServs | global | 2-64 |  Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. |
> | botServices / canais | serviço bot | 2-64 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. |
> | botServices / Conexões | serviço bot | 2-64 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. |
> | enterpriseChannels | grupo de recursos | 2-64 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | Redis | global | 1-63 | Alfanuméricos e hífens.<br><br>Comece e termine com alfanumérico. Hífenes consecutivos não são permitidos. |
> | Redis / firewallRules | Redis | 1-256 | Alfanuméricos |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | perfis | grupo de recursos | 1-260 | Alfanuméricos e hífens.<br><br>Comece e termine com alfanumérico. |
> | perfis / pontos finais | global | 1-50 | Alfanuméricos e hífens.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | certificadoRes | grupo de recursos | 3-30 | Alfanuméricos. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | accounts | grupo de recursos | 2-64 | Alfanuméricos e hífens.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | disponibilidadeSets | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Termine com alfanumérico ou sublinhado. |
> | DiskEncryptionSets | grupo de recursos | 1-80 | Alfanuméricos e sublinhados. |
> | discos | grupo de recursos | 1-80 | Alfanuméricos e sublinhados. |
> | galerias | grupo de recursos | 1-80 | Alfanuméricos e períodos.<br><br>Comece e termine com alfanumérico. |
> | galerias / aplicações | galeria | 1-80 | Alfanuméricos, hífens e períodos.<br><br>Comece e termine com alfanumérico. |
> | galerias / aplicações/versões | aplicação | Inteiro de 32 bits | Números e períodos. |
> | galerias / imagens | galeria | 1-80 | Alfanuméricos, sublinhados, hífens e períodos.<br><br>Comece e termine com alfanumérico. |
> | galerias / imagens / versões | image | Inteiro de 32 bits | Números e períodos. |
> | imagens | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Termine com alfanumérico ou sublinhado. |
> | instantâneos | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Termine com alfanumérico ou sublinhado. |
> | virtualMachines | grupo de recursos | 1-15 (Windows)<br>1-64 (Linux)<br><br>Veja a nota abaixo. | Não se pode usar o espaço ou estes caracteres:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>Os VMs do Windows não podem incluir período ou fim com hífen.<br><br>Os VMs Linux não podem terminar com período ou hífen. |
> | virtualMachineScaleSets | grupo de recursos | 1-15 (Windows)<br>1-64 (Linux)<br><br>Veja a nota abaixo. | Não se pode usar o espaço ou estes caracteres:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>Não posso começar com sublinhado. Não pode terminar com período ou hífen. |

> [!NOTE]
> As máquinas virtuais Azure têm dois nomes distintos: nome de recurso e nome de hospedeiro. Quando se cria uma máquina virtual no portal, o mesmo valor é utilizado para ambos os nomes. As restrições na tabela anterior são para o nome de anfitrião. O nome do recurso real pode ter até 64 carateres.

## <a name="microsoftcommunication"></a>Microsoft.Communication

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | Serviços de comunicação | global | 1-63 | Alfanuméricos, hífens e sublinhados. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | grupos de contentores | grupo de recursos | 1-63 | Letras minúsculas, números e hífenes.<br><br>Não se pode começar ou acabar com hífen. Os hífenes consecutivos não são permitidos. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | registos | global | 5-50 | Alfanuméricos. |
> | registos / buildTasks | registry | 5-50 | Alfanuméricos. |
> | registos / buildTasks/steps | construir tarefa | 5-50 | Alfanuméricos. |
> | registos / replicações | registry | 5-50 | Alfanuméricos. |
> | registos / scopeMaps | registry | 5-50 | Alfanuméricos, hífens e sublinhados. |
> | registos / tarefas | registry | 5-50 | Alfanuméricos, hífens e sublinhados. |
> | registos / fichas | registry | 5-50 | Alfanuméricos, hífens e sublinhados. |
> | registos / webhooks | registry | 5-50 | Alfanuméricos. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | geridoClusters | grupo de recursos | 1-63 | Alfanuméricos, sublinhados e hífens.<br><br>Comece e termine com alfanumérico. |
> | openShiftManagedClusters | grupo de recursos | 1-30 | Alfanuméricos. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | hubs | grupo de recursos | 1-64 | Alfanuméricos.<br><br>Comece com a carta.  |
> | hubs / autorizaçõesPolícias | hub | 1-50 | Alfanuméricos, sublinhados e períodos.<br><br>Comece e termine com alfanumérico. |
> | centros / conectores | hub | 1-128 | Alfanuméricos e sublinhados.<br><br>Comece com a carta. |
> | centros / conectores/mapeamentos | conector | 1-128 | Alfanuméricos e sublinhados.<br><br>Comece com a carta. |
> | centros / interações | hub | 1-128 | Alfanuméricos e sublinhados.<br><br>Comece com a carta. |
> | hubs / kpi | hub | 1-512 | Alfanuméricos e sublinhados.<br><br>Comece com a carta. |
> | centros / ligações | hub | 1-512 | Alfanuméricos e sublinhados.<br><br>Comece com a carta. |
> | centros / previsões | hub | 1-512 | Alfanuméricos e sublinhados.<br><br>Comece com a carta. |
> | centros / perfis | hub | 1-128 | Alfanuméricos e sublinhados.<br><br>Comece com a carta. |
> | hubs / relacionamentosLinks | hub | 1-512 | Alfanuméricos e sublinhados.<br><br>Comece com a carta. |
> | centros / relacionamentos | hub | 1-512 | Alfanuméricos e sublinhados.<br><br>Comece com a carta. |
> | hubs / funAsignments | hub | 1-128 | Alfanuméricos e sublinhados.<br><br>Comece com a carta. |
> | centros / vistas | hub | 1-512 | Alfanuméricos e sublinhados.<br><br>Comece com a carta. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | associações | grupo de recursos | 1-180 | Não posso usar:<br>`%&\\?/`<br><br>Não pode terminar com período ou espaço. |
> | recursosProviders | grupo de recursos | 3-64 | Não posso usar:<br>`%&\\?/`<br><br>Não pode terminar com período ou espaço. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | empregos | grupo de recursos | 3-24 | Alfanuméricos, hífens, sublinhados e períodos. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | áreas de trabalho | grupo de recursos | 3-30 | Alfanuméricos, sublinhados e hífens |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | fábricas | global | 3-63 | Alfanuméricos e hífens.<br><br>Comece e termine com alfanumérico. |
> | fábricas / fluxos de dados | fábrica | 1-260 | Não posso usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / conjuntos de dados | fábrica | 1-260 | Não posso usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / integraçãoRuntimes | fábrica | 3-63 | Alfanuméricos e hífens.<br><br>Comece e termine com alfanumérico. |
> | fábricas / serviços de linkedservices | fábrica | 1-260 | Não posso usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / oleodutos | fábrica | 1-260 | Não posso usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / gatilhos | fábrica | 1-260 | Não posso usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / gatilhos / rerunSTroggers | gatilho | 1-260 | Não posso usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Letras minúsculas e números. |
> | contas / computePolicias | conta | 3-60 | Alfanuméricos, hífens e sublinhados. |
> | contas / dadosLakeStoreAccounts | conta | 3-24 | Letras minúsculas e números. |
> | contas / firewallRules | conta | 3-50 | Alfanuméricos, hífens e sublinhados. |
> | contas / armazenamentoCotações | conta | 3-60 | Alfanuméricos, hífens e sublinhados. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Letras minúsculas e números. |
> | contas / firewallRules | conta | 3-50 | Alfanuméricos, hífens e sublinhados. |
> | contas / virtualNetworkRules | conta | 3-50 | Alfanuméricos, hífens e sublinhados. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | services | grupo de recursos | 2-62 | Alfanuméricos, hífens, períodos e sublinhados.<br><br>Comece com alfanumérico. |
> | serviços / projetos | serviço | 2-57 | Alfanuméricos, hífens, períodos e sublinhados.<br><br>Comece com alfanumérico. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores | global | 3-63 | Letras minúsculas, hífens e números.<br><br>Não se pode começar ou acabar com hífen. |
> | servidores / bases de dados | servidores | 1-63 | Alfanuméricos e hífens. |
> | servidores / firewallRules | servidores | 1-128 | Alfanuméricos, hífens e sublinhados. |
> | servidores / virtualNetworkRules | servidores | 1-128 | Alfanuméricos e hífens. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores | global | 3-63 | Letras minúsculas, hífens e números.<br><br>Não se pode começar ou acabar com hífen. |
> | servidores / bases de dados | servidores | 1-63 | Alfanuméricos e hífens. |
> | servidores / firewallRules | servidores | 1-128 | Alfanuméricos, hífens e sublinhados. |
> | servidores / virtualNetworkRules | servidores | 1-128 | Alfanuméricos e hífens. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DbforPostgresql

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores | global | 3-63 | Letras minúsculas, hífens e números.<br><br>Não se pode começar ou acabar com hífen. |
> | servidores / bases de dados | servidores | 1-63 | Alfanuméricos e hífens. |
> | servidores / firewallRules | servidores | 1-128 | Alfanuméricos, hífens e sublinhados. |
> | servidores / virtualNetworkRules | servidores | 1-128 | Alfanuméricos e hífens. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | IotHubs | global | 3-50 | Alfanuméricos e hífens.<br><br>Não pode acabar com hífen. |
> | IotHubs / certificados | Hub IoT | 1-64 | Alfanuméricos, hífens, períodos e sublinhados. |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | Alfanuméricos, hífens, períodos e sublinhados. |
> | provisionamentoServiadores | grupo de recursos | 3-64 | Alfanuméricos e hífens.<br><br>Termine com alfanumérico. |
> | provisionamentoServiaservação Serviços/Certificados | provisionamentoServiadores | 1-64 | Alfanuméricos, hífens, períodos e sublinhados. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | laboratórios | grupo de recursos | 1-50 | Alfanuméricos, sublinhados e hífens. |
> | laboratórios / imagens personalizadas | laboratório | 1-80 | Alfanuméricos, sublinhados, hífens e parênteses. |
> | laboratórios / fórmulas | laboratório | 1-80 | Alfanuméricos, sublinhados, hífens e parênteses. |
> | laboratórios / virtualmachines | laboratório | 1-15 (Windows)<br>1-64 (Linux) | Alfanuméricos e hífens.<br><br>Comece e termine com alfanumérico. Não podem ser todos os números. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | bases de dadosAconses | global | 3-44 | Letras minúsculas, números e hífenes.<br><br>Comece com letra ou número minúsculo. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | domínios | grupo de recursos | 3-50 | Alfanuméricos e hífens. |
> | domínios / tópicos | domínio | 3-50 | Alfanuméricos e hífens. |
> | eventoSubscrições | grupo de recursos | 3-64 | Alfanuméricos e hífens. |
> | tópicos | grupo de recursos | 3-50 | Alfanuméricos e hífens. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | grupo de recursos | 6-50 | Alfanuméricos e hífens.<br><br>Comece com a carta. Termine com letra ou número. |
> | espaços de nome | global | 6-50 | Alfanuméricos e hífens.<br><br>Comece com a carta. Termine com letra ou número. |
> | espaços de nome / AutorizaçõesRules | espaço de nomes | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com letra ou número. |
> | espaços de nome / desastreRecoveryConfigs | espaço de nomes | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com letra ou número. |
> | espaços de nomes / eventhubs | espaço de nomes | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com letra ou número. |
> | espaços de nomes / eventhubs / autorizaçõesRules | centro de eventos | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com letra ou número. |
> | espaços de nomes / eventhubs / grupos de consumidores | centro de eventos | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com letra ou número. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | global | 3-59 | Alfanuméricos e hífens<br><br>Comece e termine com letra ou número. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | empregos | grupo de recursos | 2-64 | Alfanuméricos e hífens.<br><br>Comece com a carta. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | grupos de ação | grupo de recursos | 1-260 | Não posso usar:<br>`/&%\?` <br><br>Não pode terminar com espaço ou período.  |
> | componentes | grupo de recursos | 1-260 | Não posso usar:<br>`%&\?/` <br><br>Não pode terminar com espaço ou período.  |
> | agendadoQueryRules | grupo de recursos | 1-260 | Não posso usar:<br>`*<>%{}&:\\?/#` <br><br>Não pode terminar com espaço ou período.  |
> | metricAlerts | grupo de recursos | 1-260 | Não posso usar:<br>`*#&+:<>?@%{}\/` <br><br>Não pode terminar com espaço ou período.  |
> | atividadeSalerts | grupo de recursos | 1-260 | Não posso usar:<br>`<>*%{}&:\\?+/#` <br><br>Não pode terminar com espaço ou período.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | IoTApps | global | 2-63 | Letras minúsculas, números e hífens.<br><br>Comece com letra ou número minúsculo. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | cofres | global | 3-24 | Alfanuméricos e hífens.<br><br>Comece com a carta. Termine com letra ou dígito. Não pode conter hífenes consecutivos. |
> | cofres / segredos | Cofre | 1-127 | Alfanuméricos e hífens. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | global | 4-22 | Letras minúsculas e números.<br><br>Comece com a carta. |
> | /clusters / bases de dados | cluster | 1-260 | Alfanuméricos, hífens, espaços e períodos. |
> | /clusters / bases de dados / dataConhecões | base de dados | 1-40 | Alfanuméricos, hífens, espaços e períodos. |
> | /clusters / bases de dados / ligações eventhub | base de dados | 1-40 | Alfanuméricos, hífens, espaços e períodos. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | integraçãoAconselhos | grupo de recursos | 1-80 | Alfanuméricos, hífens, sublinhados, períodos e parênteses. |
> | integração Contas/assembleias | conta de integração | 1-80 | Alfanuméricos, hífens, sublinhados, períodos e parênteses. |
> | integração Contas /lotesConfigurations | conta de integração | 1-20 | Alfanuméricos. |
> | integração Contas/certificados | conta de integração | 1-80 | Alfanuméricos, hífens, sublinhados, períodos e parênteses. |
> | integração Contas / mapas | conta de integração | 1-80 | Alfanuméricos, hífens, sublinhados, períodos e parênteses. |
> | integraçãoCotas/parceiros | conta de integração | 1-80 | Alfanuméricos, hífens, sublinhados, períodos e parênteses. |
> | integração Contas / rosettanet processos de confirmação | conta de integração | 1-80 | Alfanuméricos, hífens, sublinhados, períodos e parênteses. |
> | integraçãoCos de contas / esquemas | conta de integração | 1-80 | Alfanuméricos, hífens, sublinhados, períodos e parênteses. |
> | integração Contas /sessões | conta de integração | 1-80 | Alfanuméricos, hífens, sublinhados, períodos e parênteses. |
> | integração ServiçoEnvironments | grupo de recursos | 1-80 | Alfanuméricos, hífens, períodos e sublinhados. |
> | integraçãoServiceEnvironments / managedApis | ambiente de serviço de integração | 1-80 | Alfanuméricos, hífens, períodos e sublinhados. |
> | fluxos de trabalho | grupo de recursos | 1-80 | Alfanuméricos, hífens, sublinhados, períodos e parênteses. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | planos de compromissos | grupo de recursos | 1-260 | Não posso usar:<br>`<>*%&:?+/\\`<br><br>Não pode terminar com um espaço. |
> | webServices | grupo de recursos | 1-260 | Não posso usar:<br>`<>*%&:?+/\\`<br><br>Não pode terminar com um espaço. |
> | áreas de trabalho | grupo de recursos | 1-260 | Não posso usar:<br>`<>*%&:?+/\\`<br><br>Não pode terminar com um espaço. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | áreas de trabalho | grupo de recursos | 3-33 | Alfanuméricos e hífens. |
> | espaços de trabalho / computas | área de trabalho | 2-16 | Alfanuméricos e hífens. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | nomeações para assediadas do utilizador | grupo de recursos | 3-128 | Alfanuméricos, hífens e sublinha<br><br>Comece com letra ou número. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | accounts | grupo de recursos | 1-98 (para nome de grupo de recursos e nome da conta) | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | mediaservices | grupo de recursos | 3-24 | Letras minúsculas e números. |
> | mediaservices / liveEvents | Serviço de mídia | 1-32 | Alfanuméricos e hífens.<br><br>Comece com alfanumérico. |
> | mediaservices / liveEvents / liveOutputs | Evento ao vivo | 1-256 | Alfanuméricos e hífens.<br><br>Comece com alfanumérico. |
> | mediaservices / streamingEndpoints | Serviço de mídia | 1-24 | Alfanuméricos e hífens.<br><br>Comece com alfanumérico. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | applicationGateways | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | applicationSecurityGroups | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | azureFirewalls | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Termine com alfanumérico ou sublinhado. |
> | bastionHosts | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | conexões | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | dnsZones | grupo de recursos | 1-63 caracteres<br><br>2 a 34 rótulos<br><br>Cada rótulo é um conjunto de caracteres separados por um período. Por exemplo, **contoso.com** tem 2 rótulos. | Cada rótulo pode conter alfanuméricos, sublinhados e hífenes.<br><br>Cada etiqueta é separada por um período. |
> | expressRouteCircuits | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | firewallPolicies | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | firewallPolicies / ruleGroups | política de firewall | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | frontDoors | global | 5-64 | Alfanuméricos e hífens.<br><br>Comece e termine com alfanumérico. |
> | frontdoorWebApplicationFirewallPolicies | grupo de recursos | 1-128 | Alfanuméricos.<br><br>Comece com a carta. |
> | loadBalancers | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | loadBalancers / inboundNatRules | balançador de carga | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | localNetworkGateways | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkInterfaces | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkSecurityGroups | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkSecurityGroups / regras de segurança | grupo de segurança de rede | 1-80 |  Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkWatchers | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | privateDnsZones | grupo de recursos | 1-63 caracteres<br><br>2 a 34 rótulos<br><br>Cada rótulo é um conjunto de caracteres separados por um período. Por exemplo, **contoso.com** tem 2 rótulos. | Cada rótulo pode conter alfanuméricos, sublinhados e hífenes.<br><br>Cada etiqueta é separada por um período. |
> | privateDnsZones / virtualNetworkLinks | zona privada de DNS | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | publicIPAddresses | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | publicIPPrefixos | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeFilters | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeFilters / routeFilterrules | filtro de rota | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeTables | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeTables / rotas | tabela de rotas | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | serviceEndpointPolicies | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | trafficmanagerprofiles | global | 1-63 | Alfanuméricos, hífens e períodos.<br><br>Comece e termine com alfanumérico. |
> | virtualNetworkGateways | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | virtualNetworks | grupo de recursos | 2-64 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | virtualnetworks / subnetas | rede virtual | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | virtualNetworks / virtualNetworkPeerings | rede virtual | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | virtualWans | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | VPNGateways | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | VPNGateways / vpnConnections | Gateway de VPN | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | VPNSites | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | espaços de nome | global | 6-50 | Alfanuméricos e hífens<br><br>Comece com a carta. Termine com alfanumérico. |
> | espaços de nome / AutorizaçõesRules | espaço de nomes | 1-256 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece alfanumérico. |
> | espaços de nomes / notificaçõesHubs | espaço de nomes | 1-260 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece alfanumérico. |
> | espaços de nome / notificaçõesHubs / Autorizações | centro de notificação | 1-256 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece alfanumérico. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | grupo de recursos | 4-63 | Alfanuméricos e hífens.<br><br>Comece e termine com alfanumérico. |
> | áreas de trabalho | global | 4-63 | Alfanuméricos e hífens.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | soluções | área de trabalho | N/D | Para soluções da autoria da Microsoft, o nome deve estar no padrão:<br>`SolutionType(WorkspaceName)`<br><br>Para soluções da autoria de terceiros, o nome deve estar no padrão:<br>`SolutionType[WorkspaceName]`<br><br>Por exemplo, um nome válido é:<br>`AntiMalware(contoso-IT)`<br><br>O tipo de solução é sensível a maiôs. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | dashboards | grupo de recursos | 3-160 | Alfanuméricos e hífens.<br><br>Para utilizar caracteres restritos, adicione uma etiqueta com **o nome de título oculto** com o nome do painel de instrumentos que pretende utilizar. O portal apresenta esse nome ao mostrar o painel de instrumentos. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | Alfanuméricos e hífens.<br><br>Não se pode começar com hífen. Não se pode usar hífenes consecutivos. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | capacidades | region | 3-63 | Letras ou números minúsculos<br><br>Comece com carta minúscula. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | cofres | grupo de recursos | 2-50 | Alfanuméricos e hífens.<br><br>Comece com a carta. |
> | cofres / backupPolicies | cofre | 3-150 | Alfanuméricos e hífens.<br><br>Comece com a carta. Não pode acabar com hífen. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | espaços de nome | global | 6-50 | Alfanuméricos e hífens.<br><br>Comece com uma letra. Termine com uma letra ou número. |
> | espaços de nome / AutorizaçõesRules | espaço de nomes | 1-50 |  Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com alfanumérico. |
> | espaços de nome / HybridConnections | espaço de nomes | 1-260 | Alfanuméricos, períodos, hífens, sublinhados e cortes.<br><br>Comece e termine com alfanumérico. |
> | espaços de nome / HybridConnections/autorizaçõesRules | conexão híbrida | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com alfanumérico. |
> | espaços de nome / WcfRelays | espaço de nomes | 1-260 | Alfanuméricos, períodos, hífens, sublinhados e cortes.<br><br>Comece e termine com alfanumérico. |
> | espaços de nome / WcfRelays / autorizaçõesRules | Retransmissão wcf | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | implementações | grupo de recursos | 1-64 | Alfanuméricos, sublinhados, parênteses, hífens e períodos. |
> | grupos de recursos | subscrição | 1-90 | Alfanuméricos, sublinhados, parênteses, hífens, períodos e caracteres unicode que correspondem à [documentação regex](/rest/api/resources/resourcegroups/createorupdate).<br><br>Não pode terminar com o período. |
> | tagNames | recurso | 1-512 | Não posso usar:<br>`<>%&\?/` |
> | tagNames / tagValues | nome tag | 1-256 | Todos os personagens. |
> | modeloSpecs | grupo de recursos | 1-90 | Alfanuméricos, sublinhados, parênteses, hífens e períodos. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | espaços de nome | global | 6-50 | Alfanuméricos e hífens.<br><br>Comece com uma letra. Termine com uma letra ou número.<br><br>Para obter mais informações, consulte [Criar espaço para nomes.](/rest/api/servicebus/create-namespace) |
> | espaços de nome / AutorizaçõesRules | espaço de nomes | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com alpina. |
> | espaços de nome / desastreRecoveryConfigs | global | 6-50 | Alfanuméricos e hífens.<br><br>Comece com a carta. Termine com alfanumérico. |
> | espaços de nome / migraçõesConfigurations | espaço de nomes |  | Deve ser sempre **$default.** |
> | espaços de nomes / filas | espaço de nomes | 1-260 | Alfanuméricos, períodos, hífens, sublinhados e cortes.<br><br>Comece e termine com alfanumérico. |
> | espaços de nome / filas / autorizaçõesRules | fila | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com alpina. |
> | espaços de nomes / tópicos | espaço de nomes | 1-260 | Alfanuméricos, períodos, hífens, sublinhados e cortes.<br><br>Comece e termine com alfanumérico. |
> | espaços de nomes / tópicos / autorizaçõesRules | tópico | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com alpina. |
> | espaços de nomes / tópicos / subscrições | tópico | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com alpina. |
> | espaços de nomes / tópicos / subscrições / regras | subscrição | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com alpina. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | region | 4-23 | Letras minúsculas, números e hífenes.<br><br>Comece com carta minúscula. Termine com letra ou número minúsculo. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | sinalR | global | 3-63 | Alfanuméricos e hífens.<br><br>Comece com a carta. Termine com letra ou número.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | managedInstances | global | 1-63 | Letras minúsculas, números e hífenes.<br><br>Não se pode começar ou acabar com hífen. <br><br> Não se pode ter personagens especiais, `@` como. |
> | servidores | global | 1-63 | Letras minúsculas, números e hífenes.<br><br>Não se pode começar ou acabar com hífen. |
> | servidores / administradores | servidor |  | Deve `ActiveDirectory` ser. |
> | servidores / bases de dados | servidor | 1-128 | Não posso usar:<br>`<>*%&:\/?`<br><br>Não pode terminar com período ou espaço. |
> | servidores / bases de dados / syncGroups | base de dados | 1-150 | Alfanuméricos, hífens e sublinhados. |
> | servidores / elásticos | servidor | 1-128 | Não posso usar:<br>`<>*%&:\/?`<br><br>Não pode terminar com período ou espaço. |
> | servidores / grupos de failover | global | 1-63 | Letras minúsculas, números e hífenes.<br><br>Não se pode começar ou acabar com hífen. |
> | servidores / firewallRules | servidor | 1-128 | Não posso usar:<br>`<>*%&:;\/?`<br><br>Não pode terminar com o período. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | armazenamento Contas | global | 3-24 | Letras minúsculas e números. |
> | armazenamentoCotas / blobServices | conta de armazenamento |  | Deve `default` ser. |
> | armazenamentoSCotas / blobServices / contentores | conta de armazenamento | 3-63 | Letras minúsculas, números e hífenes.<br><br>Comece com letra ou número minúsculo. Não se pode usar hífenes consecutivos. |
> | armazenamentoSCotas / serviços de ficheiros | conta de armazenamento |  | Deve `default` ser. |
> | armazenamentoSCotas / fileServices / ações | conta de armazenamento | 3-63 | Letras minúsculas, números e hífenes.<br><br>Não se pode começar ou acabar com hífen. Não se pode usar hífenes consecutivos. |
> | armazenamentoCotas / gestãoPolícias | conta de armazenamento |  | Deve `default` ser. |
> | blob | contentor | 1-1024 | Quaisquer caracteres URL, sensíveis a casos |
> | fila | conta de armazenamento | 3-63 | Letras minúsculas, números e hífenes.<br><br>Não se pode começar ou acabar com hífen. Não se pode usar hífenes consecutivos. |
> | table | conta de armazenamento | 3-63 | Alfanuméricos.<br><br>Comece com a carta. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | storageSyncServices | grupo de recursos | 1-260 | Alfanuméricos, espaços, períodos, hífens e sublinhados.<br><br>Não pode terminar com período ou espaço. |
> | storageSyncServices / syncGroups | serviço de sincronização de armazenamento | 1-260 | Alfanuméricos, espaços, períodos, hífens e sublinhados.<br><br>Não pode terminar com período ou espaço. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | gestores | grupo de recursos | 2-50 | Alfanuméricos e hífens.<br><br>Comece com a carta. Termine com alfanumérico. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | streamingjobs | grupo de recursos | 3-63 | Alfanuméricos, hífens e sublinhados. |
> | streamingjobs / funções | trabalho de streaming | 3-63 | Alfanuméricos, hífens e sublinhados. |
> | streamingjobs / entradas | trabalho de streaming | 3-63 | Alfanuméricos, hífens e sublinhados. |
> | streamingjobs / saídas | trabalho de streaming | 3-63 | Alfanuméricos, hífens e sublinhados. |
> | streamingjobs / transformações | trabalho de streaming | 3-63 | Alfanuméricos, hífens e sublinhados. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | ambientes | grupo de recursos | 1-90 | Não posso usar:<br>`'<>%&:\?/#` |
> | ambientes / acessosPolícias | ambiente | 1-90 | Não posso usar:<br> `'<>%&:\?/#` |
> | ambientes / eventosSources | ambiente | 1-90 | Não posso usar:<br>`'<>%&:\?/#` |
> | ambientes / referênciaSSets | ambiente | 3-63 | Alfanuméricos |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | certificados | grupo de recursos | 1-260 | Não posso usar:<br>`/` <br><br>Não pode terminar com espaço ou período.  | 
> | serverfarms | grupo de recursos | 1-40 | Alfanuméricos e hífens. |
> | sites | global ou por domínio. Veja a nota abaixo. | 2-60 | Contém alfanuméricos e hífens.<br><br>Não se pode começar ou acabar com hífen. |
> | sites / slots | site | 2-59 | Alfanuméricos e hífens. |

> [!NOTE]
> Um site deve ter um URL globalmente único. Quando cria um web site que utiliza um plano de hospedagem, o URL é `http://<app-name>.azurewebsites.net` . O nome da aplicação deve ser globalmente único. Quando cria um web site que utiliza um Ambiente de Serviço de Aplicações, o nome da aplicação deve ser único dentro do [domínio para o Ambiente de Serviço de Aplicações.](../../app-service/environment/using-an-ase.md#app-access) Em ambos os casos, o URL do site é globalmente único.
>
> A Azure Functions tem as mesmas regras e restrições de nomeação que a Microsoft.Web/sites.

## <a name="next-steps"></a>Passos seguintes

Para recomendações sobre como nomear recursos, consulte [Ready: Convenções recomendadas de nomeação e marcação](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
