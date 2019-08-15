---
title: Implantar o serviço StorSimple Device Manager no Azure | Microsoft Docs
description: Explica como criar e excluir o serviço StorSimple Device Manager no portal do Azure e descreve como gerenciar a chave de registro do serviço.
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
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965462"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Implantar o serviço StorSimple Device Manager para dispositivos da série StorSimple 8000

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral

O serviço StorSimple Device Manager é executado em Microsoft Azure e se conecta a vários dispositivos StorSimple. Depois de criar o serviço, você pode usá-lo para gerenciar todos os dispositivos que estão conectados ao serviço StorSimple Device Manager de um único local central, minimizando assim a carga administrativa.

Este tutorial descreve as etapas necessárias para a criação, exclusão, migração do serviço e o gerenciamento da chave de registro do serviço. As informações contidas neste artigo são aplicáveis somente a dispositivos da série StorSimple 8000. Para obter mais informações sobre matrizes virtuais do StorSimple, acesse [implantar um serviço storsimple Device Manager para sua matriz virtual storsimple](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  O portal do Azure dá suporte a dispositivos que executam a atualização 5,0 ou posterior. Se o dispositivo não estiver atualizado, instale a atualização 5 imediatamente. Para obter mais informações, acesse [instalar a atualização 5](storsimple-8000-install-update-5.md). 
> - Se você estiver usando um dispositivo de nuvem StorSimple (8010/8020), não poderá atualizar um dispositivo de nuvem. Use a versão mais recente do software para criar um novo dispositivo de nuvem com a atualização 5,0 e, em seguida, faça failover para o novo dispositivo de nuvem criado. 
> - Todos os dispositivos que executam a atualização 4,0 ou anterior terão uma funcionalidade de gerenciamento reduzida. 

## <a name="create-a-service"></a>Criar um serviço
Para criar um serviço de Device Manager do StorSimple, você precisa ter:

* Uma assinatura com um Enterprise Agreement
* Uma conta de armazenamento do Microsoft Azure ativa
* As informações de cobrança usadas para gerenciamento de acesso

Somente as assinaturas com um Enterprise Agreement são permitidas. Você também pode optar por gerar uma conta de armazenamento padrão ao criar o serviço.

Um único serviço pode gerenciar vários dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter várias instâncias de serviço para trabalhar com diferentes assinaturas, organizações ou até mesmo locais de implantação. 

> [!NOTE]
> Você precisa de instâncias separadas do serviço de Device Manager do StorSimple para gerenciar dispositivos da série StorSimple 8000 e matrizes virtuais StorSimple.

Execute as etapas a seguir para criar um serviço.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Para cada serviço de Device Manager do StorSimple, existem os seguintes atributos:

* **Nome** – o nome que foi atribuído ao serviço de Device Manager do StorSimple quando ele foi criado. **O nome do serviço não pode ser alterado depois que o serviço é criado. Isso também é verdadeiro para outras entidades, como dispositivos, volumes, contêineres de volume e políticas de backup que não podem ser renomeadas no portal do Azure.**
* **Status** – o status do serviço, que pode estar **ativo**, **criando**ou **online**.
* **Local** – a localização geográfica na qual o dispositivo StorSimple será implantado.
* **Assinatura** – a assinatura de cobrança associada ao seu serviço.

## <a name="delete-a-service"></a>Excluir um serviço

Antes de excluir um serviço, verifique se nenhum dispositivo conectado está usando-o. Se o serviço estiver em uso, desative os dispositivos conectados. A operação de desativação irá romper a conexão entre o dispositivo e o serviço, mas preservar os dados do dispositivo na nuvem.

> [!IMPORTANT]
> Depois que um serviço é excluído, a operação não pode ser revertida. Qualquer dispositivo que estava usando o serviço precisa ser redefinido para os padrões de fábrica antes que possa ser usado com outro serviço. Nesse cenário, os dados locais no dispositivo, bem como a configuração, são perdidos.

Execute as etapas a seguir para excluir um serviço.

### <a name="to-delete-a-service"></a>Para excluir um serviço

1. Procure o serviço que você deseja excluir. Clique no ícone **recursos** e, em seguida, insira os termos apropriados para pesquisar. Nos resultados da pesquisa, clique no serviço que você deseja excluir.

    ![Serviço de pesquisa a ser excluído](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Isso levará você para a folha serviço StorSimple Device Manager. Clique em **Eliminar**.

    ![Eliminar serviço](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Clique em **Sim** na notificação de confirmação. Pode levar alguns minutos para que o serviço seja excluído.

    ![Confirmar eliminação](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Obter a chave de registo do serviço

Depois de criar um serviço com êxito, você precisará registrar seu dispositivo StorSimple no serviço. Para registrar seu primeiro dispositivo StorSimple, você precisará da chave de registro do serviço. Para registrar dispositivos adicionais com um serviço StorSimple existente, você precisará da chave de registro e da chave de criptografia de dados de serviço (que é gerada no primeiro dispositivo durante o registro). Para obter mais informações sobre a chave de criptografia de dados de serviço, consulte [segurança do StorSimple](storsimple-8000-security.md). Você pode obter a chave de registro acessando **chaves** na folha Device Manager StorSimple.

Execute as etapas a seguir para obter a chave de registro do serviço.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Mantenha a chave de registro do serviço em um local seguro. Você precisará dessa chave, bem como da chave de criptografia de dados de serviço, para registrar dispositivos adicionais nesse serviço. Depois de obter a chave de registro do serviço, você deve configurar o dispositivo por meio da interface Windows PowerShell para StorSimple.

Para obter detalhes sobre como usar essa chave de registro, [consulte Step 3: Configure e registre o dispositivo por meio](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)do Windows PowerShell para StorSimple.

## <a name="regenerate-the-service-registration-key"></a>Regenerar a chave de registro do serviço
Você precisará regenerar uma chave de registro de serviço se for necessário executar a rotação de chaves ou se a lista de administradores de serviço tiver mudado. Quando você regenera a chave, a nova chave é usada somente para registrar os dispositivos subsequentes. Os dispositivos que já foram registrados não são afetados por esse processo.

Execute as etapas a seguir para regenerar uma chave de registro de serviço.

### <a name="to-regenerate-the-service-registration-key"></a>Para regenerar a chave de registro do serviço
1. Na folha **Device Manager do StorSimple** , vá para **chaves**de **gerenciamento &gt;**  .
    
    ![Painel de chaves](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Na folha **chaves** , clique em **regenerar**.

    ![Clique em regenerar](./media/storsimple-8000-manage-service/regenregkey3.png)
3. Na folha **regenerar chave de registro de serviço** , examine a ação necessária quando as chaves são regeneradas. Todos os dispositivos subsequentes registrados com esse serviço usam a nova chave de registro. Clique em regenerar para confirmar. Você será notificado depois que a regeneração for concluída.

    ![Confirmar regeneração](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Uma nova chave de registro de serviço será exibida.

5. Copie essa chave e salve-a para registrar novos dispositivos com esse serviço.



## <a name="change-the-service-data-encryption-key"></a>Alterar a chave de criptografia de dados de serviço
As chaves de criptografia de dados de serviço são usadas para criptografar dados confidenciais do cliente, como credenciais de conta de armazenamento, que são enviadas do seu serviço de StorSimple Manager para o dispositivo StorSimple. Você precisará alterar essas chaves periodicamente se sua organização de TI tiver uma política de rotação de chaves nos dispositivos de armazenamento. O processo de alteração de chave pode ser ligeiramente diferente dependendo se há um único dispositivo ou vários dispositivos gerenciados pelo serviço de StorSimple Manager. Para obter mais informações, vá para [segurança e proteção de dados do StorSimple](storsimple-8000-security.md).

A alteração da chave de criptografia de dados de serviço é um processo de 3 etapas:

1. Usando scripts do Windows PowerShell para Azure Resource Manager, autorize um dispositivo a alterar a chave de criptografia de dados de serviço.
2. Usando Windows PowerShell para StorSimple, inicie a alteração da chave de criptografia de dados de serviço.
3. Se você tiver mais de um dispositivo StorSimple, atualize a chave de criptografia de dados de serviço nos outros dispositivos.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Passo 1: Usar o script do Windows PowerShell para autorizar um dispositivo a alterar a chave de criptografia de dados de serviço
Normalmente, o administrador do dispositivo solicitará que o administrador do serviço autorize um dispositivo a alterar as chaves de criptografia de dados de serviço. Em seguida, o administrador do serviço autorizará o dispositivo a alterar a chave.

Esta etapa é executada usando o script baseado em Azure Resource Manager. O administrador de serviços pode selecionar um dispositivo que esteja qualificado para ser autorizado. Em seguida, o dispositivo é autorizado a iniciar o processo de alteração da chave de criptografia de dados de serviço. 

Para obter mais informações sobre como usar o script, acesse [Authorize-ServiceEncryptionRollover. ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Quais dispositivos podem ser autorizados a alterar as chaves de criptografia de dados de serviço?
Um dispositivo deve atender aos seguintes critérios antes de poder ser autorizado a iniciar as alterações da chave de criptografia de dados de serviço:

* O dispositivo deve estar online para ser elegível para a autorização de alteração da chave de criptografia de dados de serviço.
* Você pode autorizar o mesmo dispositivo novamente após 30 minutos se a alteração da chave não tiver sido iniciada.
* Você pode autorizar um dispositivo diferente, desde que a alteração da chave não tenha sido iniciada pelo dispositivo autorizado anteriormente. Depois que o novo dispositivo tiver sido autorizado, o dispositivo antigo não poderá iniciar a alteração.
* Não é possível autorizar um dispositivo enquanto a substituição da chave de criptografia de dados de serviço está em andamento.
* Você pode autorizar um dispositivo quando alguns dos dispositivos registrados com o serviço tiverem substituído a criptografia, enquanto outros não. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Passo 2: Usar Windows PowerShell para StorSimple para iniciar a alteração da chave de criptografia de dados de serviço
Essa etapa é executada na interface de Windows PowerShell para StorSimple no dispositivo StorSimple autorizado.

> [!NOTE]
> Nenhuma operação pode ser executada no portal do Azure do serviço de StorSimple Manager até que a substituição de chave seja concluída.


Se você estiver usando o console serial do dispositivo para se conectar à interface do Windows PowerShell, execute as etapas a seguir.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Para iniciar a alteração da chave de criptografia de dados de serviço
1. Selecione a opção 1 para fazer logon com acesso completo.
2. Na linha de comandos, escreva:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Depois que o cmdlet for concluído com êxito, você receberá uma nova chave de criptografia de dados de serviço. Copie e salve essa chave para uso na etapa 3 deste processo. Essa chave será usada para atualizar todos os dispositivos restantes registrados com o serviço de StorSimple Manager.
   
   > [!NOTE]
   > Esse processo deve ser iniciado dentro de quatro horas após a autorização de um dispositivo StorSimple.
   > 
   > 
   
   Essa nova chave é então enviada para o serviço a ser enviado por push para todos os dispositivos registrados com o serviço. Um alerta será exibido no painel de serviço. O serviço desabilitará todas as operações nos dispositivos registrados e o administrador do dispositivo precisará atualizar a chave de criptografia de dados de serviço nos outros dispositivos. No entanto, as e/SS (hosts que enviam dados para a nuvem) não serão interrompidas.
   
   Se você tiver um único dispositivo registrado para o serviço, o processo de substituição agora estará concluído e você poderá ignorar a próxima etapa. Se você tiver vários dispositivos registrados em seu serviço, passe para a etapa 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Passo 3: Atualizar a chave de criptografia de dados de serviço em outros dispositivos StorSimple
Essas etapas devem ser executadas na interface do Windows PowerShell do seu dispositivo StorSimple se você tiver vários dispositivos registrados em seu serviço de StorSimple Manager. A chave que você obteve na etapa 2 deve ser usada para atualizar todos os dispositivos StorSimple restantes registrados com o serviço de StorSimple Manager.

Execute as etapas a seguir para atualizar a criptografia de dados de serviço em seu dispositivo.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Para atualizar a chave de criptografia de dados de serviço em dispositivos físicos
1. Use Windows PowerShell para StorSimple para se conectar ao console. Selecione a opção 1 para fazer logon com acesso completo.
2. No prompt de comando, digite:`Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Forneça a chave de criptografia de dados de serviço obtida [na etapa 2: Use Windows PowerShell para StorSimple para iniciar a alteração](#to-initiate-the-service-data-encryption-key-change)da chave de criptografia de dados de serviço.

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Para atualizar a chave de criptografia de dados de serviço em todos os dispositivos de nuvem 8010/8020
1. Baixe e configure o script do PowerShell [Update-CloudApplianceServiceEncryptionKey. ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) . 
2. Abra o PowerShell e, no prompt de comando, digite:`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Esse script garantirá que a chave de criptografia de dados de serviço seja definida em todos os dispositivos de nuvem 8010/8020 no Gerenciador de dispositivos.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Operações com suporte em dispositivos que executam versões anteriores à atualização 5,0
No portal do Azure, há suporte apenas para os dispositivos StorSimple que executam a atualização 5,0 e superior. Os dispositivos que executam versões mais antigas têm suporte limitado. Depois de migrar para o portal do Azure, use a tabela a seguir para entender quais operações têm suporte em dispositivos que executam versões anteriores à atualização 5,0.

| Operação                                                                                                                       | Suportadas      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registar um dispositivo                                                                                                               | Sim            |
| Definir configurações do dispositivo, como geral, rede e segurança                                                                | Sim            |
| Verificar, baixar e instalar atualizações                                                                                             | Sim            |
| Desativar dispositivo                                                                                                               | Sim            |
| Eliminar dispositivo                                                                                                                   | Sim            |
| Criar, modificar e excluir um contêiner de volume                                                                                   | Não             |
| Criar, modificar e excluir um volume                                                                                             | Não             |
| Criar, modificar e excluir uma política de backup                                                                                      | Não             |
| Fazer um backup manual                                                                                                            | Não             |
| Fazer um backup agendado                                                                                                         | Não aplicável |
| Restaurar de um conjunto de backup                                                                                                        | Não             |
| Clonar para um dispositivo que executa a atualização 3,0 e posterior <br> O dispositivo de origem está executando a versão anterior à atualização 3,0.                                | Sim            |
| Clonar para um dispositivo que executa versões anteriores à atualização 3,0                                                                          | Não             |
| Failover como dispositivo de origem <br> (de um dispositivo que executa a versão anterior à atualização 3,0 para um dispositivo que executa a atualização 3,0 e posterior)                                                               | Sim            |
| Failover como dispositivo de destino <br> (para um dispositivo que executa a versão de software anterior à atualização 3,0)                                                                                   | Não             |
| Limpar um alerta                                                                                                                  | Sim            |
| Exibir políticas de backup, catálogo de backup, volumes, contêineres de volume, gráficos de monitoramento, trabalhos e alertas criados no portal clássico | Sim            |
| Ativar e desativar controladores de dispositivo                                                                                              | Sim            |


## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre o [processo de implantação do StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Saiba mais sobre como [gerenciar sua conta de armazenamento do StorSimple](storsimple-8000-manage-storage-accounts.md).
* Saiba mais sobre como [usar o serviço de Device Manager do storsimple para administrar seu dispositivo storsimple](storsimple-8000-manager-service-administration.md).
