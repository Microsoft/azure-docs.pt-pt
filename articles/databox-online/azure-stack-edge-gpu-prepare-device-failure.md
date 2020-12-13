---
title: Prepare-se para falha no dispositivo Azure Stack Edge Pro
description: Descreve como obter um substituto para um dispositivo falhado Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: b437ce7b6894ebefe38b32f27d370d9f8c4bfe80
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369026"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Prepare-se para uma falha do dispositivo GPU Azure Stack Edge Pro

Este artigo ajuda-o a preparar-se para uma falha no dispositivo, detalhando como guardar e fazer o back up da configuração do dispositivo e os dados do seu dispositivo GPU Azure Stack Edge Pro. 

O artigo não inclui passos para apoiar os recipientes Kubernetes e IoT implantados no seu dispositivo GPU Azure Stack Edge Pro. 

## <a name="understand-device-failures"></a>Compreender falhas do dispositivo

O seu dispositivo GPU Azure Stack Edge Pro pode experimentar dois tipos de falhas de hardware.

- Falhas toleráveis que requerem que substitua um componente de hardware. Estas falhas permitir-lhe-ão operar o dispositivo num estado degradado. Exemplos destas falhas incluem uma única unidade de alimentação falhada (PSU) ou um único disco falhado no dispositivo. Em cada um destes casos, o dispositivo pode continuar a funcionar. Contacte o Microsoft Support o mais rapidamente possível para substituir os componentes falhados.

- Falhas não toleráveis que requerem a substituição de todo o dispositivo - por exemplo, quando dois discos falharam no seu dispositivo. Nestes casos, contacte imediatamente o Microsoft Support. Depois de determinarem que é necessária uma substituição do dispositivo, o Suporte ajudará na substituição do seu dispositivo Azure Stack Edge.

Para se preparar para falhas não toleráveis, precisa de fazer o apoio às seguintes coisas no seu dispositivo:

- Informação sobre a configuração do dispositivo
- Dados em ações locais edge e ações de nuvem Edge
- Ficheiros e pastas associados aos VMs em execução no seu dispositivo


## <a name="back-up-device-configuration"></a>Configuração do dispositivo de back-up

Durante a configuração inicial do dispositivo, é importante manter uma cópia das informações de configuração do dispositivo, tal como descritas na [lista de verificação de implementação](azure-stack-edge-gpu-deploy-checklist.md). Durante a recuperação, esta informação de configuração será utilizada para aplicar ao novo dispositivo de substituição. 

## <a name="protect-device-data"></a>Proteger os dados do dispositivo

Os dados do dispositivo podem ser de um dos seguintes tipos:

- Dados em ações de nuvem Edge
- Dados em ações locais
- Ficheiros e pastas em VMs

As secções seguintes discutem as etapas e recomendações para proteger cada um destes tipos de dados no seu dispositivo.

## <a name="protect-data-in-edge-cloud-shares"></a>Proteja os dados em ações de nuvem Edge

Pode criar a cloud Edge partilha esses dados de nível do seu dispositivo para o Azure. Dependendo da largura de banda disponível, configurar modelos de largura de banda no seu dispositivo para minimizar qualquer perda de dados se ocorrer uma falha não tolerável.

> [!IMPORTANT]
> Se o dispositivo tiver uma falha não tolerável, podem perder-se os dados locais que não sejam extraídos do dispositivo para o Azure. 

## <a name="protect-data-in-edge-local-shares"></a>Proteger dados em ações locais edge

Se estiver a implementar Kubernetes ou IoT Edge, configuure-se para guardar os dados da aplicação no dispositivo localmente e não sincronizar com o Azure Storage. Os dados são armazenados numa partilha do dispositivo. Pode achar importante fazer backup dos dados destas ações.

As seguintes soluções de proteção de dados de terceiros podem fornecer uma solução de backup para os dados nas ações locais de SMB ou NFS. 

| Software de terceiros           | Referência à solução                               |
|--------------------------------|---------------------------------------------------------|
| Coesidade                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Para mais detalhes, contacte a Coesity.          |
| Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Para mais informações, contacte a Commvault.          |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Para mais detalhes, contacte a Veritas.   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Para mais detalhes, contacte o Veeam. |


## <a name="protect-files-and-folders-on-vms"></a>Proteger ficheiros e pastas em VMs

O Azure Stack Edge trabalha com a Azure Backup e outras soluções de proteção de dados de terceiros para fornecer uma solução de backup para proteger os dados contidos nos VMs que são implantados no dispositivo. A tabela que se segue lista referências às soluções disponíveis que pode escolher.


| Soluções de backup        | SoA apoiado   | Referência                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Agente dos Serviços de Recuperação do Microsoft Azure (MARS) para backup do Azure | Windows        | [Acerca do agente MARS](../backup/backup-azure-about-mars.md)    |
| Coesidade                | Windows, Linux | [Microsoft Azure Integration, Backup & Recovery solution brief](https://www.cohesity.com/solution/cloud/azure) <br>Para mais detalhes, contacte a Coesity.                          |
| Commvault               | Windows, Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br>Para mais informações, contacte a Commvault.                          |
| Veritas                 | Windows, Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370) <br> Para mais detalhes, contacte a Veritas.                    |
| Veeam                   | Windows, Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Para mais detalhes, contacte o Veeam. |


## <a name="next-steps"></a>Passos seguintes

- Saiba como [recuperar de um dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-gpu-recover-device-failure.md)falhado .