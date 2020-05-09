---
title: Conceitos - Identidade e acesso
description: Conheça os conceitos de identidade e acesso da Azure VMware Solution (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ce85b60b38db86af14b9e9aa06e568436dc76658
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740957"
---
# <a name="azure-vmware-solution-avs-identity-concepts"></a>Conceitos de identidade Azure VMware Solution (AVS)

Um servidor vCenter e um gestor NSX-T são provisionados quando uma nuvem privada é implantada. Usa o vCenter para gerir cargas de trabalho de máquinas virtuais e gestor NSX-T para estender a rede privada de software de nuvem definida.

O acesso e a gestão de identidade usam privilégios de grupo CloudAdmin para vCenter e direitos de administrador restrito para o gestor NSX-T. Esta política garante que a sua plataforma privada de nuvem pode ser atualizada automaticamente. Isto fornece regularmente as mais recentes funcionalidades e patches. Veja o artigo de upgrades de [conceitos de cloud privada][concepts-upgrades] para mais detalhes sobre atualizações privadas em nuvem.

## <a name="vcenter-access-and-identity"></a>acesso e identidade vCenter

Os privilégios em vCenter são fornecidos através do grupo CloudAdmin. Este grupo pode ser gerido localmente no vCenter, ou através da integração do vCenter LDAP single sign-on com o Azure Ative Directory. Tem a capacidade de permitir essa integração depois de implantar uma nuvem privada.

Os privilégios CloudAdmin e CloudGlobalAdmin são mostrados na tabela abaixo.

|  Conjunto de privilégios           | CloudAdmin | CloudGlobalAdmin | Comentário |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Alarmes                  | Um utilizador da CloudAdmin tem todos os privilégios de Alarmes para alarmes nas Compute-ResourcePool e VMs.     |          --        |  -- |
|  Implantação automática             |  --  |        --        |  A Microsoft faz a gestão do anfitrião.  |
|  Certificados            |  --  |        --       |  A Microsoft faz gestão de certificados.  |
|  Biblioteca de Conteúdos         | Um utilizador da CloudAdmin tem privilégios para criar e usar ficheiros numa Biblioteca de Conteúdos.    |         Ativado com SSO.         |  A Microsoft distribuirá ficheiros na Biblioteca de Conteúdos aos anfitriões esXi.  |
|  Datacenter              |  --  |        --          |  A Microsoft faz todas as operações do data center.  |
|  Arquivo de dados               | Datastore.AllocateSpace, Datastore.Browse, Datastore.Config, Datastore.DeleteFile, Datastore.FileManagement, Datastore.UpdateVirtualMachineMetadata     |    --    |   -- |
|  Gestor de Agente ESX       |  --  |         --       |  A Microsoft faz todas as operações.  |
|  Pasta                  |  Um utilizador cloudAdmin tem todos os privilégios de pasta.     |  --  |  --  |
|  Global                  |  Global.CancelTask, Global.GlobalTag, Global.Health, Global.LogEvent, Global.ManageCustomFields, Global.ServiceManagers, Global.SetCustomField, Global.SystemTag         |                  |    |
|  Anfitrião                    |  Host.Hbr.HbrManagement      |        --          |  A Microsoft faz todas as outras operações do Host.  |
|  Serviço de Inventário        |  Serviço de Inventário.Marcação      |        --          |  --  |
|  Rede                 |  Rede.Atribuir    |                  |  A Microsoft faz todas as outras operações da Rede.  |
|  Permissões             |  --  |        --       |  A Microsoft faz todas as operações de Permissões.  |
|  Armazenamento orientado por perfil  |  --  |        --       |  A Microsoft faz todas as operações de Profile.  |
|  Recurso                |  Um utilizador da CloudAdmin tem todos os privilégios de Recursos.        |      --       | --   |
|  Tarefa agendada          |  Um utilizador cloudAdmin tem todos os privilégios ScheduleTask.   |   --   | -- |
|  Sessões                |  Sessões.GlobalMessage, Sessions.ValidateSession      |   --   |  A Microsoft faz todas as outras operações das Sessões.  |
|  Vistas de armazenamento           |  StorageViews.View   |        --          |  A Microsoft faz todas as outras operações de Storage View (Serviço de Configuração).  |
|  Tarefas                   |  --  |  --   |  A Microsoft gere extensões que gerem tarefas.  |
|  vApp                    |  Um utilizador da CloudAdmin tem todos os privilégios vApp.  |  --  |  --  |
|  Máquina Virtual         |  Um utilizador da CloudAdmin tem todos os privilégios VirtualMachine.  |  --  |  --  |
|  vServiço                |  Um utilizador da CloudAdmin tem todos os privilégios vService.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>Acesso e identidade do Gestor NSX-T

Acede ao NSX-T Manager utilizando a conta "administrador". Essa conta tem plenos privilégios e permite-lhe criar e gerir routers T1, comutadores lógicos e todos os serviços. Os privilégios completos em NSX-T também lhe fornecem acesso ao router NSX-T T0. Uma alteração no router T0 pode resultar num desempenho degradado da rede ou numa perda de acesso a uma nuvem privada. Para satisfazer os requisitos de suporte, é necessário que abra um pedido de suporte no portal Azure para solicitar quaisquer alterações ao seu router NSX-T T0.
  
## <a name="next-steps"></a>Passos seguintes

O próximo passo é aprender sobre [conceitos privados][concepts-upgrades]de atualização de nuvens.

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md