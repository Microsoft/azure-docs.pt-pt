---
title: Recuperar de uma falha no dispositivo Azure Stack Edge Pro
description: Descreve como recuperar de um dispositivo falhado do Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: b1bfbda007619bf5bd94d47297845881758037bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636645"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>Recuperar de um dispositivo GPU Azure Stack Edge Pro falhado 

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Este artigo descreve como recuperar de uma falha não tolerável no seu dispositivo GPU Azure Stack Edge Pro. Uma falha não tolerável no dispositivo GPU Azure Stack Edge Pro requer uma substituição do dispositivo.

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que tem:

- Contactei o Microsoft Support sobre a falha do dispositivo e recomendaram a substituição do dispositivo. 
- A configuração do dispositivo é ressoou como descrito no [Prepare-se para uma falha do dispositivo](azure-stack-edge-gpu-prepare-device-failure.md).


## <a name="configure-replacement-device"></a>Dispositivo de substituição de configuração

Quando o seu dispositivo encontra uma falha não tolerável, é necessário encomendar um dispositivo de substituição. Os passos de configuração do dispositivo de substituição permanecem os mesmos. 

Recupere as informações de configuração do dispositivo que tenha apoiado do dispositivo que falhou. Utilizará estas informações para configurar o dispositivo de substituição.  

Siga estes passos para configurar o dispositivo de substituição:

