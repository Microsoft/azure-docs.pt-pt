---
title: Implementar o serviço StorSimple Device Manager em Azure | Microsoft Docs
description: Conheça os passos necessários para a criação, supressão, migração do serviço e gestão da chave de registo de serviços.
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
ms.openlocfilehash: 85d66b5e4f7e95d114cbf0b4c681d73eebb93a67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076569"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Implementar o serviço StorSimple Device Manager para dispositivos da série StorSimple 8000

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição Geral

O serviço StorSimple Device Manager funciona no Microsoft Azure e liga-se a vários dispositivos StorSimple. Depois de criar o serviço, pode utilizá-lo para gerir todos os dispositivos que estão ligados ao serviço StorSimple Device Manager a partir de uma localização central única, minimizando assim os encargos administrativos.

Este tutorial descreve os passos necessários para a criação, supressão, migração do serviço e gestão da chave de registo de serviços. As informações contidas neste artigo aplicam-se apenas aos dispositivos da série StorSimple 8000. Para obter mais informações sobre as Matrizes Virtuais StorSimple, vá [implementar um serviço StorSimple Device Manager para o seu StorSimple Virtual Array](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  O portal Azure suporta dispositivos em execução de Update 5.0 ou posterior. Se o seu dispositivo não estiver atualizado, instale imediatamente o Update 5. Para mais informações, aceda à [Atualização 5](storsimple-8000-install-update-5.md). 
> - Se estiver a utilizar um aparelho de nuvem StorSimple (8010/8020), não poderá atualizar um aparelho em nuvem. Utilize a versão mais recente do software para criar um novo aparelho em nuvem com o Update 5.0 e, em seguida, falhe no novo aparelho em nuvem criado. 
> - Todos os dispositivos em execução Update 4.0 ou anteriores experimentarão uma funcionalidade de gestão reduzida. 

## <a name="create-a-service"></a>Criar um serviço
Para criar um serviço StorSimple Device Manager, é necessário ter:

* Uma assinatura com um Acordo de Empresa
* Uma conta de armazenamento ativa do Microsoft Azure
* A informação de faturação que é usada para a gestão de acessos

Apenas são permitidas as subscrições com um Acordo Empresarial. Também pode optar por gerar uma conta de armazenamento predefinida quando criar o serviço.

Um único serviço pode gerir vários dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter múltiplas instâncias de serviço para trabalhar com diferentes subscrições, organizações ou até mesmo locais de implantação. 

> [!NOTE]
> Precisa de instâncias separadas do serviço StorSimple Device Manager para gerir dispositivos da série StorSimple 8000 e Arrays Virtuais StorSimple.

Execute os seguintes passos para criar um serviço.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Para cada serviço StorSimple Device Manager, existem os seguintes atributos:

* **Nome** – O nome que foi atribuído ao seu serviço StorSimple Device Manager quando foi criado. **O nome de serviço não pode ser alterado após a criação do serviço. Isto também é verdade para outras entidades, como dispositivos, volumes, contentores de volume e políticas de backup que não podem ser rebatizadas no portal Azure.**
* **Estado** – O estado do serviço, que pode ser **Ativo,** **Criando,** ou **Online.**
* **Localização** – Localização geográfica na qual o dispositivo StorSimple será implantado.
* **Subscrição** – A subscrição de faturação que está associada ao seu serviço.

## <a name="delete-a-service"></a>Apagar um serviço

Antes de eliminar um serviço, certifique-se de que não há dispositivos ligados a usá-lo. Se o serviço estiver a ser utilizado, desative os dispositivos ligados. A operação desativada cortará a ligação entre o dispositivo e o serviço, mas conserva os dados do dispositivo na nuvem.

> [!IMPORTANT]
> Depois de um serviço ser eliminado, a operação não pode ser invertida. Qualquer dispositivo que estivesse a utilizar o serviço tem de ser reiniciado para falhas de fábrica antes de poder ser utilizado com outro serviço. Neste cenário, perdem-se os dados locais sobre o dispositivo, bem como a configuração.

Execute os seguintes passos para apagar um serviço.

### <a name="to-delete-a-service"></a>Para apagar um serviço

1. Procure o serviço que pretende eliminar. Clique no ícone **Recursos** e, em seguida, insira os termos apropriados para pesquisar. Nos resultados da pesquisa, clique no serviço que pretende eliminar.

    ![Serviço de pesquisa para eliminar](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Isto leva-o à lâmina de serviço StorSimple Device Manager. Clique em **Eliminar**.

    ![Apagar serviço](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Clique em **Sim** na notificação de confirmação. Pode levar alguns minutos para o serviço ser apagado.

    ![Confirmar eliminação](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Obter a chave de registo do serviço

Depois de ter criado um serviço com sucesso, terá de registar o seu dispositivo StorSimple com o serviço. Para registar o seu primeiro dispositivo StorSimple, necessitará da chave de registo de serviço. Para registar dispositivos adicionais com um serviço StorSimple existente, é necessário tanto a chave de registo como a chave de encriptação de dados de serviço (que é gerada no primeiro dispositivo durante o registo). Para obter mais informações sobre a chave de encriptação de dados de serviço, consulte [a segurança StorSimple](storsimple-8000-security.md). Pode obter a chave de registo acedendo às **Chaves** na sua lâmina StorSimple Device Manager.

Execute os seguintes passos para obter a chave de registo de serviço.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Mantenha a chave de registo de serviço num local seguro. Necessitará desta chave, bem como da chave de encriptação de dados de serviço, para registar dispositivos adicionais com este serviço. Depois de obter a chave de registo de serviço, tem de configurar o seu dispositivo através do Windows PowerShell para interface StorSimple.

Para obter mais informações sobre como utilizar esta chave de registo, consulte [o Passo 3: Configurar e registar o dispositivo através do Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Regenerar a chave de registo de serviço
É necessário regenerar uma chave de registo de serviço se for obrigado a efetuar a rotação da chave ou se a lista de administradores de serviço tiver mudado. Quando se regenera a chave, a nova chave é utilizada apenas para registar dispositivos subsequentes. Os dispositivos já registados não são afetados por este processo.

Execute os seguintes passos para regenerar uma chave de registo de serviço.

### <a name="to-regenerate-the-service-registration-key"></a>Para regenerar a chave de registo de serviço
1. Na lâmina **StorSimple Device Manager,** aceda às **Teclas** **de Gestão &gt;** .
    
    ![Vá para a lâmina de Chaves](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Na lâmina **Keys,** clique em **Regenerar.**

    ![Clique em regenerar](./media/storsimple-8000-manage-service/regenregkey3.png)
3. Na lâmina da chave de registo de **serviço Regenerate,** reveja a ação necessária quando as teclas forem regeneradas. Todos os dispositivos subsequentes registados com este serviço utilizam a nova chave de registo. Clique **em Regenerar** para confirmar. É notificado depois que a regeneração estiver completa.

    ![Confirmar regenerar](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Aparecerá uma nova chave de registo de serviço.

5. Copie esta chave e guarde-a para registar quaisquer novos dispositivos com este serviço.



## <a name="change-the-service-data-encryption-key"></a>Alterar a chave de encriptação de dados de serviço
[!INCLUDE [storage-files-migration-generate-key](../../includes/storage-files-migration-generate-key.md)]

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Operações suportadas em dispositivos que executam versões antes do Update 5.0
No portal Azure, apenas os dispositivos StorSimple que executam o Update 5.0 ou superior são suportados. Os dispositivos que estão a executar versões mais antigas têm suporte limitado. Depois de migrar para o portal Azure, utilize a seguinte tabela para perceber quais as operações suportadas nos dispositivos que executam versões antes da Atualização 5.0.

| Operação                                                                                                                       | Suportado      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registar um dispositivo                                                                                                               | Yes            |
| Configurar configurações de dispositivos como geral, rede e segurança                                                                | Yes            |
| Digitalizar, descarregar e instalar atualizações                                                                                             | Yes            |
| Dispositivo desativado                                                                                                               | Yes            |
| Eliminar dispositivo                                                                                                                   | Yes            |
| Criar, modificar e eliminar um recipiente de volume                                                                                   | No             |
| Criar, modificar e apagar um volume                                                                                             | No             |
| Criar, modificar e eliminar uma política de backup                                                                                      | No             |
| Pegue uma cópia de segurança manual                                                                                                            | No             |
| Pegue um backup programado                                                                                                         | Não aplicável |
| Restaurar a partir de um conjunto de backup                                                                                                        | No             |
| Clone para um dispositivo em execução Atualização 3.0 e mais tarde <br> O dispositivo de origem encontra-se em execução antes do Update 3.0.                                | Yes            |
| Clone para um dispositivo a executar versões antes do Update 3.0                                                                          | No             |
| Falha como dispositivo de origem <br> (de uma versão em execução do dispositivo antes do Update 3.0 para um dispositivo em execução Update 3.0 e posterior)                                                               | Yes            |
| Falha como dispositivo-alvo <br> (para um dispositivo que executa a versão do software antes do Update 3.0)                                                                                   | No             |
| Limpe um alerta                                                                                                                  | Yes            |
| Ver políticas de backup, catálogo de backup, volumes, recipientes de volume, gráficos de monitorização, empregos e alertas criados no portal clássico | Yes            |
| Ligar e desligar controladores de dispositivos                                                                                              | Sim            |


## <a name="next-steps"></a>Próximos passos
* Saiba mais sobre o [processo de implementação StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Saiba mais sobre [a gestão da sua conta de armazenamento StorSimple](storsimple-8000-manage-storage-accounts.md).
* Saiba mais sobre como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
