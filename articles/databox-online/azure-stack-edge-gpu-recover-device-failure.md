---
title: Recuperar de uma falha no dispositivo Azure Stack Edge Pro
description: Descreve como recuperar de um dispositivo falhado do Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: ef99ff874376e0d76e45e4b0b2c36a78d938130b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91844010"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>Recuperar de um dispositivo GPU Azure Stack Edge Pro falhado 

Este artigo descreve como recuperar de uma falha não tolerável no seu dispositivo GPU Azure Stack Edge Pro. Uma falha não tolerável no dispositivo GPU Azure Stack Edge Pro requer uma substituição do dispositivo.

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que tem:

- Contactei o Microsoft Support sobre a falha do dispositivo e recomendaram a substituição do dispositivo. 
- A configuração do dispositivo é ressoou como descrito no [Prepare-se para uma falha do dispositivo](azure-stack-edge-gpu-prepare-device-failure.md).


## <a name="configure-replacement-device"></a>Dispositivo de substituição de configuração

Quando o seu dispositivo encontra uma falha não tolerável, é necessário encomendar um dispositivo de substituição. Os passos de configuração do dispositivo de substituição permanecem os mesmos. 

Recupere as informações de configuração do dispositivo que tenha apoiado do dispositivo que falhou. Utilizará estas informações para configurar o dispositivo de substituição.  

Siga estes passos para configurar o dispositivo de substituição:

1. Recolha as informações necessárias na [lista de verificação de implementação](azure-stack-edge-gpu-deploy-checklist.md). Deve utilizar as informações que guardou da configuração do dispositivo anterior. 
1. Encomende um novo dispositivo com a mesma configuração que o que falhou.  Para esemcruitar uma encomenda, [crie um novo recurso Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#) no portal Azure.
1. [Desembale,](azure-stack-edge-gpu-deploy-install.md#unpack-the-device) [suporte de cremalheira](azure-stack-edge-gpu-deploy-install.md#rack-the-device) e [cabo do seu dispositivo](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Ligue-se à UI local do dispositivo](azure-stack-edge-gpu-deploy-connect.md).
1. Configure a rede utilizando os mesmos endereços IP que usou para o seu antigo dispositivo. Isto minimiza o impacto em quaisquer máquinas clientes utilizadas no seu ambiente. Veja como [configurar as definições de rede](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Atribua o mesmo nome do dispositivo e domínio DNS que o seu antigo dispositivo. Isto garante que os seus clientes podem usar o mesmo nome do dispositivo para falar com o novo dispositivo. Veja como configurar a [definição do dispositivo](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Configure os certificados do novo dispositivo da mesma forma que o fez para o dispositivo antigo. Tenha em mente que o novo dispositivo tem um novo número de série de nó. Se usou os seus próprios certificados no dispositivo antigo, terá de obter um novo certificado de nó. Veja como [configurar certificados.](azure-stack-edge-gpu-deploy-configure-certificates.md)
1. Obtenha a chave de ativação do portal Azure e ative o novo dispositivo. Ver como [ativar o dispositivo](azure-stack-edge-gpu-deploy-activate.md).

Está agora pronto para implantar as cargas de trabalho que estava a executar no antigo dispositivo.

## <a name="restore-edge-cloud-shares"></a>Restaurar ações de nuvem Edge

Siga estes passos para restaurar os dados nas partilhas de nuvem Edge no seu dispositivo:

1. [Adicione ações](azure-stack-edge-j-series-manage-shares.md#add-a-share) com os mesmos nomes de ações criados anteriormente no dispositivo falhado. Certifique-se de que, durante a criação de partilhas, **selecione o recipiente blob** para **utilizar a** opção existente e, em seguida, selecione o recipiente que foi utilizado com o dispositivo anterior.
1. [Adicione utilizadores](azure-stack-edge-j-series-manage-users.md#add-a-user) que tenham tido acesso ao dispositivo anterior.
1. [Adicione contas de armazenamento associadas](azure-stack-edge-j-series-manage-storage-accounts.md#add-an-edge-storage-account) às ações anteriormente no dispositivo. Ao criar contas de armazenamento Edge, selecione a partir de um recipiente existente e aponte para o recipiente que foi mapeado para a conta de Armazenamento Azure mapeada no dispositivo anterior. Todos os dados do dispositivo que foi escrito na conta de armazenamento Edge no dispositivo anterior foram enviados para o recipiente de armazenamento selecionado na conta de Armazenamento Azure mapeada.
1. [Refresque os](azure-stack-edge-j-series-manage-shares.md#refresh-shares) dados de partilha do Azure. Isto retiraria todos os dados em nuvem do contentor existente para as ações.

## <a name="restore-edge-local-shares"></a>Restaurar ações locais edge

Para se preparar para uma possível falha no dispositivo, pode ter implementado uma das seguintes soluções de backup para proteger os dados de partilhas locais das suas cargas de trabalho Kubernetes ou IoT:

| Software de terceiros           | Referência à solução                               |
|--------------------------------|---------------------------------------------------------|
| Coesidade                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Para mais detalhes, contacte a Coesity.          |
| Veritas                        | Para mais detalhes, contacte a Veritas.   |

Depois de configurar completamente o dispositivo de substituição, ative o dispositivo para armazenamento local. 

Siga estes passos para recuperar os dados das ações locais: 

1. [Configure o cálculo do dispositivo](azure-stack-edge-gpu-deploy-configure-compute.md).
1. [Adicione uma parte local](azure-stack-edge-j-series-manage-shares.md#add-a-local-share) de volta.
1. Executar o procedimento de recuperação fornecido pela solução de proteção de dados de eleição. Consulte as referências da tabela anterior.

## <a name="restore-vm-files-and-folders"></a>Restaurar ficheiros e pastas VM

Para se preparar para uma possível falha do dispositivo, pode ter implementado uma das seguintes soluções de backup para proteger os dados em VMs:



| Soluções de backup        | SoA apoiado   | Referência                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Agente dos Serviços de Recuperação do Microsoft Azure (MARS) para backup do Azure | Windows        | [Acerca do agente MARS](/azure/backup/backup-azure-about-mars)    |
| Coesidade                | Windows, Linux | [Resumo da solução de integração, backup e recuperação da Microsoft Azure](https://www.cohesity.com/solution/cloud/azure) <br>Para mais detalhes, contacte a Coesity.                          |
| Veritas                 | Windows, Linux | Para mais detalhes, contacte a Veritas.                    |

Depois de o dispositivo de substituição estar totalmente configurado, pode recolocar os VMs com a imagem VM anteriormente utilizada. 

Siga estes passos para recuperar os dados nos VMs:
 
1. [Implementar um VM a partir de uma imagem VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) no dispositivo. 
1. Instale a solução de proteção de dados de eleição no VM.
1. Executar o procedimento de recuperação fornecido pela solução de proteção de dados de eleição. Consulte as referências da tabela anterior.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [devolver um dispositivo Azure Stack Edge Pro](azure-stack-edge-return-device.md).
