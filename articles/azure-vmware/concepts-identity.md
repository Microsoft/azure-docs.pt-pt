---
title: Conceitos - Identidade e acesso
description: Conheça os conceitos de identidade e acesso da Azure VMware Solution
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 07a7ac8093524ef4240b8f7607d649520b9439e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586255"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Conceitos de identidade de solução Azure VMware

As nuvens privadas Azure VMware Solution são a provisionadas com um servidor vCenter e um Gestor NSX-T. Utiliza o vCenter para gerir cargas de trabalho de máquinas virtuais (VM). Utiliza o Gestor NSX-T para gerir e alargar a rede de nuvem privada.

O vCenter Access e a gestão de identidade utilizam os privilégios do grupo CloudAdmin. O Gestor NSX-T utiliza permissões de administrador restritos. Isto é por natureza do serviço gerido e garante que a sua plataforma de nuvem privada atualiza com as mais recentes funcionalidades e patches como seria de esperar.  Para mais informações, consulte [o artigo conceitos de atualizações de nuvem privada.][concepts-upgrades]

## <a name="vcenter-access-and-identity"></a>vCenter acesso e identidade

O grupo vCenter CloudAdmin define e proporciona os privilégios no vCenter. Outra opção é fornecer acesso e identidade através da integração do vCenter LDAP single sign-on com a Azure Ative Directory. Você ativa essa integração depois de implementar a sua nuvem privada. 

A tabela mostra privilégios **CloudAdmin** e **CloudGlobalAdmin.**

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

Utilize a conta *de administrador* para aceder ao NSX-T Manager. Tem plenos privilégios e permite criar e gerir Gateways Tier-1 (T1), segmentos (comutadores lógicos) e todos os serviços. Esta conta também fornece acesso ao Gateway NSX-T Tier-0 (T0). Tenha cuidado ao fazer tais alterações, uma vez que isso pode resultar em desempenho de rede degradado ou sem acesso privado à nuvem. Abra um pedido de suporte no portal Azure para solicitar quaisquer alterações ao seu Gateway NSX-T T0.
  
## <a name="next-steps"></a>Passos seguintes

Agora que cobriu os conceitos de acesso e identidade da Azure VMware Solution, talvez queira saber:

- [Conceitos de upgrade de nuvem privada.](concepts-upgrades.md)
- [v Controlo de acesso baseado em funções para Azure VMware Solution](concepts-role-based-access-control.md).
- [Como ativar o recurso Azure VMware Solution](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
