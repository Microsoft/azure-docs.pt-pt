---
title: Restrições de nomenclatura de recursos
description: Mostra as regras e restrições para nomear recursos do Azure.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 7b6a9e16ba28a2a0e5e4e181dc5650d2110eab88
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153167"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Regras de nomenclatura e restrições para recursos do Azure

Este artigo resume as regras de nomenclatura e as restrições dos recursos do Azure. Para recomendações sobre como nomear recursos, consulte Convenções de [nomeação e marcação recomendadas.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

Os nomes de recursos não diferenciam maiúsculas de minúsculas, a menos que sejam especificamente indicados na coluna caracteres válidos.

Nas tabelas a seguir, o termo alfanumérico refere-se a:

* **a** através **z** (letras minúsculas)
* **A** a **Z** (letras maiúsculas)
* **0** a **9** (números)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores | grupo de recursos | 3-63 | Letras minúsculas e números.<br><br>Comece com uma letra minúscula. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | serviço | global | 1-50 | Alfanuméricos.<br><br>Comece com a letra. |
> | serviço/APIs | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/APIs/problemas | api | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/APIs/problemas/anexos | lo | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/APIs/problemas/comentários | lo | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/APIs/operações | api | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/APIs/operações/marcas | operation | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/APIs/versões | api | 1-80 | Alfanuméricos, sublinhados e hifens.<br><br>Comece e termine com caracteres alfanuméricos ou sublinhados. |
> | serviços/APIs/esquemas | api | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/APIs/tagDescriptions | api | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/APIs/marcas | api | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/API – conjuntos de versão | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/authorizationServers | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/back-ends | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/certificados | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/diagnóstico | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/grupos | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/grupos/usuários | grupo | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/identityProviders | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/agentes | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/notificações | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/notificações/recipientEmails | notificação | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/openidConnectProviders | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/políticas | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviços/produtos | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/produtos/APIs | produto | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/produtos/grupos | produto | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/produtos/marcas | produto | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/Propriedades | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/assinaturas | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/marcas | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/modelos | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |
> | serviço/usuários | serviço | 1-256 | Não é possível usar:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | configuraçãoLojas | grupo de recursos | 5-50 | Alfanuméricos, sublinhados e hifens. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | fechaduras | escopo da atribuição | 1-90 | Alfanuméricos, pontos, sublinhados, hifens e parênteses.<br><br>Não pode terminar no período. |
> | atribuições políticas | escopo da atribuição | 1-128 nome para exibição<br><br>1-260 nome do recurso | O nome para exibição pode conter qualquer caractere.<br><br>O nome do recurso não pode incluir `%` e não pode terminar com período ou espaço. |
> | PolicyDefinitions | escopo da definição | 1-128 nome para exibição<br><br>1-260 nome do recurso | O nome para exibição pode conter qualquer caractere.<br><br>O nome do recurso não pode incluir `%` e não pode terminar com período ou espaço. |
> | policySetDefinitions | escopo da definição | 1-128 nome para exibição<br><br>1-260 nome do recurso | O nome para exibição pode conter qualquer caractere.<br><br>O nome do recurso não pode incluir `%` e não pode terminar com período ou espaço.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | automatizaçõesContas | grupo de recursos | 6-50 | Alfanuméricos e hifens.<br><br>Comece com letra e termine com alfanumérico. |
> | automationAccounts/certificados | conta de automação | 1-128 | Não é possível usar:<br> `<>*%&:\?.+/` <br><br>Não é possível terminar com espaço.  |
> | automationAccounts/conexões | conta de automação | 1-128 | Não é possível usar:<br> `<>*%&:\?.+/` <br><br>Não é possível terminar com espaço. |
> | automationAccounts/credenciais | conta de automação | 1-128 | Não é possível usar:<br> `<>*%&:\?.+/` <br><br>Não é possível terminar com espaço. |
> | automaçãoContas / livros de execução | conta de automação | 1-63 | Alfanuméricos, sublinhados e hifens.<br><br>Comece com a letra.  |
> | automationAccounts/agendas | conta de automação | 1-128 | Não é possível usar:<br> `<>*%&:\?.+/` <br><br>Não é possível terminar com espaço. |
> | automationAccounts/variáveis | conta de automação | 1-128 | Não é possível usar:<br> `<>*%&:\?.+/` <br><br>Não é possível terminar com espaço. |
> | automationAccounts/inspetores | conta de automação | 1-63 |  Alfanuméricos, sublinhados e hifens.<br><br>Comece com a letra. |
> | automatizaçãoContas / webhooks | conta de automação | 1-128 | Não é possível usar:<br> `<>*%&:\?.+/` <br><br>Não é possível terminar com espaço. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | batchAccounts | Região | 3-24 | Letras minúsculas e números. |
> | batchAccounts/aplicativos | conta do lote | 1-64 | Alfanuméricos, sublinhados e hifens. |
> | batchAccounts/certificados | conta do lote | 5-45 | Alfanuméricos, sublinhados e hifens. |
> | batchAccounts/pools | conta do lote | 1-64 | Alfanuméricos, sublinhados e hifens. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | blockchainMembros | global | 2-20 | Letras minúsculas e números.<br><br>Comece com uma letra minúscula. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | botServices | global | 2-64 |  Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. |
> | botServices / canais | serviço de bot | 2-64 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. |
> | botServices/conexões | serviço de bot | 2-64 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. |
> | enterpriseChannels | grupo de recursos | 2-64 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | Redis | global | 1-63 | Alfanuméricos e hifens.<br><br>Comece e termine com alfanumérico. Hifens consecutivos não são permitidos. |
> | Redis/firewallRules | Redis | 1-256 | Alfanuméricos |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | profiles | grupo de recursos | 1-260 | Alfanuméricos e hifens.<br><br>Comece e termine com alfanumérico. |
> | perfis / pontos finais | global | 1-50 | Alfanuméricos e hifens.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | certificadoSOrdens | grupo de recursos | 3-30 | Alfanuméricos. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | accounts | grupo de recursos | 2-64 | Alfanuméricos e hifens.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | disponibilidadeConjuntos | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Terminar com alfanumérico ou sublinhado. |
> | diskEncryptionSets | grupo de recursos | 1-80 | Caracteres alfanuméricos e sublinhados. |
> | discos | grupo de recursos | 1-80 | Caracteres alfanuméricos e sublinhados. |
> | galerias | grupo de recursos | 1-80 | Alfanuméricos e pontos.<br><br>Comece e termine com alfanumérico. |
> | galerias / aplicações | galeria | 1-80 | Alfanuméricos, hifens e pontos.<br><br>Comece e termine com alfanumérico. |
> | galerias/aplicativos/versões | aplicação | Inteiro de 32 bits | Números e pontos. |
> | galerias / imagens | galeria | 1-80 | Alfanuméricos, hifens e pontos.<br><br>Comece e termine com alfanumérico. |
> | galerias / imagens / versões | image | Inteiro de 32 bits | Números e pontos. |
> | imagens | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Terminar com alfanumérico ou sublinhado. |
> | instantâneos | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Terminar com alfanumérico ou sublinhado. |
> | máquinas virtuais | grupo de recursos | 1-15 (Windows)<br>1-64 (Linux)<br><br>Consulte a observação abaixo. | Não é possível usar:<br> `\/""[]:|<>+=;,?*@&`<br><br>Não é possível começar com sublinhado. Não é possível terminar com ponto ou hífen. |
> | virtualMachineScaleSets | grupo de recursos | 1-15 (Windows)<br>1-64 (Linux)<br><br>Consulte a observação abaixo. | Não é possível usar:<br> `\/""[]:|<>+=;,?*@&`<br><br>Não é possível começar com sublinhado. Não é possível terminar com ponto ou hífen. |

> [!NOTE]
> As máquinas virtuais do Azure têm dois nomes distintos: nome do recurso e nome do host. Quando você cria uma máquina virtual no portal, o mesmo valor é usado para ambos os nomes. As restrições na tabela anterior são para o nome do host. O nome do recurso real pode ter até 64 carateres.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | grupos de contentores | grupo de recursos | 1-63 | Letras minúsculas, números e hifens.<br><br>Não é possível iniciar ou terminar com hífen. Hífenes consecutivos não são permitidos. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | registos | global | 5-50 | Alfanuméricos. |
> | registos / buildTasks | registo | 5-50 | Alfanuméricos. |
> | registros/buildTasks/etapas | Compilar tarefa | 5-50 | Alfanuméricos. |
> | registos / replicações | registo | 5-50 | Alfanuméricos. |
> | registos / scopeMaps | registo | 5-50 | Alfanuméricos, hifens e sublinhados. |
> | registos / tarefas | registo | 5-50 | Alfanuméricos, hifens e sublinhados. |
> | registos / fichas | registo | 5-50 | Alfanuméricos, hifens e sublinhados. |
> | registos / webhooks | registo | 5-50 | Alfanuméricos. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | clusters geridos | grupo de recursos | 1-63 | Alfanuméricos, sublinhados e hifens.<br><br>Comece e termine com alfanumérico. |
> | openShiftManagedClusters | grupo de recursos | 1-30 | Alfanuméricos. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | centros | grupo de recursos | 1-64 | Alfanuméricos.<br><br>Comece com a letra.  |
> | hubs/authorizationPolicies | hub | 1-50 | Alfanuméricos, sublinhados e pontos.<br><br>Comece e termine com alfanumérico. |
> | hubs/conectores | hub | 1-128 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com a letra. |
> | hubs/conectores/mapeamentos | conector | 1-128 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com a letra. |
> | hubs/interações | hub | 1-128 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com a letra. |
> | hubs/KPI | hub | 1-512 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com a letra. |
> | hubs/links | hub | 1-512 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com a letra. |
> | hubs/previsões | hub | 1-512 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com a letra. |
> | hubs/perfis | hub | 1-128 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com a letra. |
> | hubs/relationshipLinks | hub | 1-512 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com a letra. |
> | hubs/relações | hub | 1-512 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com a letra. |
> | hubs/roleAssignments | hub | 1-128 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com a letra. |
> | hubs/exibições | hub | 1-512 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com a letra. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | associações | grupo de recursos | 1-180 | Não é possível usar:<br>`%&\\?/`<br><br>Não é possível terminar com período ou espaço. |
> | recursosFornecedores | grupo de recursos | 3-64 | Não é possível usar:<br>`%&\\?/`<br><br>Não é possível terminar com período ou espaço. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | Empregos | grupo de recursos | 3-24 | Alfanuméricos, hifens, sublinhados e pontos. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | espaços de trabalho | grupo de recursos | 3-30 | Alfanuméricos, sublinhados e hifens |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | fábricas | global | 3-63 | Alfanuméricos e hifens.<br><br>Comece e termine com alfanumérico. |
> | fábricas/fluxos de fluxo de os | padrões | 1-260 | Não é possível usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas/conjuntos de | padrões | 1-260 | Não é possível usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / integraçãoTempos | padrões | 3-63 | Alfanuméricos e hifens.<br><br>Comece e termine com alfanumérico. |
> | fábricas/linksservices | padrões | 1-260 | Não é possível usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas/pipelines | padrões | 1-260 | Não é possível usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas/gatilhos | padrões | 1-260 | Não é possível usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas/gatilhos/rerunTriggers | gatilho | 1-260 | Não é possível usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Letras minúsculas e números. |
> | contas/computePolicies | conta | 3-60 | Alfanuméricos, hifens e sublinhados. |
> | contas / dadosLakeStoreAccounts | conta | 3-24 | Letras minúsculas e números. |
> | contas / firewallRules | conta | 3-50 | Alfanuméricos, hifens e sublinhados. |
> | contas / armazenamentoContas | conta | 3-60 | Alfanuméricos, hifens e sublinhados. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Letras minúsculas e números. |
> | contas / firewallRules | conta | 3-50 | Alfanuméricos, hifens e sublinhados. |
> | contas/virtualNetworkRules | conta | 3-50 | Alfanuméricos, hifens e sublinhados. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | services | grupo de recursos | 2-62 | Alfanuméricos, hifens, pontos e sublinhados.<br><br>Comece com alfanumérico. |
> | serviços / projetos | serviço | 2-57 | Alfanuméricos, hifens, pontos e sublinhados.<br><br>Comece com alfanumérico. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores | global | 3-63 | Letras minúsculas, hifens e números.<br><br>Não é possível iniciar ou terminar com hífen. |
> | servidores / bases de dados | servidores | 1-63 | Alfanuméricos e hifens. |
> | servidores / firewallRules | servidores | 1-128 | Alfanuméricos, hifens e sublinhados. |
> | servidores / virtualNetworkRules | servidores | 1-128 | Alfanuméricos e hifens. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores | global | 3-63 | Letras minúsculas, hifens e números.<br><br>Não é possível iniciar ou terminar com hífen. |
> | servidores / bases de dados | servidores | 1-63 | Alfanuméricos e hifens. |
> | servidores / firewallRules | servidores | 1-128 | Alfanuméricos, hifens e sublinhados. |
> | servidores / virtualNetworkRules | servidores | 1-128 | Alfanuméricos e hifens. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores | global | 3-63 | Letras minúsculas, hifens e números.<br><br>Não é possível iniciar ou terminar com hífen. |
> | servidores / bases de dados | servidores | 1-63 | Alfanuméricos e hifens. |
> | servidores / firewallRules | servidores | 1-128 | Alfanuméricos, hifens e sublinhados. |
> | servidores / virtualNetworkRules | servidores | 1-128 | Alfanuméricos e hifens. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | IotHubs | global | 3-50 | Alfanuméricos e hifens.<br><br>Não é possível terminar com hífen. |
> | IotHubs/certificados | Hub IoT | 1-64 | Alfanuméricos, hifens, pontos e sublinhados. |
> | IotHubs/eventHubEndpoints/ConsumerGroups | eventHubEndpoints | 1-50 | Alfanuméricos, hifens, pontos e sublinhados. |
> | serviços de provisionamento | grupo de recursos | 3-64 | Alfanuméricos e hifens.<br><br>Terminar com alfanumérico. |
> | provisioningServices/certificados | serviços de provisionamento | 1-64 | Alfanuméricos, hifens, pontos e sublinhados. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | laboratórios | grupo de recursos | 1-50 | Alfanuméricos, sublinhados e hifens. |
> | laboratórios/customimages | laboratórios | 1-80 | Alfanuméricos, sublinhados, hifens e parênteses. |
> | laboratórios/fórmulas | laboratórios | 1-80 | Alfanuméricos, sublinhados, hifens e parênteses. |
> | laboratórios / máquinas virtuais | laboratórios | 1-15 (Windows)<br>1-64 (Linux) | Alfanuméricos e hifens.<br><br>Comece e termine com alfanumérico. Não pode ser todos os números. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | databaseAccounts | global | 3-31 | Letras minúsculas, números e hifens.<br><br>Comece com letra minúscula ou número. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | domínios | grupo de recursos | 3-50 | Alfanuméricos e hifens. |
> | domínios / tópicos | domínio | 3-50 | Alfanuméricos e hifens. |
> | eventoSSubscrições | grupo de recursos | 3-64 | Alfanuméricos e hifens. |
> | tópicos | grupo de recursos | 3-50 | Alfanuméricos e hifens. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | grupo de recursos | 6-50 | Alfanuméricos e hifens.<br><br>Comece com a letra. Terminar com letra ou número. |
> | espaços de nome | global | 6-50 | Alfanuméricos e hifens.<br><br>Comece com a letra. Terminar com letra ou número. |
> | namespaces/AuthorizationRules | espaço de nomes | 1-50 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com letra ou número. |
> | namespaces/disasterRecoveryConfigs | espaço de nomes | 1-50 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com letra ou número. |
> | espaços de nomes / centros de eventos | espaço de nomes | 1-50 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com letra ou número. |
> | namespaces/Eventhubs/authorizationRules | Hub de eventos | 1-50 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com letra ou número. |
> | espaços de nome / eventhubs / grupos de consumidores | Hub de eventos | 1-50 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com letra ou número. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | global | 3-59 | Alfanuméricos e hifens<br><br>Comece e termine com letra ou número. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | Empregos | grupo de recursos | 2-64 | Alfanuméricos e hifens.<br><br>Comece com a letra. |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | IoTApps | global | 2-63 | Letras minúsculas, números e hifens.<br><br>Comece com letra minúscula ou número. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | cofres | global | 3-24 | Alfanuméricos e hifens.<br><br>Comece com a letra. Terminar com letra ou dígito. Não pode conter hifens consecutivos. |
> | cofres / segredos | Cofre | 1-127 | Alfanuméricos e hifens. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | global | 4-22 | Letras minúsculas e números.<br><br>Comece com a letra. |
> | /clusters/bancos de dados | aglomerado | 1-260 | Alfanuméricos, hifens, espaços e pontos. |
> | /clusters/bancos de dados/conexões | base de dados | 1-40 | Alfanuméricos, hifens, espaços e pontos. |
> | /clusters/bancos de dados/eventhubconnections | base de dados | 1-40 | Alfanuméricos, hifens, espaços e pontos. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | integraçõesContas | grupo de recursos | 1-80 | Alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts/assemblies | conta de integração | 1-80 | Alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts / batchConfigurations | conta de integração | 1-20 | Alfanuméricos. |
> | integrationAccounts/certificados | conta de integração | 1-80 | Alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts/Maps | conta de integração | 1-80 | Alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts/parceiros | conta de integração | 1-80 | Alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts / rosettanetprocessconfigurations | conta de integração | 1-80 | Alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts/esquemas | conta de integração | 1-80 | Alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts/sessões | conta de integração | 1-80 | Alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integraçãoServiçosAmbientes | grupo de recursos | 1-80 | Alfanuméricos, hifens, pontos e sublinhados. |
> | integraçãoServiceEnvironments / managedApis | ambiente do serviço de integração | 1-80 | Alfanuméricos, hifens, pontos e sublinhados. |
> | fluxos de trabalho | grupo de recursos | 1-80 | Alfanuméricos, hifens, sublinhados, pontos e parênteses. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | compromissoPlanos | grupo de recursos | 1-260 | Não é possível usar:<br>`<>*%&:?+/\\`<br><br>Não é possível terminar com um espaço. |
> | webServices | grupo de recursos | 1-260 | Não é possível usar:<br>`<>*%&:?+/\\`<br><br>Não é possível terminar com um espaço. |
> | espaços de trabalho | grupo de recursos | 1-260 | Não é possível usar:<br>`<>*%&:?+/\\`<br><br>Não é possível terminar com um espaço. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | espaços de trabalho | grupo de recursos | 3-33 | Alfanuméricos e hifens. |
> | espaços de trabalho / computação | área de trabalho | 2-16 | Alfanuméricos e hifens. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | userAssignedIdentities | grupo de recursos | 3-128 | Alfanuméricos, hifens e sublinhados<br><br>Comece com letra ou número. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | accounts | grupo de recursos | 1-98 (para nome do grupo de recursos e nome da conta) | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | serviços de media | grupo de recursos | 3-24 | Letras minúsculas e números. |
> | mediaservices / liveEvents | Serviço de mídia | 1-32 | Alfanuméricos e hifens.<br><br>Comece com alfanumérico. |
> | mediaservices / liveEvents / liveOutputs | Evento ao vivo | 1-256 | Alfanuméricos e hifens.<br><br>Comece com alfanumérico. |
> | mediaservices / streamingPontos de finalidade | Serviço de mídia | 1-24 | Alfanuméricos e hifens.<br><br>Comece com alfanumérico. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | applicationGateways | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | applicationSecurityGroups | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | azureFirewalls | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Terminar com alfanumérico ou sublinhado. |
> | bastionHosts | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | conexões | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | dnsZones | grupo de recursos | 1-63 caracteres<br><br>2 a 34 rótulos<br><br>Cada rótulo é um conjunto de caracteres separados por um ponto. Por exemplo, **contoso.com** tem 2 etiquetas. | Cada rótulo pode conter alfanuméricos, sublinhados e hifens.<br><br>Cada rótulo é separado por um ponto. |
> | expressRouteCircuits | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | firewallPolíticas | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | firewallPolicies/grupo | política de firewall | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | frontDoors | global | 5-64 | Alfanuméricos e hifens.<br><br>Comece e termine com alfanumérico. |
> | loadBalancers | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | Balanceadores de carga/inboundNatRules | equilibrador de carga | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | localNetworkGateways | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkInterfaces | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkSecurityGroups | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkSecurityGroups/securityRules | grupo de segurança de rede | 1-80 |  Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkWatchers | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | privateDnsZones | grupo de recursos | 1-63 caracteres<br><br>2 a 34 rótulos<br><br>Cada rótulo é um conjunto de caracteres separados por um ponto. Por exemplo, **contoso.com** tem 2 etiquetas. | Cada rótulo pode conter alfanuméricos, sublinhados e hifens.<br><br>Cada rótulo é separado por um ponto. |
> | privateDnsZones / virtualNetworkLinks | zona DNS privada | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | publicIPAddresses | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | publicIPPrefixes | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeFilters | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeFilters / routeFilterRules | filtro de rota | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeTables | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeTables/rotas | tabela de rotas | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | serviceEndpointPolicies | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | perfis de gestor de tráfego | global | 1-63 | Alfanuméricos, hifens e pontos.<br><br>Comece e termine com alfanumérico. |
> | virtualNetworkGateways | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | redes virtuais | grupo de recursos | 2-64 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | virtualnetworks/sub-redes | rede virtual | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | redes virtuais / virtualNetworkPeerings | rede virtual | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | virtualWans | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | vpnGateways | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | vpnGateways / vpnConnections | Gateway de VPN | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | vpnSites | grupo de recursos | 1-80 | Alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | espaços de nome | global | 6-50 | Alfanuméricos e hifens<br><br>Comece e termine com alfanumérico. |
> | namespaces/AuthorizationRules | espaço de nomes | 1-256 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Iniciar alfanumérico. |
> | espaços de nome / notificationHubs | espaço de nomes | 1-260 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Iniciar alfanumérico. |
> | namespaces/notificationHubs/AuthorizationRules | Hub de notificação | 1-256 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Iniciar alfanumérico. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | grupo de recursos | 4-63 | Alfanuméricos e hifens.<br><br>Comece e termine com alfanumérico. |
> | espaços de trabalho | grupo de recursos | 4-63 | Alfanuméricos e hifens.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | dashboards | grupo de recursos | 3-160 | Alfanuméricos e hifens.<br><br>Para utilizar caracteres restritos, adicione uma etiqueta com o **nome de título oculto** com o nome do painel de instrumentos que pretende utilizar. O portal exibe esse nome ao mostrar o painel de instrumentos. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | workspaceCollections | . | 3-63 | Alfanuméricos e hifens.<br><br>Não é possível começar com hífen. Não é possível usar hifens consecutivos. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | capacidades | . | 3-63 | Letras minúsculas ou números<br><br>Comece com uma letra minúscula. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | cofres | grupo de recursos | 2-50 | Alfanuméricos e hifens.<br><br>Comece com a letra. |
> | cofres/backupPolicies | vault | 3-150 | Alfanuméricos e hifens.<br><br>Comece com a letra. Não é possível terminar com hífen. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | espaços de nome | global | 6-50 | Alfanuméricos e hifens.<br><br>Comece com uma letra. Terminar com uma letra ou número. |
> | namespaces/AuthorizationRules | espaço de nomes | 1-50 |  Alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com alfanumérico. |
> | namespaces/HybridConnections | espaço de nomes | 1-260 | Alfanuméricos, pontos, hifens, sublinhados e barras.<br><br>Comece e termine com alfanumérico. |
> | namespaces/HybridConnections/authorizationRules | conexão híbrida | 1-50 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com alfanumérico. |
> | namespaces/WcfRelays | espaço de nomes | 1-260 | Alfanuméricos, pontos, hifens, sublinhados e barras.<br><br>Comece e termine com alfanumérico. |
> | namespaces/WcfRelays/authorizationRules | Retransmissão do WCF | 1-50 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | implementações | grupo de recursos | 1-64 | Alfanuméricos, sublinhados, parênteses, hifens e pontos. |
> | resourcegroups | subscrição | 1-90 | Alfanuméricos, sublinhados, parênteses, hífens, períodos e caracteres unicódigo que correspondem à [documentação regex](/rest/api/resources/resourcegroups/createorupdate).<br><br>Não é possível terminar com período. |
> | tagNames | resource | 1-512 | Não é possível usar:<br>`<>%&\?/` |
> | tagNames/tagValues | nome da marca | 1-256 | Todos os caracteres. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | espaços de nome | global | 6-50 | Alfanuméricos e hifens.<br><br>Comece com uma letra. Terminar com uma letra ou número.<br><br>Para mais informações, consulte [Criar espaço para nomes.](/rest/api/servicebus/create-namespace) |
> | namespaces/AuthorizationRules | espaço de nomes | 1-50 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com alphnumeric. |
> | namespaces/disasterRecoveryConfigs | global | 6-50 | Alfanuméricos e hifens.<br><br>Comece com a letra. Terminar com alfanumérico. |
> | namespaces/migrationConfigurations | espaço de nomes |  | Deve ser sempre **$default.** |
> | espaços de nome / filas | espaço de nomes | 1-260 | Alfanuméricos, pontos, hifens, sublinhados e barras.<br><br>Comece e termine com alfanumérico. |
> | namespaces/filas/authorizationRules | fila | 1-50 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com alphnumeric. |
> | espaços de nome / tópicos | espaço de nomes | 1-260 | Alfanuméricos, pontos, hifens, sublinhados e barras.<br><br>Comece e termine com alfanumérico. |
> | namespaces/tópicos/authorizationRules | tópico | 1-50 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com alphnumeric. |
> | espaços de nome / tópicos / subscrições | tópico | 1-50 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com alphnumeric. |
> | espaços de nome / tópicos / subscrições / regras | subscrição | 1-50 | Alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com alphnumeric. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | aglomerados | . | 4-23 | Letras minúsculas, números e hifens.<br><br>Comece com uma letra minúscula. Terminar com letra minúscula ou número. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | signalR | global | 3-63 | Alfanuméricos e hifens.<br><br>Comece com a letra. Terminar com letra ou número.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | managedInstances | global | 1-63 | Letras minúsculas, números e hifens.<br><br>Não é possível iniciar ou terminar com hífen. |
> | servidores | global | 1-63 | Letras minúsculas, números e hifens.<br><br>Não é possível iniciar ou terminar com hífen. |
> | servidores / bases de dados | servidor | 1-128 | Não é possível usar:<br>`<>*%&:\/?`<br><br>Não é possível terminar com período ou espaço. |
> | servidores/bancos de dados/syncGroups | base de dados | 1-150 | Alfanuméricos, hifens e sublinhados. |
> | servidores/elasticPools | servidor | 1-128 | Não é possível usar:<br>`<>*%&:\/?`<br><br>Não é possível terminar com período ou espaço. |
> | servidores/failoverGroups | global | 1-63 | Letras minúsculas, números e hifens.<br><br>Não é possível iniciar ou terminar com hífen. |
> | servidores / firewallRules | servidor | 1-128 | Não é possível usar:<br>`<>*%&:;\/?`<br><br>Não é possível terminar com período. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | storageAccounts | global | 3-24 | Letras minúsculas e números. |
> | armazenamentoContas / blobServices | conta de armazenamento |  | Deve ser `default`. |
> | storageAccounts/blobservices/contêineres | conta de armazenamento | 3-63 | Letras minúsculas, números e hifens.<br><br>Comece com letra minúscula ou número. Não é possível usar hifens consecutivos. |
> | armazenamentoContas / ficheirosServiços | conta de armazenamento |  | Deve ser `default`. |
> | storageAccounts/fileservices/compartilhamentos | conta de armazenamento | 3-63 | Letras minúsculas, números e hifens.<br><br>Não é possível iniciar ou terminar com hífen. Não é possível usar hifens consecutivos. |
> | storageAccounts/managementPolicies | conta de armazenamento |  | Deve ser `default`. |
> | blob | contentor | 1-1024 | Qualquer caractere de URL, diferencia maiúsculas de minúsculas |
> | fila | conta de armazenamento | 3-63 | Letras minúsculas, números e hifens.<br><br>Não é possível iniciar ou terminar com hífen. Não é possível usar hifens consecutivos. |
> | table | conta de armazenamento | 3-63 | Alfanuméricos.<br><br>Comece com a letra. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | storageSyncServices | grupo de recursos | 1-260 | Alfanuméricos, espaços, pontos, hifens e sublinhados.<br><br>Não é possível terminar com período ou espaço. |
> | armazenamentoSyncServices / syncGroups | serviço de sincronização de armazenamento | 1-260 | Alfanuméricos, espaços, pontos, hifens e sublinhados.<br><br>Não é possível terminar com período ou espaço. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | gestores | grupo de recursos | 2-50 | Alfanuméricos e hifens.<br><br>Comece com a letra. Terminar com alfanumérico. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | streamingjobs | grupo de recursos | 3-63 | Alfanuméricos, hifens e sublinhados. |
> | streamingjobs/funções | trabalho de streaming | 3-63 | Alfanuméricos, hifens e sublinhados. |
> | streamingjobs/entradas | trabalho de streaming | 3-63 | Alfanuméricos, hifens e sublinhados. |
> | streamingjobs/saídas | trabalho de streaming | 3-63 | Alfanuméricos, hifens e sublinhados. |
> | streamingjobs/transformações | trabalho de streaming | 3-63 | Alfanuméricos, hifens e sublinhados. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | ambientes | grupo de recursos | 1-90 | Não é possível usar:<br>`'<>%&:\?/#` |
> | ambientes / accessPolicies | environment | 1-90 | Não é possível usar:<br> `'<>%&:\?/#` |
> | ambientes/eventSources | environment | 1-90 | Não é possível usar:<br>`'<>%&:\?/#` |
> | ambientes / referênciaDataSets | environment | 3-63 | Alfanuméricos |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entidade | Âmbito | Comprimento | Carateres Válidos |
> | --- | --- | --- | --- |
> | servidores fazendas | grupo de recursos | 1-40 | Alfanuméricos e hifens. |
> | sites | global | 2-60 | Contém caracteres alfanuméricos e hifens.<br><br>Não é possível iniciar ou terminar com hífen. |
> | sites / slots | site | 2-59 | Alfanuméricos e hifens. |

## <a name="next-steps"></a>Passos seguintes

Para recomendações sobre como nomear recursos, consulte Convenções de [nomeação e marcação recomendadas.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)
