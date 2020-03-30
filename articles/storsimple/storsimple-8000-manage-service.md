---
title: Implementar o serviço StorSimple Device Manager em Azure [ Gestor de Dispositivos) em Azure [ Microsoft Docs
description: Explica como criar e eliminar o serviço StorSimple Device Manager no portal Azure, e descreve como gerir a chave de registo de serviço.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 1e75acc03209fdd7e613801c9152f24aaecfa6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267784"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Implemente o serviço StorSimple Device Manager para dispositivos da série StorSimple 8000

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral

O serviço StorSimple Device Manager funciona no Microsoft Azure e conecta-se a vários dispositivos StorSimple. Depois de criar o serviço, pode usá-lo para gerir todos os dispositivos que estão ligados ao serviço StorSimple Device Manager a partir de uma localização única e central, minimizando assim os encargos administrativos.

Este tutorial descreve os passos necessários para a criação, supressão, migração do serviço e gestão da chave de registo de serviços. As informações contidas neste artigo aplicam-se apenas aos dispositivos da série StorSimple 8000. Para obter mais informações sobre as Matrizes Virtuais StorSimple, vá [implementar um serviço StorSimple Device Manager para o seu StorSimple Virtual Array](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  O portal Azure suporta dispositivos que executam o Update 5.0 ou mais tarde. Se o seu dispositivo não estiver atualizado, instale o Update 5 imediatamente. Para mais informações, vá à [Instalação da Atualização 5](storsimple-8000-install-update-5.md). 
> - Se estiver a utilizar um StorSimple Cloud Appliance (8010/8020), não é possível atualizar um aparelho em nuvem. Utilize a versão mais recente do software para criar um novo aparelho em nuvem com o Update 5.0 e, em seguida, falhe com o novo aparelho em nuvem criado. 
> - Todos os dispositivos que executam o Update 4.0 ou mais cedo irão experimentar uma funcionalidade de gestão reduzida. 

## <a name="create-a-service"></a>Criar um serviço
Para criar um serviço StorSimple Device Manager, precisa de ter:

* Uma subscrição com um Acordo de Empresa
* Uma conta de armazenamento ativa do Microsoft Azure
* A informação de faturação que é usada para a gestão de acesso

Apenas são permitidas as assinaturas com um Acordo Empresarial. Também pode optar por gerar uma conta de armazenamento por defeito quando criar o serviço.

Um único serviço pode gerir vários dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter múltiplas instâncias de serviço para trabalhar com diferentes subscrições, organizações ou mesmo localizações de implementação. 

> [!NOTE]
> Precisa de instâncias separadas do serviço StorSimple Device Manager para gerir dispositivos da série StorSimple 8000 e Matrizes Virtuais StorSimple.

Execute os seguintes passos para criar um serviço.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Para cada serviço StorSimple Device Manager, existem os seguintes atributos:

* **Nome** – O nome que foi atribuído ao seu serviço StorSimple Device Manager quando foi criado. **O nome do serviço não pode ser alterado após a criação do serviço. Isto também é verdade para outras entidades, como dispositivos, volumes, contentores de volume e políticas de backup que não podem ser rebatizadas no portal Azure.**
* **Estado** – O estado do serviço, que pode ser **Ativo,** **Criar**ou **Online**.
* **Localização** – A localização geográfica em que o dispositivo StorSimple será implantado.
* **Subscrição** – A subscrição de faturação que está associada ao seu serviço.

## <a name="delete-a-service"></a>Apagar um serviço

Antes de apagar um serviço, certifique-se de que nenhum dispositivo ligado está a utilizá-lo. Se o serviço estiver a ser utilizado, desative os dispositivos ligados. A operação de desativação cortará a ligação entre o dispositivo e o serviço, mas preservará os dados do dispositivo na nuvem.

> [!IMPORTANT]
> Após a eliminação de um serviço, a operação não pode ser invertida. Qualquer dispositivo que estivesse a utilizar o serviço tem de ser reiniciado nas predefinições da fábrica antes de poder ser utilizado com outro serviço. Neste cenário, perdem-se os dados locais sobre o dispositivo, bem como a configuração.

Efetue os seguintes passos para eliminar um serviço.

### <a name="to-delete-a-service"></a>Para apagar um serviço

1. Procure o serviço que pretende eliminar. Clique no ícone **Recursos** e, em seguida, insera os termos apropriados para pesquisar. Nos resultados da pesquisa, clique no serviço que pretende eliminar.

    ![Serviço de pesquisa para apagar](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Isto leva-o à lâmina de serviço StorSimple Device Manager. Clique em **Apagar**.

    ![Eliminar o serviço](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Clique **sim** na notificação de confirmação. Pode levar alguns minutos para que o serviço seja apagado.

    ![Confirmar eliminação](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Obter a chave de registo do serviço

Depois de ter criado com sucesso um serviço, terá de registar o seu dispositivo StorSimple com o serviço. Para registar o seu primeiro dispositivo StorSimple, necessitará da chave de registo de serviço. Para registar dispositivos adicionais com um serviço StorSimple existente, necessita tanto da chave de registo como da chave de encriptação de dados de serviço (que é gerada no primeiro dispositivo durante o registo). Para obter mais informações sobre a chave de encriptação de dados do serviço, consulte a [segurança StorSimple](storsimple-8000-security.md). Pode obter a chave de registo acedendo a **Teclas** na sua lâmina StorSimple Device Manager.

Execute os seguintes passos para obter a chave de registo de serviço.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Mantenha a chave de registo de serviço num local seguro. Necessitará desta chave, bem como da chave de encriptação de dados de serviço, para registar dispositivos adicionais com este serviço. Depois de obter a chave de registo de serviço, deve configurar o seu dispositivo através do Windows PowerShell para a interface StorSimple.

Para mais informações sobre como utilizar esta chave de registo, consulte o [Passo 3: Configure e registe o dispositivo através do Windows PowerShell para o StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Regenerar a chave de registo de serviço
Precisa regenerar uma chave de registo de serviço se for obrigado a realizar a rotação da chave ou se a lista de administradores de serviços tiver mudado. Quando regenera a tecla, a nova chave é utilizada apenas para registar dispositivos subsequentes. Os dispositivos que já estavam registados não são afetados por este processo.

Execute os seguintes passos para regenerar uma chave de registo de serviço.

### <a name="to-regenerate-the-service-registration-key"></a>Para regenerar a chave de registo de serviço
1. Na lâmina **StorSimple Device Manager,** vá para **as Teclas**de **Gestão &gt; ** .
    
    ![Painel de chaves](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Na lâmina **keys,** clique em **Regenerar**.

    ![Clique em regenerar](./media/storsimple-8000-manage-service/regenregkey3.png)
3. Na lâmina de registo de **serviço Regenerar,** reveja a ação necessária quando as teclas forem regeneradas. Todos os dispositivos subsequentes que estejam registados neste serviço utilizam a nova chave de registo. Clique em **Regenerar** para confirmar. É notificado após a regeneração estar completa.

    ![Confirmar regeneração](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Aparecerá uma nova chave de registo de serviço.

5. Copie esta tecla e guarde-a para registar quaisquer novos dispositivos com este serviço.



## <a name="change-the-service-data-encryption-key"></a>Alterar a chave de encriptação de dados do serviço
As chaves de encriptação de dados de serviço são usadas para encriptar dados confidenciais do cliente, tais como credenciais de conta de armazenamento, que são enviadas do seu serviço StorSimple Manager para o dispositivo StorSimple. Terá de alterar estas teclas periodicamente se a sua organização de TI tiver uma política de rotação chave nos dispositivos de armazenamento. O processo de mudança de chave pode ser ligeiramente diferente dependendo se existe um único dispositivo ou vários dispositivos geridos pelo serviço StorSimple Manager. Para mais informações, vá à [StorSimple segurança e proteção](storsimple-8000-security.md)de dados .

Alterar a chave de encriptação de dados do serviço é um processo de 3 passos:

1. Utilizando scripts Windows PowerShell para O Gestor de Recursos Azure, autorize um dispositivo a alterar a chave de encriptação de dados do serviço.
2. Utilizando o Windows PowerShell para o StorSimple, inicie a alteração da chave de encriptação de dados do serviço.
3. Se tiver mais de um dispositivo StorSimple, atualize a chave de encriptação de dados de serviço nos outros dispositivos.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Passo 1: Utilize o script Do Windows PowerShell para autorizar um dispositivo para alterar a chave de encriptação de dados do serviço
Normalmente, o administrador do dispositivo solicitará que o administrador de serviço autorize um dispositivo a alterar as chaves de encriptação de dados do serviço. O administrador de serviço autorizará então o dispositivo a alterar a chave.

Este passo é realizado utilizando o script baseado em Azure Resource Manager. O administrador de serviço pode selecionar um dispositivo que seja elegível para ser autorizado. O dispositivo está então autorizado a iniciar o processo de alteração da chave de encriptação de dados do serviço. 

Para mais informações sobre o uso do script, vá a [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Que dispositivos podem ser autorizados a alterar as chaves de encriptação de dados do serviço?
Um dispositivo deve cumprir os seguintes critérios antes de ser autorizado a iniciar alterações na chave de encriptação de dados de serviço:

* O dispositivo deve estar on-line para ser elegível para a autorização de alteração da chave de encriptação de dados de serviço.
* Pode autorizar o mesmo dispositivo novamente após 30 minutos se a alteração da chave não tiver sido iniciada.
* Pode autorizar um dispositivo diferente, desde que a alteração da chave não tenha sido iniciada pelo dispositivo previamente autorizado. Depois de autorizado o novo dispositivo, o dispositivo antigo não pode iniciar a alteração.
* Não é possível autorizar um dispositivo enquanto está em curso o capotamento da chave de encriptação de dados de serviço.
* Pode autorizar um dispositivo quando alguns dos dispositivos registados com o serviço tiverem capotado sobre a encriptação enquanto outros não o fizeram. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Passo 2: Utilize o Windows PowerShell para o StorSimple para iniciar a alteração da chave de encriptação de dados do serviço
Este passo é realizado no Windows PowerShell para a interface StorSimple no dispositivo Autorizado StorSimple.

> [!NOTE]
> Nenhuma operação pode ser realizada no portal Azure do seu serviço StorSimple Manager até que a capotagem da chave esteja concluída.


Se estiver a utilizar a consola em série do dispositivo para se ligar à interface Do Windows PowerShell, execute os seguintes passos.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Para iniciar a alteração da chave de encriptação de dados do serviço
1. Selecione a opção 1 para iniciar sessão com acesso total.
2. Na linha de comandos, escreva:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Depois de o cmdlet ter concluído com sucesso, obterá uma nova chave de encriptação de dados de serviço. Copie e guarde esta chave para utilização no passo 3 deste processo. Esta chave será utilizada para atualizar todos os restantes dispositivos registados no serviço StorSimple Manager.
   
   > [!NOTE]
   > Este processo deve ser iniciado no prazo de quatro horas após a autorização de um dispositivo StorSimple.
   > 
   > 
   
   Esta nova chave é então enviada para o serviço para ser empurrada para todos os dispositivos que estão registados no serviço. Um alerta aparecerá então no painel de instrumentos de serviço. O serviço irá desativar todas as operações nos dispositivos registados, e o administrador do dispositivo terá então de atualizar a chave de encriptação de dados de serviço nos outros dispositivos. No entanto, o I/Os (anfitriões que enviam dados para a nuvem) não será interrompido.
   
   Se tiver um único dispositivo registado no seu serviço, o processo de capotamento está agora concluído e pode saltar o próximo passo. Se tiver vários dispositivos registados ao seu serviço, proceda ao passo 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Passo 3: Atualizar a chave de encriptação de dados do serviço em outros dispositivos StorSimple
Estes passos devem ser realizados na interface Windows PowerShell do seu dispositivo StorSimple se tiver vários dispositivos registados no seu serviço StorSimple Manager. A chave que obteve no Passo 2 deve ser utilizada para atualizar todos os restantes dispositivos StorSimple registados no serviço StorSimple Manager.

Execute os seguintes passos para atualizar a encriptação de dados do serviço no seu dispositivo.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Para atualizar a chave de encriptação de dados de serviço em dispositivos físicos
1. Utilize o Windows PowerShell para o StorSimple para ligar à consola. Selecione a opção 1 para iniciar sessão com acesso total.
2. No pedido de comando, escreva:`Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Forneça a chave de encriptação de dados de serviço que obteve no Passo 2: Utilize o [Windows PowerShell para o StorSimple para iniciar a alteração da chave](#to-initiate-the-service-data-encryption-key-change)de encriptação de dados do serviço .

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Para atualizar a chave de encriptação de dados de serviço em todos os aparelhos em nuvem 8010/8020
1. Descarregue e configurar o script [Update-CloudApplianceServiceCryptonKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell. 
2. Open PowerShell e no pedido de comando, escreva:`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Este script irá garantir que a chave de encriptação de dados do serviço está definida em todos os aparelhos de nuvem 8010/8020 sob o gestor do dispositivo.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Operações suportadas em dispositivos que executam versões antes do Update 5.0
No portal Azure, apenas são suportados os dispositivos StorSimple que executam o Update 5.0 e superior. Os dispositivos que estão a executar versões mais antigas têm suporte limitado. Depois de ter migrado para o portal Azure, utilize a tabela seguinte para perceber quais as operações suportadas em dispositivos que executam versões antes do Update 5.0.

| Operação                                                                                                                       | Suportado      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registar um dispositivo                                                                                                               | Sim            |
| Configure as definições do dispositivo, tais como geral, rede e segurança                                                                | Sim            |
| Scan, descarregue e instale atualizações                                                                                             | Sim            |
| Desativar dispositivo                                                                                                               | Sim            |
| Eliminar dispositivo                                                                                                                   | Sim            |
| Criar, modificar e eliminar um recipiente de volume                                                                                   | Não             |
| Criar, modificar e eliminar um volume                                                                                             | Não             |
| Criar, modificar e eliminar uma política de backup                                                                                      | Não             |
| Pegue uma cópia de segurança manual                                                                                                            | Não             |
| Pegue um backup agendado                                                                                                         | Não aplicável |
| Restaurar de uma reviravolta                                                                                                        | Não             |
| Clone para um dispositivo que executa a Atualização 3.0 e mais tarde <br> O dispositivo de origem está a executar a versão antes do Update 3.0.                                | Sim            |
| Clone para um dispositivo que executa versões antes do Update 3.0                                                                          | Não             |
| Failover como dispositivo de origem <br> (de uma versão em execução de dispositivo antes do Update 3.0 para um dispositivo que executa o Update 3.0 e mais tarde)                                                               | Sim            |
| Failover como dispositivo-alvo <br> (a uma versão de software de execução de dispositivos antes do Update 3.0)                                                                                   | Não             |
| Limpe um alerta                                                                                                                  | Sim            |
| Ver políticas de backup, catálogo de backup, volumes, contentores de volume, gráficos de monitorização, empregos e alertas criados em portal clássico | Sim            |
| Ligar e desligar os controladores do dispositivo                                                                                              | Sim            |


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o processo de [implantação StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Saiba mais sobre a gestão da sua conta de [armazenamento StorSimple](storsimple-8000-manage-storage-accounts.md).
* Saiba mais sobre como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