1. Recolha as informações necessárias na [lista de verificação de implementação](azure-stack-edge-gpu-deploy-checklist.md). Pode utilizar as informações que guardou da configuração do dispositivo anterior. 
1. Encomende um novo dispositivo com a mesma configuração que o que falhou.  Para esemcruitar uma encomenda, [crie um novo recurso Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#) no portal Azure.
1. [Desembale,](azure-stack-edge-gpu-deploy-install.md#unpack-the-device) [suporte de cremalheira](azure-stack-edge-gpu-deploy-install.md#rack-the-device) e [cabo do seu dispositivo](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Ligue-se à UI local do dispositivo](azure-stack-edge-gpu-deploy-connect.md).
1. Configure a rede utilizando os mesmos endereços IP que usou para o seu antigo dispositivo. A utilização dos mesmos endereços IP minimizará o impacto em quaisquer máquinas clientes utilizadas no seu ambiente. Veja como [configurar as definições de rede](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Atribua o mesmo nome do dispositivo e domínio DNS que o seu antigo dispositivo. Desta forma, os seus clientes podem usar o mesmo nome do dispositivo para falar com o novo dispositivo. Veja como configurar a [definição do dispositivo](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Configure os certificados do novo dispositivo da mesma forma que o fez para o dispositivo antigo. Tenha em mente que o novo dispositivo tem um novo número de série de nó. Se usou os seus próprios certificados no dispositivo antigo, terá de obter um novo certificado de nó. Veja como [configurar certificados.](azure-stack-edge-gpu-deploy-configure-certificates.md)
1. Obtenha a chave de ativação do portal Azure e ative o novo dispositivo. Ver como [ativar o dispositivo](azure-stack-edge-gpu-deploy-activate.md).

Está agora pronto para implantar as cargas de trabalho que estava a executar no antigo dispositivo.

## <a name="restore-edge-cloud-shares"></a>Restaurar ações de nuvem Edge

Siga estes passos para restaurar os dados nas partilhas de nuvem Edge no seu dispositivo:

1. [Adicione ações](azure-stack-edge-gpu-manage-shares.md#add-a-share) com os mesmos nomes de ações criados anteriormente no dispositivo falhado. Certifique-se de que, durante a criação de partilhas, **selecione o recipiente blob** para **utilizar a** opção existente e, em seguida, selecione o recipiente que foi utilizado com o dispositivo anterior.
1. [Adicione utilizadores](azure-stack-edge-gpu-manage-users.md#add-a-user) que tenham tido acesso ao dispositivo anterior.
1. [Adicione contas de armazenamento associadas](azure-stack-edge-gpu-manage-storage-accounts.md#add-an-edge-storage-account) às ações anteriormente no dispositivo. Ao criar contas de armazenamento Edge, selecione a partir de um recipiente existente e aponte para o recipiente que foi mapeado para a conta de Armazenamento Azure mapeada no dispositivo anterior. Todos os dados do dispositivo que foi escrito na conta de armazenamento Edge no dispositivo anterior foram enviados para o recipiente de armazenamento selecionado na conta de Armazenamento Azure mapeada.
1. [Refresque os](azure-stack-edge-gpu-manage-shares.md#refresh-shares) dados de partilha do Azure. Isto retira todos os dados da nuvem do recipiente existente para as ações.

## <a name="restore-edge-local-shares"></a>Restaurar ações locais edge

Para se preparar para uma possível falha no dispositivo, pode ter implementado uma das seguintes soluções de backup para proteger os dados de partilhas locais das suas cargas de trabalho Kubernetes ou IoT:

| Software de terceiros           | Referência à solução                               |
|--------------------------------|---------------------------------------------------------|
| Coesidade                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Para mais detalhes, contacte a Coesity.          |
| Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Para mais informações, contacte a Commvault. |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Para mais detalhes, contacte a Veritas.   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Para mais detalhes, contacte o Veeam. |

Depois de configurar completamente o dispositivo de substituição, ative o dispositivo para armazenamento local. 

Siga estes passos para recuperar os dados das ações locais:

1. [Configure o cálculo do dispositivo](azure-stack-edge-gpu-deploy-configure-compute.md).
1. [Adicione uma parte local](azure-stack-edge-gpu-manage-shares.md#add-a-local-share) de volta.
1. Executar o procedimento de recuperação fornecido pela solução de proteção de dados de eleição. Consulte as referências da tabela anterior.

## <a name="restore-vm-files-and-folders"></a>Restaurar ficheiros e pastas VM

Para se preparar para uma possível falha do dispositivo, pode ter implementado uma das seguintes soluções de backup para proteger os dados em VMs:



| Soluções de backup        | SOs Suportados   | Referência                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Agente dos Serviços de Recuperação do Microsoft Azure (MARS) para backup do Azure | Windows        | [Acerca do agente MARS](../backup/backup-azure-about-mars.md)    |
| Coesidade                | Windows, Linux | [Microsoft Azure Integration, Backup & Recovery solution brief](https://www.cohesity.com/solution/cloud/azure) <br>Para mais detalhes, contacte a Coesity.                          |
| Commvault               | Windows, Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Para mais informações, contacte a Commvault.
| Veritas                 | Windows, Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370) <br> Para mais detalhes, contacte a Veritas.                    |
| Veeam                   | Windows, Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Para mais detalhes, contacte o Veeam. |

Depois de o dispositivo de substituição estar totalmente configurado, pode recolocar os VMs com a imagem VM anteriormente utilizada. 

Siga estes passos para recuperar os dados nos VMs:
 
1. [Implementar um VM a partir de uma imagem VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) no dispositivo. 
1. Instale a solução de proteção de dados de eleição no VM.
1. Executar o procedimento de recuperação fornecido pela solução de proteção de dados de eleição. Consulte as referências da tabela anterior.

## <a name="restore-a-kubernetes-deployment"></a>Restaurar uma implantação de Kubernetes

Se tiver realizado a sua implantação de Kubernetes através do Arco Azure, pode restaurar a implementação após uma falha não tolerável do dispositivo. Terá de recolocar a aplicação/recipientes do cliente a partir do `git` repositório onde a definição de aplicação está armazenada. [Informação sobre a implantação de Kubernetes com Azure Arc](./azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)<!--Original text: Kubernetes deployments can be restored from a non-tolerated failure with the device when deployed with Azure Arc. Customer application/containers deployed onto a Kubernetes on Azure Stack Edge via Azure Arc can be redeployed from the git repository where the application definition is. Here is a link to the article to deploy Kubernetes with Arc -->
 
## <a name="next-steps"></a>Passos seguintes

- Saiba como [devolver um dispositivo Azure Stack Edge Pro](azure-stack-edge-return-device.md).
