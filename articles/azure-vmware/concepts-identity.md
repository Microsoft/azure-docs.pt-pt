---
title: Conceitos - Identidade e acesso
description: Conheça os conceitos de identidade e acesso da Azure VMware Solution
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 7127109801d92d2177f6edac3efcaf76ddf217e6
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674647"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Conceitos de identidade de solução Azure VMware

Ao implementar uma nuvem privada, um servidor vCenter e um gestor NSX-T são a provisionados. Utiliza o vCenter para gerir cargas de trabalho de máquinas virtuais (VM). Utiliza o gestor NSX-T para alargar a rede definida por software em nuvem privada.

O acesso e a gestão de identidade utilizam os privilégios do grupo CloudAdmin para o vCenter e direitos de administrador restritos para o gestor NSX-T. Garante que a sua plataforma de nuvem privada atualiza automaticamente com as funcionalidades e patches mais recentes.  Para mais informações, consulte [o artigo conceitos de atualizações de nuvem privada.][concepts-upgrades]

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

Utilize a conta "administrador" para aceder ao NSX-T Manager. Tem plenos privilégios e permite criar e gerir routers T1, comutadores lógicos e todos os serviços. Os privilégios dão-lhe acesso ao router NSX-T T0. Uma alteração no router T0 pode resultar num desempenho de rede degradado ou sem acesso privado à nuvem. Abra um pedido de suporte no portal Azure para solicitar quaisquer alterações ao seu router NSX-T T0.
  
## <a name="next-steps"></a>Passos seguintes

O próximo passo é aprender sobre [conceitos privados de upgrade em nuvem.][concepts-upgrades]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md