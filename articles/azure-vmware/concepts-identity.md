---
title: Conceitos - Identidade e acesso
description: Conheça os conceitos de identidade e acesso da Azure VMware Solution
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 9d5b7b4c7c2e0d55cffc99a3f371494f40320a16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88750581"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Conceitos de identidade de solução Azure VMware

Um servidor vCenter e um gestor NSX-T são a provisionados quando uma nuvem privada é implantada. Utiliza o vCenter para gerir as cargas de trabalho da máquina virtual e o gestor NSX-T para alargar a rede definida por software em nuvem privada.

O acesso e a gestão de identidade utilizam os privilégios do grupo CloudAdmin para o vCenter e direitos de administrador restritos para o gestor NSX-T. Esta política garante que a sua plataforma de nuvem privada pode ser atualizada automaticamente. Isto fornece as funcionalidades e patches mais recentes regularmente. Veja o [artigo de atualizações de nuvem privada][concepts-upgrades] para obter mais detalhes sobre atualizações de nuvem privada.

## <a name="vcenter-access-and-identity"></a>vCenter acesso e identidade

Os privilégios no vCenter são fornecidos através do grupo CloudAdmin. Este grupo pode ser gerido localmente em vCenter, ou através da integração do vCenter LDAP single sign-on com a Azure Ative Directory. É-lhe fornecida a capacidade de permitir essa integração depois de implementar uma nuvem privada.

Os privilégios CloudAdmin e CloudGlobalAdmin são mostrados na tabela abaixo.

|  Conjunto de privilégios           | CloudAdmin | CloudGlobalAdmin | Comentário |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Alarmes                  | Um utilizador cloudAdmin tem todos os privilégios de alarmes para alarmes nos Compute-ResourcePool e VMs.     |          --        |  -- |
|  Implantação automática             |  --  |        --        |  A Microsoft faz gestão de anfitriões.  |
|  Certificados            |  --  |        --       |  A Microsoft faz gestão de certificados.  |
|  Biblioteca de Conteúdos         | Um utilizador do CloudAdmin tem privilégios para criar e utilizar ficheiros numa Biblioteca de Conteúdos.    |         Habilitado com SSO.         |  A Microsoft distribuirá ficheiros na Biblioteca de Conteúdos aos anfitriões ESXi.  |
|  Datacenter              |  --  |        --          |  A Microsoft faz todas as operações do data center.  |
|  Arquivo de dados               | Datastore.AllocateSpace, Datastore.Browse, Datastore.Config, Datastore.DeleteFile, Datastore.FileManagement, Datastore.UpdateVirtualMachineMetadata     |    --    |   -- |
|  Gestor de agente da ESX       |  --  |         --       |  A Microsoft faz todas as operações.  |
|  Pasta                  |  Um utilizador cloudAdmin tem todos os privilégios de Pasta.     |  --  |  --  |
|  Global                  |  Global.CancelTask, Global.GlobalTag, Global.Health, Global.LogEvent, Global.ManageCustomFields, Global.ServiceManagers, Global.SetCustomField, Global.SystemTag         |                  |    |
|  Anfitrião                    |  Host.Hbr.HbrManagement      |        --          |  A Microsoft faz todas as outras operações do Host.  |
|  Serviço de Inventário        |  Serviço de Inventário.Marcação      |        --          |  --  |
|  Rede                 |  Rede.Atribuir    |                  |  A Microsoft faz todas as outras operações da Rede.  |
|  Permissões             |  --  |        --       |  A Microsoft faz todas as operações de Permissões.  |
|  Armazenamento orientado para o perfil  |  --  |        --       |  A Microsoft faz todas as operações de perfil.  |
|  Recurso                |  Um utilizador da CloudAdmin tem todos os privilégios de Recursos.        |      --       | --   |
|  Tarefa agendada          |  Um utilizador do CloudAdmin tem todos os privilégios ScheduleTask.   |   --   | -- |
|  Sessões                |  Sessões.GlobalMessage, Sessions.ValidateSsion      |   --   |  A Microsoft faz todas as outras operações de Sessões.  |
|  Vistas de armazenamento           |  StorageViews.View   |        --          |  A Microsoft faz todas as outras operações de Visualização de Armazenamento (Serviço Configure).  |
|  Tarefas                   |  --  |  --   |  A Microsoft gere extensões que gerem tarefas.  |
|  vApp                    |  Um utilizador do CloudAdmin tem todos os privilégios vApp.  |  --  |  --  |
|  Máquina Virtual         |  Um utilizador do CloudAdmin tem todos os privilégios VirtualMachine.  |  --  |  --  |
|  vServiço                |  Um utilizador da CloudAdmin tem todos os privilégios vService.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>Acesso e identidade do Gerente NSX-T

Aceda ao NSX-T Manager utilizando a conta "administrador". Essa conta tem plenos privilégios e permite-lhe criar e gerir routers T1, comutadores lógicos e todos os serviços. Os privilégios completos em NSX-T também lhe proporcionam acesso ao router NSX-T T0. Uma alteração no router T0 pode resultar num desempenho de rede degradado ou na perda de acesso a uma nuvem privada. Para satisfazer os requisitos de suporte, é necessário que abra um pedido de suporte no portal Azure para solicitar quaisquer alterações ao seu router NSX-T T.
  
## <a name="next-steps"></a>Passos seguintes

O próximo passo é aprender sobre [conceitos privados de upgrade em nuvem.][concepts-upgrades]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md