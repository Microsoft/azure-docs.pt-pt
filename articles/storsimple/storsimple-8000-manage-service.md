---
title: Implementar o serviço StorSimple Device Manager no Azure | Documentos da Microsoft
description: Explica como criar e eliminar o serviço StorSimple Device Manager no portal do Azure e descreve como gerir a chave de registo do serviço.
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
ms.openlocfilehash: eb1fe69a7fb99949ac95291c33e76c1a32bf5439
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60506694"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Implementar o serviço StorSimple Device Manager para dispositivos da série StorSimple 8000

## <a name="overview"></a>Descrição geral

O serviço StorSimple Device Manager é executado no Microsoft Azure e liga-se a diversos dispositivos StorSimple. Depois de criar o serviço, pode usá-lo para gerir todos os dispositivos que estão ligados ao serviço StorSimple Device Manager de um local único e central, minimizando, assim, a carga administrativa.

Este tutorial descreve os passos necessários para a criação, eliminação, migração do serviço e o gerenciamento da chave de registo do serviço. As informações contidas neste artigo são aplicáveis apenas a dispositivos da série StorSimple 8000. Para obter mais informações sobre as matrizes virtuais do StorSimple, aceda a [implementar um serviço StorSimple Device Manager do StorSimple Virtual Array](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  O portal do Azure suporta dispositivos que executam a atualização 5.0 ou posterior. Se o dispositivo não estiver atualizado, instale a atualização 5 imediatamente. Para obter mais informações, aceda a [instalar atualização 5](storsimple-8000-install-update-5.md). 
> - Se estiver a utilizar uma StorSimple Cloud Appliance (8010/8020), não é possível atualizar uma aplicação da cloud. Utilize a versão mais recente do software para criar uma nova aplicação de cloud com a atualização 5.0 e, em seguida, efetuar a ativação pós-falha para a nova aplicação da cloud criada. 
> - Todos os dispositivos que executam a atualização 4.0 ou anterior se vão refletir a funcionalidade de gerenciamento reduzida. 

## <a name="create-a-service"></a>Criar um serviço
Para criar um serviço StorSimple Device Manager, tem de ter:

* Uma subscrição com um contrato Enterprise
* Uma conta de armazenamento do Microsoft Azure Active Directory
* As informações de faturas que são utilizadas para gestão de acesso

São permitidas apenas as subscrições com um contrato Enterprise. Também pode optar por gerar uma conta de armazenamento predefinida quando criar o serviço.

Um único serviço pode gerir diversos dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter várias instâncias do serviço para trabalhar com diferentes subscrições, as organizações ou até mesmo as localizações de implementação. 

> [!NOTE]
> Terá de instâncias separadas do serviço StorSimple Device Manager para gerir dispositivos da série StorSimple 8000 e as matrizes virtuais do StorSimple.

Execute os seguintes passos para criar um serviço.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Para cada serviço StorSimple Device Manager, existem os seguintes atributos:

* **Nome** – o nome que foi atribuído ao seu serviço StorSimple Device Manager, quando foram criada. **O nome do serviço não pode ser alterado depois do serviço é criado. Isso também é verdadeiro para outras entidades como dispositivos, volumes, contentores de volumes e políticas de cópia de segurança que não não possível mudar o nome no portal do Azure.**
* **Estado** – o estado do serviço, que pode ser **Active Directory**, **criando**, ou **Online**.
* **Localização** – a localização geográfica na qual o dispositivo StorSimple será implementado.
* **Subscrição** – a subscrição de faturação que está associada ao seu serviço.

## <a name="delete-a-service"></a>Eliminar um serviço

Antes de eliminar um serviço, certifique-se de que não existem dispositivos ligados a estiver a utilizar. Se o serviço estiver em uso, desative os dispositivos ligados. A operação de desativar será a ligação entre o dispositivo e o serviço do servidor, mas preservar os dados do dispositivo na cloud.

> [!IMPORTANT]
> Depois de eliminar um serviço, a operação não pode ser revertida. Todos os dispositivos que estava a utilizar o serviço tem de ser repostos para as predefinições de fábrica, antes de poder ser utilizada com outro serviço. Neste cenário, os dados locais no dispositivo, bem como a configuração, são perdidos.

Execute os passos seguintes para eliminar um serviço.

### <a name="to-delete-a-service"></a>Para eliminar um serviço

1. Procure o serviço que pretende eliminar. Clique em **recursos** ícone e, em seguida, introduza os termos apropriados para procurar. Nos resultados da pesquisa, clique no serviço que pretende eliminar.

    ![Serviço de pesquisa para eliminar](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Isto leva-o para o painel do serviço StorSimple Device Manager. Clique em **Eliminar**.

    ![Eliminar serviço](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Clique em **Sim** da notificação de confirmação. Pode demorar alguns minutos para que o serviço seja eliminado.

    ![Confirmar eliminação](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Obter a chave de registo do serviço

Depois de criar um serviço com êxito, terá de registar o dispositivo StorSimple com o serviço. Para registar o seu primeiro dispositivo StorSimple, terá da chave de registo do serviço. Para registar dispositivos adicionais com um serviço existente do StorSimple, terá da chave de registo e a chave de encriptação de dados do serviço (que é gerado no primeiro dispositivo durante o registo). Para obter mais informações sobre a chave de encriptação de dados do serviço, consulte [StorSimple segurança](storsimple-8000-security.md). Pode obter a chave de registo acessando **chaves** no painel do StorSimple Device Manager.

Execute os passos seguintes para obter a chave de registo do serviço.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Tenha a chave de registo do serviço num local seguro. Precisará desta chave, bem como a chave de encriptação de dados de serviço, para registar dispositivos adicionais com este serviço. Depois de obter a chave de registo do serviço, tem de configurar o seu dispositivo através do Windows PowerShell para StorSimple interface.

Para obter detalhes sobre como utilizar esta chave de registo, consulte [passo 3: Configurar e registar o dispositivo através do Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Regenerar a chave de registo do serviço
Terá de voltar a gerar uma chave de registo do serviço, se for necessário para efetuar a rotação de chaves ou se a lista de administradores de serviço tiver sido alterado. Quando voltar a gerar a chave, a nova chave é utilizada apenas para registar dispositivos subsequentes. Os dispositivos que foram registados já não são afetados por este processo.

Execute os seguintes passos para voltar a gerar uma chave de registo do serviço.

### <a name="to-regenerate-the-service-registration-key"></a>Para voltar a gerar a chave de registo do serviço
1. Na **StorSimple Device Manager** painel, aceda à **Management &gt;**  **chaves**.
    
    ![Painel de chaves](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Na **chaves** painel, clique em **voltar a gerar**.

    ![Clique em voltar a gerar](./media/storsimple-8000-manage-service/regenregkey3.png)
3. Na **chave de registo do serviço de voltar a gerar** painel, verifique a ação é necessária quando as chaves são gerados novamente. Todos os dispositivos subsequentes que estão registados com este serviço utilizam a nova chave de registo. Clique em **regenerar** para confirmar. Será notificado depois de concluída a regeneração.

    ![Confirmar voltar a gerar](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Será apresentada uma nova chave de registo do serviço.

5. Copie esta chave e guarde-a para registar novos dispositivos com este serviço.



## <a name="change-the-service-data-encryption-key"></a>Alterar a chave de encriptação de dados do serviço
Chaves de encriptação de dados de serviço são usadas para criptografar dados confidenciais do cliente, como credenciais de conta de armazenamento, que são enviados do serviço StorSimple Manager para o dispositivo StorSimple. Precisará alterar essas chaves periodicamente se a sua organização de TI tiver uma política de rotação de chaves para os dispositivos de armazenamento. O processo de alteração de chaves pode ser ligeiramente diferentes consoante esteja há um único ou vários dispositivos geridos pelo serviço StorSimple Manager. Para obter mais informações, aceda a [StorSimple segurança e proteção de dados](storsimple-8000-security.md).

Alterar a chave de encriptação de dados do serviço é um processo passo 3:

1. Utilizar scripts do Windows PowerShell para o Azure Resource Manager, autorize um dispositivo para alterar a chave de encriptação de dados do serviço.
2. Usando o Windows PowerShell para StorSimple, inicie uma alteração chave de encriptação de dados do serviço.
3. Se tiver mais de um dispositivo StorSimple, atualize a chave de encriptação de dados do serviço nos outros dispositivos.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Passo 1: Utilizar o script do Windows PowerShell para autorizar um dispositivo para alterar a chave de encriptação de dados do serviço
Normalmente, o administrador do dispositivo irá pedir que o administrador de serviço de autorizar um dispositivo para alterar as chaves de encriptação de dados do serviço. O administrador de serviço, em seguida, irá autorizar o dispositivo para alterar a chave.

Este passo é realizado usando o script do Azure Resource Manager com base. O administrador de serviço pode selecionar um dispositivo elegível ser autorizado. O dispositivo, em seguida, está autorizado a iniciar o processo de alteração de chaves de encriptação de dados do serviço. 

Para obter mais informações sobre como utilizar o script, aceda a [ServiceEncryptionRollover.ps1 autorizar](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Os dispositivos que podem ser autorizados a alterar as chaves de encriptação de dados do serviço?
Ele pode ser autorizado a iniciar alterações de chave de encriptação de dados de serviço, um dispositivo tem de cumprir os seguintes critérios:

* O dispositivo tem de estar online para ser elegível para autorização de alteração de chaves de encriptação de dados do serviço.
* Pode autorizar o mesmo dispositivo novamente após 30 minutos, se a alteração de chaves não foi iniciada.
* Pode autorizar um dispositivo diferente, desde que a alteração de chaves não foi iniciada pelo dispositivo anteriormente autorizado. Depois do novo dispositivo foi autorizado, o dispositivo antigo não pode iniciar a alteração.
* Não é possível autorizar um dispositivo enquanto o rollover de chave de encriptação de dados de serviço está em curso.
* Pode autorizar um dispositivo quando alguns dos dispositivos registados no serviço de ter implementado a encriptação enquanto outros não têm. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Passo 2: Utilize o Windows PowerShell para StorSimple iniciar a chave de encriptação de dados do serviço alterar
Este passo é executado no Windows PowerShell para StorSimple interface no dispositivo StorSimple autorizado.

> [!NOTE]
> Não existem operações podem ser efetuadas no portal do Azure do seu serviço StorSimple Manager até que o rollover da chave é concluído.


Se estiver a utilizar a consola de série do dispositivo para ligar à interface do Windows PowerShell, execute os seguintes passos.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Para iniciar a alteração de chaves de encriptação do serviço dados
1. Selecione a opção 1 para iniciar sessão com acesso total.
2. Na linha de comandos, escreva:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Depois do cmdlet foi concluída com êxito, irá obter uma nova chave de encriptação de dados do serviço. Copie e guarde esta chave para utilização no passo 3 deste processo. Esta chave será utilizada para atualizar todos os restantes dispositivos registados com o serviço StorSimple Manager.
   
   > [!NOTE]
   > Este processo pode ser iniciado dentro de quatro horas de autorização de um dispositivo StorSimple.
   > 
   > 
   
   Esta nova chave, em seguida, é enviado para o serviço seja disponibilizado a todos os dispositivos que estão registados com o serviço. Um alerta, em seguida, irá aparecer no dashboard do serviço. O serviço vai desativar todas as operações nos dispositivos registados e o administrador de dispositivo, em seguida, irá precisar de atualizar a chave de encriptação de dados do serviço nos outros dispositivos. No entanto, não será interrompido o e/s (anfitriões a enviar dados para a cloud).
   
   Se tiver um único dispositivo registado ao seu serviço, o processo de rollover está agora concluído e pode ignorar o passo seguinte. Se tiver vários dispositivos registados para o seu serviço, avance para o passo 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Passo 3: Atualizar a chave de encriptação de dados do serviço em outros dispositivos do StorSimple
Estes passos devem ser executados na interface do Windows PowerShell do dispositivo StorSimple, se tiver vários dispositivos registados para o serviço StorSimple Manager. A chave que obteve no passo 2 deve ser usada para atualizar todos os restante dispositivo StorSimple registado no serviço StorSimple Manager.

Execute os seguintes passos para atualizar a encriptação de dados de serviço no seu dispositivo.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Para atualizar a chave de encriptação de dados do serviço em dispositivos físicos
1. Utilize o Windows PowerShell para StorSimple para ligar à consola do. Selecione a opção 1 para iniciar sessão com acesso total.
2. Na linha de comandos, escreva:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Forneça a chave de encriptação de dados de serviço que obteve no [passo 2: Utilizar o Windows PowerShell para StorSimple para iniciar a alteração de chaves de encriptação do serviço dados](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Para atualizar a chave de encriptação de dados do serviço em todas as aplicações de cloud 8010/8020
1. Transferir e configurar [CloudApplianceServiceEncryptionKey.ps1 atualização](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) script do PowerShell. 
2. Abra o PowerShell e a linha de comandos, escreva:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Este script irá garantir que essa chave de encriptação de dados do serviço está definido em todas as aplicações de cloud 8010/8020 sob o Gestor de dispositivos.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Suporte a operações em dispositivos com versões anteriores à atualização 5.0
No portal do Azure, são suportados apenas os dispositivos do StorSimple que executam a atualização 5.0 e versões superiores. Os dispositivos que executam versões mais antigas têm suporte limitado. Após a migração para o portal do Azure, utilize a seguinte tabela para compreender as operações são suportadas em dispositivos com versões anteriores à atualização 5.0.

| Operação                                                                                                                       | Suportadas      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registar um dispositivo                                                                                                               | Sim            |
| Configurar definições de dispositivo como geral, rede e segurança                                                                | Sim            |
| Procurar, transferir e instalar atualizações                                                                                             | Sim            |
| Desativar dispositivo                                                                                                               | Sim            |
| Eliminar dispositivo                                                                                                                   | Sim            |
| Criar, modificar e eliminar um contentor de volumes                                                                                   | Não             |
| Criar, modificar e eliminar um volume                                                                                             | Não             |
| Criar, modificar e eliminar uma política de cópia de segurança                                                                                      | Não             |
| Faça uma cópia de segurança manual                                                                                                            | Não             |
| Faça uma cópia de segurança agendada                                                                                                         | Não aplicável |
| Restaurar a partir de um backupset                                                                                                        | Não             |
| Clonar um dispositivo com o Update 3.0 e versões posteriores <br> O dispositivo de origem está em execução a versão anterior à atualização 3.0.                                | Sim            |
| Clonar num dispositivo com versões anteriores ao Update 3.0                                                                          | Não             |
| Ativação pós-falha como dispositivo de origem <br> (a partir de um dispositivo com a versão anterior ao Update 3.0 num dispositivo com o Update 3.0 e posterior)                                                               | Sim            |
| Ativação pós-falha como dispositivo de destino <br> (para um dispositivo a executar a versão do software antes do Update 3.0)                                                                                   | Não             |
| Limpar um alerta                                                                                                                  | Sim            |
| Ver políticas de cópia de segurança, o catálogo de cópia de segurança, volumes, contentores de volumes, gráficos de monitorização, tarefas e alertas criados no portal clássico | Sim            |
| Ativar e desativar os controladores de dispositivo                                                                                              | Sim            |


## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre o [processo de implementação do StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Saiba mais sobre [gerir a sua conta de armazenamento StorSimple](storsimple-8000-manage-storage-accounts.md).
* Saiba mais sobre como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
