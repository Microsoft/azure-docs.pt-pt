---
title: Restrições de nomeação de recursos
description: Mostra as regras e restrições para nomear recursos Azure.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 7b6a9e16ba28a2a0e5e4e181dc5650d2110eab88
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273920"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Regras de nomenclatura e restrições para recursos do Azure

Este artigo resume regras e restrições de nomeação para os recursos do Azure. Para recomendações sobre como nomear recursos, consulte Convenções de [nomeação e marcação recomendadas.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

Os nomes dos recursos são insensíveis a casos, a menos que especificamente anotados na coluna de caracteres válidos.

Nas tabelas a seguir, o termo alfanumérico refere-se a:

* **a** através **z** (letras minúsculas)
* **A** a **Z** (letras maiúsculas)
* **0** a **9** (números)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores | grupo de recursos | 3-63 | Letras minúsculas e números.<br><br>Comece com a carta minúscula. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | serviço | global | 1-50 | Alfanumérico.<br><br>Comece com a carta. |
> | serviço / apis | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / apis / problemas | api | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / apis / problemas / anexos | questão | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / apis / emites / comentários | questão | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / apis / operações | api | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / apis / operações / tags | operation | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / apis / lançamentos | api | 1-80 | Alfanuméricos, sublinhados e hífenes.<br><br>Comece e termine com alfanumérico ou sublinhado. |
> | serviço / apis / schemas | api | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / apis / tagDescrições | api | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / apis / tags | api | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / api-versão-sets | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço /autorizaçãoServidores | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / backends | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / certificados | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / diagnósticos | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço/grupos | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / grupos / utilizadores | grupo | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / identidadeFornecedores | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / madeireiros | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / notificações | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / notificações / destinatárioEmails | notificação | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / openidConnectProviders | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço /políticas | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / produtos | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / produtos / apis | produto | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / produtos / grupos | produto | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / produtos / tags | produto | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / propriedades | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço/subscrições | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / tags | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | modelos de serviço / | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |
> | serviço / utilizadores | serviço | 1-256 | Não pode usar:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | configuraçãoLojas | grupo de recursos | 5-50 | Alfanuméricos, sublinhados e hífenes. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | fechaduras | âmbito da atribuição | 1-90 | Alfanuméricos, períodos, sublinhados, hífenes e parênteses.<br><br>Não pode terminar no período. |
> | atribuições políticas | âmbito da atribuição | 1-128 nome de exibição<br><br>Nome de recurso 1-260 | O nome do visor pode conter quaisquer caracteres.<br><br>O nome do recurso não pode incluir `%` e não pode terminar com período ou espaço. |
> | definições políticas | âmbito de definição | 1-128 nome de exibição<br><br>Nome de recurso 1-260 | O nome do visor pode conter quaisquer caracteres.<br><br>O nome do recurso não pode incluir `%` e não pode terminar com período ou espaço. |
> | policySetDefinitions | âmbito de definição | 1-128 nome de exibição<br><br>Nome de recurso 1-260 | O nome do visor pode conter quaisquer caracteres.<br><br>O nome do recurso não pode incluir `%` e não pode terminar com período ou espaço.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | automatizaçõesContas | grupo de recursos | 6-50 | Alfanuméricos e hífenes.<br><br>Comece com a letra, e termine com alfanumérico. |
> | automatizaçãoContas / certificados | conta de automação | 1-128 | Não pode usar:<br> `<>*%&:\?.+/` <br><br>Não pode terminar com o espaço.  |
> | automatizaçõesContas / conexões | conta de automação | 1-128 | Não pode usar:<br> `<>*%&:\?.+/` <br><br>Não pode terminar com o espaço. |
> | automatizaçãoContas / credenciais | conta de automação | 1-128 | Não pode usar:<br> `<>*%&:\?.+/` <br><br>Não pode terminar com o espaço. |
> | automaçãoContas / livros de execução | conta de automação | 1-63 | Alfanuméricos, sublinhados e hífenes.<br><br>Comece com a carta.  |
> | automatizaçãoContas / horários | conta de automação | 1-128 | Não pode usar:<br> `<>*%&:\?.+/` <br><br>Não pode terminar com o espaço. |
> | automatizaçãoContas /variáveis | conta de automação | 1-128 | Não pode usar:<br> `<>*%&:\?.+/` <br><br>Não pode terminar com o espaço. |
> | automatizaçãoContas /observadores | conta de automação | 1-63 |  Alfanuméricos, sublinhados e hífenes.<br><br>Comece com a carta. |
> | automatizaçãoContas / webhooks | conta de automação | 1-128 | Não pode usar:<br> `<>*%&:\?.+/` <br><br>Não pode terminar com o espaço. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | batchAccounts | Região | 3-24 | Letras minúsculas e números. |
> | lotAccounts / aplicações | conta lote | 1-64 | Alfanuméricos, sublinhados e hífenes. |
> | lotAccounts / certificados | conta lote | 5-45 | Alfanuméricos, sublinhados e hífenes. |
> | lotAccounts / piscinas | conta lote | 1-64 | Alfanuméricos, sublinhados e hífenes. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | blockchainMembros | global | 2-20 | Letras minúsculas e números.<br><br>Comece com a carta minúscula. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | botServices | global | 2-64 |  Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. |
> | botServices / canais | serviço bot | 2-64 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. |
> | botServiços / Conexões | serviço bot | 2-64 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. |
> | enterpriseChannels | grupo de recursos | 2-64 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | Redis | global | 1-63 | Alfanuméricos e hífenes.<br><br>Comece e termine com alfanumérico. Hífenes consecutivos não são permitidos. |
> | Redis / firewallRules | Redis | 1-256 | Alfanumérico |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | profiles | grupo de recursos | 1-260 | Alfanuméricos e hífenes.<br><br>Comece e termine com alfanumérico. |
> | perfis / pontos finais | global | 1-50 | Alfanuméricos e hífenes.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | certificadoSOrdens | grupo de recursos | 3-30 | Alfanumérico. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | accounts | grupo de recursos | 2-64 | Alfanuméricos e hífenes.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | disponibilidadeConjuntos | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Termine com alfanumérico ou sublinhado. |
> | diskEncryptionSets | grupo de recursos | 1-80 | Alfanumérico e sublinha. |
> | discos | grupo de recursos | 1-80 | Alfanumérico e sublinha. |
> | galerias | grupo de recursos | 1-80 | Alfanumérico e períodos.<br><br>Comece e termine com alfanumérico. |
> | galerias / aplicações | galeria | 1-80 | Alfanuméricos, hífenes e períodos.<br><br>Comece e termine com alfanumérico. |
> | galerias / aplicações/versões | aplicação | Inteiro de 32 bits | Números e períodos. |
> | galerias / imagens | galeria | 1-80 | Alfanuméricos, hífenes e períodos.<br><br>Comece e termine com alfanumérico. |
> | galerias / imagens / versões | image | Inteiro de 32 bits | Números e períodos. |
> | imagens | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Termine com alfanumérico ou sublinhado. |
> | instantâneos | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Termine com alfanumérico ou sublinhado. |
> | máquinas virtuais | grupo de recursos | 1-15 (Janelas)<br>1-64 (Linux)<br><br>Veja a nota abaixo. | Não pode usar:<br> `\/""[]:|<>+=;,?*@&`<br><br>Não posso começar com sublinhado. Não pode terminar com período ou hífen. |
> | virtualMachineScaleSets | grupo de recursos | 1-15 (Janelas)<br>1-64 (Linux)<br><br>Veja a nota abaixo. | Não pode usar:<br> `\/""[]:|<>+=;,?*@&`<br><br>Não posso começar com sublinhado. Não pode terminar com período ou hífen. |

> [!NOTE]
> As máquinas virtuais Azure têm dois nomes distintos: nome de recurso e nome de anfitrião. Quando se cria uma máquina virtual no portal, o mesmo valor é utilizado para ambos os nomes. As restrições na tabela anterior destinam-se ao nome do anfitrião. O nome do recurso real pode ter até 64 carateres.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | grupos de contentores | grupo de recursos | 1-63 | Letras minúsculas, números e hífenes.<br><br>Não pode começar ou terminar com hífen. Hífenes consecutivos não são permitidos. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | registos | global | 5-50 | Alfanumérico. |
> | registos / buildTasks | registo | 5-50 | Alfanumérico. |
> | registos / construçãoTarefas/passos | construir tarefa | 5-50 | Alfanumérico. |
> | registos / replicações | registo | 5-50 | Alfanumérico. |
> | registos / scopeMaps | registo | 5-50 | Alfanuméricos, hífenes e sublinhados. |
> | registos / tarefas | registo | 5-50 | Alfanuméricos, hífenes e sublinhados. |
> | registos / fichas | registo | 5-50 | Alfanuméricos, hífenes e sublinhados. |
> | registos / webhooks | registo | 5-50 | Alfanumérico. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | clusters geridos | grupo de recursos | 1-63 | Alfanuméricos, sublinhados e hífenes.<br><br>Comece e termine com alfanumérico. |
> | openShiftManagedClusters | grupo de recursos | 1-30 | Alfanumérico. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | centros | grupo de recursos | 1-64 | Alfanumérico.<br><br>Comece com a carta.  |
> | hubs /autorizaçõesPolíticas | hub | 1-50 | Alfanumérico, sublinhae e períodos.<br><br>Comece e termine com alfanumérico. |
> | hubs / conectores | hub | 1-128 | Alfanumérico e sublinha.<br><br>Comece com a carta. |
> | hubs / conectores/mapeamentos | conector | 1-128 | Alfanumérico e sublinha.<br><br>Comece com a carta. |
> | hubs / interações | hub | 1-128 | Alfanumérico e sublinha.<br><br>Comece com a carta. |
> | hubs / kpi | hub | 1-512 | Alfanumérico e sublinha.<br><br>Comece com a carta. |
> | hubs / links | hub | 1-512 | Alfanumérico e sublinha.<br><br>Comece com a carta. |
> | centros / previsões | hub | 1-512 | Alfanumérico e sublinha.<br><br>Comece com a carta. |
> | hubs / perfis | hub | 1-128 | Alfanumérico e sublinha.<br><br>Comece com a carta. |
> | hubs / relacionamentoLinks | hub | 1-512 | Alfanumérico e sublinha.<br><br>Comece com a carta. |
> | centros / relacionamentos | hub | 1-512 | Alfanumérico e sublinha.<br><br>Comece com a carta. |
> | hubs / roleAssignments | hub | 1-128 | Alfanumérico e sublinha.<br><br>Comece com a carta. |
> | hubs / vistas | hub | 1-512 | Alfanumérico e sublinha.<br><br>Comece com a carta. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | associações | grupo de recursos | 1-180 | Não pode usar:<br>`%&\\?/`<br><br>Não pode terminar com período ou espaço. |
> | recursosFornecedores | grupo de recursos | 3-64 | Não pode usar:<br>`%&\\?/`<br><br>Não pode terminar com período ou espaço. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | empregos | grupo de recursos | 3-24 | Alfanuméricos, hífenes, sublinhados e períodos. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | espaços de trabalho | grupo de recursos | 3-30 | Alfanuméricos, sublinhados e hífenes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | fábricas | global | 3-63 | Alfanuméricos e hífenes.<br><br>Comece e termine com alfanumérico. |
> | fábricas / fluxos de dados | fábrica | 1-260 | Não pode usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / conjuntos de dados | fábrica | 1-260 | Não pode usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / integraçãoTempos | fábrica | 3-63 | Alfanuméricos e hífenes.<br><br>Comece e termine com alfanumérico. |
> | fábricas / serviços ligados | fábrica | 1-260 | Não pode usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / oleodutos | fábrica | 1-260 | Não pode usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / gatilhos | fábrica | 1-260 | Não pode usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / gatilhos / rerunTriggers | gatilho | 1-260 | Não pode usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Letras minúsculas e números. |
> | contas / computePolicies | conta | 3-60 | Alfanuméricos, hífenes e sublinhados. |
> | contas / dadosLakeStoreAccounts | conta | 3-24 | Letras minúsculas e números. |
> | contas / firewallRules | conta | 3-50 | Alfanuméricos, hífenes e sublinhados. |
> | contas / armazenamentoContas | conta | 3-60 | Alfanuméricos, hífenes e sublinhados. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Letras minúsculas e números. |
> | contas / firewallRules | conta | 3-50 | Alfanuméricos, hífenes e sublinhados. |
> | contas / virtualNetworkRules | conta | 3-50 | Alfanuméricos, hífenes e sublinhados. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | services | grupo de recursos | 2-62 | Alfanuméricos, hífenes, períodos e sublinhados.<br><br>Comece com alfanumérico. |
> | serviços / projetos | serviço | 2-57 | Alfanuméricos, hífenes, períodos e sublinhados.<br><br>Comece com alfanumérico. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores | global | 3-63 | Letras minúsculas, hífenes e números.<br><br>Não pode começar ou terminar com hífen. |
> | servidores / bases de dados | servidores | 1-63 | Alfanuméricos e hífenes. |
> | servidores / firewallRules | servidores | 1-128 | Alfanuméricos, hífenes e sublinhados. |
> | servidores / virtualNetworkRules | servidores | 1-128 | Alfanuméricos e hífenes. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores | global | 3-63 | Letras minúsculas, hífenes e números.<br><br>Não pode começar ou terminar com hífen. |
> | servidores / bases de dados | servidores | 1-63 | Alfanuméricos e hífenes. |
> | servidores / firewallRules | servidores | 1-128 | Alfanuméricos, hífenes e sublinhados. |
> | servidores / virtualNetworkRules | servidores | 1-128 | Alfanuméricos e hífenes. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores | global | 3-63 | Letras minúsculas, hífenes e números.<br><br>Não pode começar ou terminar com hífen. |
> | servidores / bases de dados | servidores | 1-63 | Alfanuméricos e hífenes. |
> | servidores / firewallRules | servidores | 1-128 | Alfanuméricos, hífenes e sublinhados. |
> | servidores / virtualNetworkRules | servidores | 1-128 | Alfanuméricos e hífenes. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | IotHubs | global | 3-50 | Alfanuméricos e hífenes.<br><br>Não pode terminar com hífen. |
> | IotHubs / certificados | Hub IoT | 1-64 | Alfanuméricos, hífenes, períodos e sublinhados. |
> | IotHubs / eventoHubEndpoints / ConsumerGroups | eventoPontos HubEndpoints | 1-50 | Alfanuméricos, hífenes, períodos e sublinhados. |
> | serviços de provisionamento | grupo de recursos | 3-64 | Alfanuméricos e hífenes.<br><br>Termine com alfanumérico. |
> | provisionamentoServiços / certificados | serviços de provisionamento | 1-64 | Alfanuméricos, hífenes, períodos e sublinhados. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | laboratórios | grupo de recursos | 1-50 | Alfanuméricos, sublinhados e hífenes. |
> | laboratórios / imagens personalizadas | laboratório | 1-80 | Alfanuméricos, sublinhados, hífenes e parênteses. |
> | laboratórios / fórmulas | laboratório | 1-80 | Alfanuméricos, sublinhados, hífenes e parênteses. |
> | laboratórios / máquinas virtuais | laboratório | 1-15 (Janelas)<br>1-64 (Linux) | Alfanuméricos e hífenes.<br><br>Comece e termine com alfanumérico. Não podem ser todos os números. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | databaseAccounts | global | 3-31 | Letras minúsculas, números e hífenes.<br><br>Comece com a letra ou número minúsculo. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | domínios | grupo de recursos | 3-50 | Alfanuméricos e hífenes. |
> | domínios / tópicos | domínio | 3-50 | Alfanuméricos e hífenes. |
> | eventoSSubscrições | grupo de recursos | 3-64 | Alfanuméricos e hífenes. |
> | tópicos | grupo de recursos | 3-50 | Alfanuméricos e hífenes. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | grupo de recursos | 6-50 | Alfanuméricos e hífenes.<br><br>Comece com a carta. Termine com carta ou número. |
> | espaços de nome | global | 6-50 | Alfanuméricos e hífenes.<br><br>Comece com a carta. Termine com carta ou número. |
> | espaços de nome / AutorizaçõesRegras | espaço de nomes | 1-50 | Alfanuméricos, períodos, hífenes e sublinhados.<br><br>Comece e termine com a letra ou o número. |
> | espaços de nome / desastreRecoveryConfigs | espaço de nomes | 1-50 | Alfanuméricos, períodos, hífenes e sublinhados.<br><br>Comece e termine com a letra ou o número. |
> | espaços de nomes / centros de eventos | espaço de nomes | 1-50 | Alfanuméricos, períodos, hífenes e sublinhados.<br><br>Comece e termine com a letra ou o número. |
> | espaços de nome / eventhubs / autorizaçõesRegras | centro de eventos | 1-50 | Alfanuméricos, períodos, hífenes e sublinhados.<br><br>Comece e termine com a letra ou o número. |
> | espaços de nome / eventhubs / grupos de consumidores | centro de eventos | 1-50 | Alfanuméricos, períodos, hífenes e sublinhados.<br><br>Comece e termine com a letra ou o número. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | global | 3-59 | Alfanuméricos e hífenes<br><br>Comece e termine com a letra ou o número. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | empregos | grupo de recursos | 2-64 | Alfanuméricos e hífenes.<br><br>Comece com a carta. |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | IoTApps | global | 2-63 | Letras minúsculas, números e hífenes.<br><br>Comece com a letra ou número minúsculo. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | cofres | global | 3-24 | Alfanuméricos e hífenes.<br><br>Comece com a carta. Termine com carta ou dígito. Não pode conter hífenes consecutivos. |
> | cofres / segredos | Cofre | 1-127 | Alfanuméricos e hífenes. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | global | 4-22 | Letras minúsculas e números.<br><br>Comece com a carta. |
> | /clusters / bases de dados | aglomerado | 1-260 | Alfanuméricos, hífenes, espaços e períodos. |
> | /clusters / bases de dados / dataConexções | base de dados | 1-40 | Alfanuméricos, hífenes, espaços e períodos. |
> | /clusters / bases de dados / eventhubconnections | base de dados | 1-40 | Alfanuméricos, hífenes, espaços e períodos. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | integraçõesContas | grupo de recursos | 1-80 | Alfanuméricos, hífenes, sublinhados, períodos e parênteses. |
> | integraçõesContas /conjuntos | conta de integração | 1-80 | Alfanuméricos, hífenes, sublinhados, períodos e parênteses. |
> | integraçõesContas / lotconfigurações | conta de integração | 1-20 | Alfanumérico. |
> | integraçõesContas / certificados | conta de integração | 1-80 | Alfanuméricos, hífenes, sublinhados, períodos e parênteses. |
> | integraçõesContas / mapas | conta de integração | 1-80 | Alfanuméricos, hífenes, sublinhados, períodos e parênteses. |
> | integraçõesContas /parceiros | conta de integração | 1-80 | Alfanuméricos, hífenes, sublinhados, períodos e parênteses. |
> | integraçõesContas / rosettanetprocessconfiguras | conta de integração | 1-80 | Alfanuméricos, hífenes, sublinhados, períodos e parênteses. |
> | integraçõesContas / schemas | conta de integração | 1-80 | Alfanuméricos, hífenes, sublinhados, períodos e parênteses. |
> | integraçõesContas / sessões | conta de integração | 1-80 | Alfanuméricos, hífenes, sublinhados, períodos e parênteses. |
> | integraçãoServiçosAmbientes | grupo de recursos | 1-80 | Alfanuméricos, hífenes, períodos e sublinhados. |
> | integraçãoServiceEnvironments / managedApis | ambiente de serviço de integração | 1-80 | Alfanuméricos, hífenes, períodos e sublinhados. |
> | fluxos de trabalho | grupo de recursos | 1-80 | Alfanuméricos, hífenes, sublinhados, períodos e parênteses. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | compromissoPlanos | grupo de recursos | 1-260 | Não pode usar:<br>`<>*%&:?+/\\`<br><br>Não pode terminar com um espaço. |
> | webServices | grupo de recursos | 1-260 | Não pode usar:<br>`<>*%&:?+/\\`<br><br>Não pode terminar com um espaço. |
> | espaços de trabalho | grupo de recursos | 1-260 | Não pode usar:<br>`<>*%&:?+/\\`<br><br>Não pode terminar com um espaço. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | espaços de trabalho | grupo de recursos | 3-33 | Alfanuméricos e hífenes. |
> | espaços de trabalho / computação | área de trabalho | 2-16 | Alfanuméricos e hífenes. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | userAssignedIdentities | grupo de recursos | 3-128 | Alfanuméricos, hífenes e sublinha<br><br>Comece com a carta ou número. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | accounts | grupo de recursos | 1-98 (para nome de grupo de recursos e nome de conta) | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | serviços de media | grupo de recursos | 3-24 | Letras minúsculas e números. |
> | mediaservices / liveEvents | Serviço de mídia | 1-32 | Alfanuméricos e hífenes.<br><br>Comece com alfanumérico. |
> | mediaservices / liveEvents / liveOutputs | Evento ao vivo | 1-256 | Alfanuméricos e hífenes.<br><br>Comece com alfanumérico. |
> | mediaservices / streamingPontos de finalidade | Serviço de mídia | 1-24 | Alfanuméricos e hífenes.<br><br>Comece com alfanumérico. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | applicationGateways | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | applicationSecurityGroups | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | azureFirewalls | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Termine com alfanumérico ou sublinhado. |
> | bastionHosts | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | conexões | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | dnsZones | grupo de recursos | 1-63 caracteres<br><br>2 a 34 rótulos<br><br>Cada etiqueta é um conjunto de caracteres separados por um período. Por exemplo, **contoso.com** tem 2 etiquetas. | Cada etiqueta pode conter alfanuméricos, sublinhados e hífenes.<br><br>Cada etiqueta é separada por um período. |
> | expressRouteCircuits | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | firewallPolíticas | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | firewallPolicies / ruleGroups | Política de firewall | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | frontDoors | global | 5-64 | Alfanuméricos e hífenes.<br><br>Comece e termine com alfanumérico. |
> | loadBalancers | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | loadBalancers / inboundNatRules | equilibrador de carga | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | localNetworkGateways | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkInterfaces | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkSecurityGroups | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkSecurityGroups / securityRules | grupo de segurança de rede | 1-80 |  Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkWatchers | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | privateDnsZones | grupo de recursos | 1-63 caracteres<br><br>2 a 34 rótulos<br><br>Cada etiqueta é um conjunto de caracteres separados por um período. Por exemplo, **contoso.com** tem 2 etiquetas. | Cada etiqueta pode conter alfanuméricos, sublinhados e hífenes.<br><br>Cada etiqueta é separada por um período. |
> | privateDnsZones / virtualNetworkLinks | zona privada dNS | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | publicIPAddresses | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | publicIPPrefixes | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeFilters | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | RotaFiltros / rotaFilterRules | filtro de rota | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeTables | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | RouteTables / rotas | tabela de rotas | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | serviceEndpointPolicies | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | perfis de gestor de tráfego | global | 1-63 | Alfanuméricos, hífenes e períodos.<br><br>Comece e termine com alfanumérico. |
> | virtualNetworkGateways | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | redes virtuais | grupo de recursos | 2-64 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | redes virtuais / subredes | rede virtual | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | redes virtuais / virtualNetworkPeerings | rede virtual | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | virtualWans | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | vpnGateways | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | vpnGateways / vpnConnections | Gateway de VPN | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | vpnSites | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, períodos e hífenes.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | espaços de nome | global | 6-50 | Alfanuméricos e hífenes<br><br>Comece e termine com alfanumérico. |
> | espaços de nome / AutorizaçõesRegras | espaço de nomes | 1-256 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Começa alfanumérico. |
> | espaços de nome / notificationHubs | espaço de nomes | 1-260 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Começa alfanumérico. |
> | espaços de nome / notificaçõesHubs / AutorizaçõesRegras | centro de notificação | 1-256 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Começa alfanumérico. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | grupo de recursos | 4-63 | Alfanuméricos e hífenes.<br><br>Comece e termine com alfanumérico. |
> | espaços de trabalho | grupo de recursos | 4-63 | Alfanuméricos e hífenes.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | dashboards | grupo de recursos | 3-160 | Alfanuméricos e hífenes.<br><br>Para utilizar caracteres restritos, adicione uma etiqueta com o **nome de título oculto** com o nome do painel de instrumentos que pretende utilizar. O portal exibe esse nome ao mostrar o painel de instrumentos. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | workspaceCollections | . | 3-63 | Alfanuméricos e hífenes.<br><br>Não posso começar com hífen. Não se pode usar hífenes consecutivos. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | capacidades | . | 3-63 | Letras minúsculas ou números<br><br>Comece com a carta minúscula. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | cofres | grupo de recursos | 2-50 | Alfanuméricos e hífenes.<br><br>Comece com a carta. |
> | cofres / backupPolicies | vault | 3-150 | Alfanuméricos e hífenes.<br><br>Comece com a carta. Não pode terminar com hífen. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | espaços de nome | global | 6-50 | Alfanuméricos e hífenes.<br><br>Comece com uma letra. Termine com uma carta ou número. |
> | espaços de nome / AutorizaçõesRegras | espaço de nomes | 1-50 |  Alfanuméricos, períodos, hífenes e sublinhados.<br><br>Comece e termine com alfanumérico. |
> | espaços de nome / Ligações Híbridas | espaço de nomes | 1-260 | Alfanuméricos, períodos, hífens, sublinhados e cortes.<br><br>Comece e termine com alfanumérico. |
> | espaços de nome / Ligações Híbridas/autorizaçõesRegras | conexão híbrida | 1-50 | Alfanuméricos, períodos, hífenes e sublinhados.<br><br>Comece e termine com alfanumérico. |
> | espaços de nome / WcfRelays | espaço de nomes | 1-260 | Alfanuméricos, períodos, hífens, sublinhados e cortes.<br><br>Comece e termine com alfanumérico. |
> | espaços de nome / WcfRelays / regras de autorização | Relé WCF | 1-50 | Alfanuméricos, períodos, hífenes e sublinhados.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | implementações | grupo de recursos | 1-64 | Alfanuméricos, sublinhados, parênteses, hífens e períodos. |
> | grupos de recursos | subscrição | 1-90 | Alfanuméricos, sublinhados, parênteses, hífens, períodos e caracteres unicódigo que correspondem à [documentação regex](/rest/api/resources/resourcegroups/createorupdate).<br><br>Não pode terminar com o período. |
> | tagNamesNomes | resource | 1-512 | Não pode usar:<br>`<>%&\?/` |
> | tagNamesNames / tagValues | Nome de etiqueta | 1-256 | Todos os personagens. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | espaços de nome | global | 6-50 | Alfanuméricos e hífenes.<br><br>Comece com uma letra. Termine com uma carta ou número.<br><br>Para mais informações, consulte [Criar espaço para nomes.](/rest/api/servicebus/create-namespace) |
> | espaços de nome / AutorizaçõesRegras | espaço de nomes | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com alfénumérico. |
> | espaços de nome / desastreRecoveryConfigs | global | 6-50 | Alfanuméricos e hífenes.<br><br>Comece com a carta. Termine com alfanumérico. |
> | espaços de nome / migraçãoConfigurações | espaço de nomes |  | Deve ser sempre **$default.** |
> | espaços de nome / filas | espaço de nomes | 1-260 | Alfanuméricos, períodos, hífens, sublinhados e cortes.<br><br>Comece e termine com alfanumérico. |
> | espaços de nome / filas / autorizaçõesRegras | fila | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com alfénumérico. |
> | espaços de nome / tópicos | espaço de nomes | 1-260 | Alfanuméricos, períodos, hífens, sublinhados e cortes.<br><br>Comece e termine com alfanumérico. |
> | espaços de nome / tópicos / autorizaçõesRegras | tópico | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com alfénumérico. |
> | espaços de nome / tópicos / subscrições | tópico | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com alfénumérico. |
> | espaços de nome / tópicos / subscrições / regras | subscrição | 1-50 | Alfanuméricos, períodos, hífens e sublinhados.<br><br>Comece e termine com alfénumérico. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | . | 4-23 | Letras minúsculas, números e hífenes.<br><br>Comece com a carta minúscula. Termine com letra ou número minúsculo. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | sinalR | global | 3-63 | Alfanuméricos e hífenes.<br><br>Comece com a carta. Termine com carta ou número.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | managedInstances | global | 1-63 | Letras minúsculas, números e hífenes.<br><br>Não pode começar ou terminar com hífen. |
> | servidores | global | 1-63 | Letras minúsculas, números e hífenes.<br><br>Não pode começar ou terminar com hífen. |
> | servidores / bases de dados | servidor | 1-128 | Não pode usar:<br>`<>*%&:\/?`<br><br>Não pode terminar com período ou espaço. |
> | servidores / bases de dados / syncGroups | base de dados | 1-150 | Alfanuméricos, hífenes e sublinhados. |
> | servidores / elásticosPools | servidor | 1-128 | Não pode usar:<br>`<>*%&:\/?`<br><br>Não pode terminar com período ou espaço. |
> | servidores / failoverGroups | global | 1-63 | Letras minúsculas, números e hífenes.<br><br>Não pode começar ou terminar com hífen. |
> | servidores / firewallRules | servidor | 1-128 | Não pode usar:<br>`<>*%&:;\/?`<br><br>Não pode terminar com o período. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | storageAccounts | global | 3-24 | Letras minúsculas e números. |
> | armazenamentoContas / blobServices | conta de armazenamento |  | Deve ser `default`. |
> | armazenamentoContas / blobServices / contentores | conta de armazenamento | 3-63 | Letras minúsculas, números e hífenes.<br><br>Comece com a letra ou número minúsculo. Não se pode usar hífenes consecutivos. |
> | armazenamentoContas / ficheirosServiços | conta de armazenamento |  | Deve ser `default`. |
> | armazenamentoContas / ficheirosServiços / ações | conta de armazenamento | 3-63 | Letras minúsculas, números e hífenes.<br><br>Não pode começar ou terminar com hífen. Não se pode usar hífenes consecutivos. |
> | armazenamentoContas / gestãoPolíticas | conta de armazenamento |  | Deve ser `default`. |
> | blob | contentor | 1-1024 | Quaisquer caracteres URL, sensíveis a casos |
> | fila | conta de armazenamento | 3-63 | Letras minúsculas, números e hífenes.<br><br>Não pode começar ou terminar com hífen. Não se pode usar hífenes consecutivos. |
> | table | conta de armazenamento | 3-63 | Alfanumérico.<br><br>Comece com a carta. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | storageSyncServices | grupo de recursos | 1-260 | Alfanuméricos, espaços, períodos, hífenes e sublinhados.<br><br>Não pode terminar com período ou espaço. |
> | armazenamentoSyncServices / syncGroups | serviço de sincronização de armazenamento | 1-260 | Alfanuméricos, espaços, períodos, hífenes e sublinhados.<br><br>Não pode terminar com período ou espaço. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | gestores | grupo de recursos | 2-50 | Alfanuméricos e hífenes.<br><br>Comece com a carta. Termine com alfanumérico. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | streamingjobs | grupo de recursos | 3-63 | Alfanuméricos, hífenes e sublinhados. |
> | streamingjobs / funções | trabalho de streaming | 3-63 | Alfanuméricos, hífenes e sublinhados. |
> | streamingjobs / inputs | trabalho de streaming | 3-63 | Alfanuméricos, hífenes e sublinhados. |
> | streamingjobs / outputs | trabalho de streaming | 3-63 | Alfanuméricos, hífenes e sublinhados. |
> | streamingjobs / transformações | trabalho de streaming | 3-63 | Alfanuméricos, hífenes e sublinhados. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | ambientes | grupo de recursos | 1-90 | Não pode usar:<br>`'<>%&:\?/#` |
> | ambientes / accessPolicies | environment | 1-90 | Não pode usar:<br> `'<>%&:\?/#` |
> | ambientes / eventoSFontes | environment | 1-90 | Não pode usar:<br>`'<>%&:\?/#` |
> | ambientes / referênciaDataSets | environment | 3-63 | Alfanumérico |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores fazendas | grupo de recursos | 1-40 | Alfanuméricos e hífenes. |
> | sites | global | 2-60 | Contém alfanuméricos e hífenes.<br><br>Não pode começar ou terminar com hífen. |
> | sites / slots | site | 2-59 | Alfanuméricos e hífenes. |

## <a name="next-steps"></a>Passos seguintes

Para recomendações sobre como nomear recursos, consulte Convenções de [nomeação e marcação recomendadas.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)
