---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a142de62f1ab7046d47c29753863b16864d97536
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075624"
---
As chaves de encriptação de dados de serviço são usadas para encriptar dados confidenciais do cliente, tais como credenciais de conta de armazenamento, que são enviados do seu serviço StorSimple Manager para o dispositivo StorSimple. Terá de alterar estas teclas periodicamente se a sua organização de TI tiver uma política de rotação chave nos dispositivos de armazenamento. O processo de mudança de chave pode ser ligeiramente diferente dependendo se existe um único dispositivo ou vários dispositivos geridos pelo serviço StorSimple Manager. Para mais informações, aceda à segurança e proteção de [dados da StorSimple.](../articles/storsimple/storsimple-8000-security.md)

Alterar a chave de encriptação de dados de serviço é um processo de 3 etapas:

1. Utilizando scripts Windows PowerShell para O Gestor de Recursos Azure, autorize um dispositivo para alterar a chave de encriptação de dados de serviço.
2. Utilizando o Windows PowerShell para StorSimple, inicie a alteração da chave de encriptação de dados de serviço.
3. Se tiver mais do que um dispositivo StorSimple, atualize a chave de encriptação de dados de serviço nos outros dispositivos.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Passo 1: Utilize o script Windows PowerShell para autorizar um dispositivo a alterar a chave de encriptação de dados de serviço
Normalmente, o administrador do dispositivo solicitará que o administrador de serviço autorize um dispositivo a alterar as chaves de encriptação de dados de serviço. O administrador de serviço autorizará então o dispositivo a alterar a tecla.

Este passo é realizado utilizando o script baseado no Azure Resource Manager. O administrador de serviço pode selecionar um dispositivo que seja elegível para ser autorizado. O dispositivo é então autorizado a iniciar o processo de alteração da chave de encriptação de dados de serviço. 

Para mais informações sobre a utilização do script, vá a [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Quais os dispositivos que podem ser autorizados a alterar chaves de encriptação de dados de serviço?
Um dispositivo deve cumprir os seguintes critérios antes de poder ser autorizado a iniciar alterações na chave de encriptação de dados de serviço:

* O dispositivo deve estar online para ser elegível para a autorização de alteração da chave de encriptação de dados de serviço.
* Pode autorizar o mesmo dispositivo novamente após 30 minutos se a alteração da chave não tiver sido iniciada.
* Pode autorizar um dispositivo diferente, desde que a alteração da chave não tenha sido iniciada pelo dispositivo previamente autorizado. Depois de autorizado o novo dispositivo, o dispositivo antigo não pode iniciar a alteração.
* Não é possível autorizar um dispositivo enquanto estiver em curso a capotamento da chave de encriptação de dados de serviço.
* Pode autorizar um dispositivo quando alguns dos dispositivos registados no serviço tiverem revirado a encriptação enquanto outros não o fizeram. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Passo 2: Utilize o Windows PowerShell para storSimple iniciar a alteração da chave de encriptação de dados de serviço
Este passo é realizado no Windows PowerShell para interface StorSimple no dispositivo autorizado StorSimple.

> [!NOTE]
> Nenhuma operação pode ser realizada no portal Azure do seu serviço StorSimple Manager até que a capotagem da chave esteja concluída.


Se estiver a utilizar a consola em série do dispositivo para ligar à interface Windows PowerShell, execute os seguintes passos.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Para iniciar a alteração da chave de encriptação de dados de serviço
1. Selecione a opção 1 para iniciar sessão com acesso total.
2. Na linha de comandos, escreva:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Depois de o cmdlet ter sido concluído com sucesso, obterá uma nova chave de encriptação de dados de serviço. Copie e guarde esta chave para utilização no passo 3 deste processo. Esta chave será utilizada para atualizar todos os dispositivos restantes registados no serviço StorSimple Manager.
   
   > [!NOTE]
   > Este processo deve ser iniciado no prazo de quatro horas após a autorização de um dispositivo StorSimple.
   > 
   > 
   
   Esta nova chave é então enviada para o serviço para ser empurrada para todos os dispositivos que estão registados no serviço. Em seguida, aparecerá um alerta no painel de instrumentos de serviço. O serviço irá desativar todas as operações nos dispositivos registados, e o administrador do dispositivo terá então de atualizar a chave de encriptação de dados de serviço nos outros dispositivos. No entanto, o I/Os (anfitriões que enviam dados para a nuvem) não será interrompido.
   
   Se tiver um único dispositivo registado no seu serviço, o processo de capotamento está concluído e pode saltar o passo seguinte. Se tiver vários dispositivos registados no seu serviço, dirijam-se ao passo 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Passo 3: Atualizar a chave de encriptação de dados de serviço em outros dispositivos StorSimple
Estes passos devem ser realizados na interface Windows PowerShell do seu dispositivo StorSimple se tiver vários dispositivos registados no seu serviço StorSimple Manager. A chave que obteve no Passo 2 deve ser utilizada para atualizar todo o restante dispositivo StorSimple registado no serviço StorSimple Manager.

Execute os seguintes passos para atualizar a encriptação de dados de serviço no seu dispositivo.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Para atualizar a chave de encriptação de dados de serviço em dispositivos físicos
1. Utilize o Windows PowerShell para que o StorSimple se conecte à consola. Selecione a opção 1 para iniciar sessão com acesso total.
2. Na solicitação de comando, escreva:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Forneça a chave de encriptação de dados de serviço que obteve no [passo 2: Utilize o Windows PowerShell para storSimple para iniciar a alteração da chave de encriptação de dados de serviço](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Para atualizar a chave de encriptação de dados de serviço em todos os aparelhos em nuvem 8010/8020
1. Baixar e configurar [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) script PowerShell. 
2. Open PowerShell e na solicitação de comando, escreva:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Este script irá garantir que a chave de encriptação de dados de serviço está definida em todos os aparelhos em nuvem 8010/8020 sob o gestor do dispositivo.