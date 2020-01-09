---
title: Definir suas preferências de portal do Azure | Microsoft Docs
description: Você pode alterar portal do Azure configurações padrão para atender às suas próprias preferências. As configurações incluem tempo limite de sessão inativo, exibição padrão, modo de menu, contraste, tema, notificações e formatos regionais e de idioma
services: azure-portal
keywords: configurações, tempo limite, idioma, regional
author: mblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 0c18ccbf5d9e4884af46e088f1a4ead67f50c3f5
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75641371"
---
# <a name="set-your-azure-portal-preferences"></a>Definir suas preferências de portal do Azure

Você pode alterar as configurações padrão do portal do Azure para atender às suas próprias preferências. Cada uma das configurações listadas abaixo pode ser alterada:

* [Tempo limite da sessão inativa](#change-the-idle-duration-for-inactive-sign-out)
* [Exibição padrão](#choose-your-default-view)
* [Modo de menu do portal](#choose-a-portal-menu-mode)
* [Tema de cor e de alto contraste](#choose-a-theme)
* [Notificações pop-up](#enable-or-disable-pop-up-notifications)
* [Idioma e formato regional](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Alterar configurações gerais do portal

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **configurações** no cabeçalho da página global.

    ![Captura de tela mostrando ícones de cabeçalho de página global com configurações realçadas](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Alterar a duração de ociosidade para saída inativa

A configuração de tempo limite de inatividade ajuda a proteger recursos contra acesso não autorizado se você se esquecer de proteger sua estação de trabalho. Depois de ficar ocioso por algum tempo, você será automaticamente desconectado da sessão de portal do Azure.

Selecione a lista suspensa em **conectar-me quando inativo**. Escolha a duração após a qual sua sessão de portal do Azure será desconectada se você estiver ocioso.

   ![Captura de tela mostrando configurações do portal com configurações de tempo limite inativo realçadas](./media/set-preferences/inactive-signout-user.png)

A alteração é salva automaticamente. Se você estiver ocioso, sua sessão de portal do Azure será desconectada após a duração definida.

Essa configuração também pode ser feita por um administrador no nível do diretório para impor um tempo ocioso máximo. Se um administrador tiver feito uma configuração de tempo limite no nível do diretório, você ainda poderá definir sua própria duração de saída inativa. Escolha uma configuração de hora que seja menor que o que está definido no nível do diretório.

Se o administrador tiver habilitado uma política de tempo limite de inatividade, marque a caixa de seleção **substituir a política de tempo limite de inatividade de diretório** . Defina um intervalo de tempo menor que a configuração de política.

   ![Captura de tela mostrando as configurações do portal com substituir a configuração da política de tempo limite de inatividade do diretório](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Se você for um administrador e quiser impor uma configuração de tempo limite inativo para todos os usuários da portal do Azure, consulte [definir o tempo limite de inatividade no nível do diretório para os usuários do portal do Azure](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>Personalizar a vista predefinida 

Você pode alterar a página que é aberta por padrão quando sua entrada no portal do Azure.

   ![Captura de tela mostrando configurações de portal do Azure com exibição padrão realçada](./media/set-preferences/default-view.png)

A configuração de exibição padrão controla qual portal do Azure exibição é mostrada quando você entra. Você pode optar por abrir a página inicial do Azure por padrão ou o modo de exibição de painel.

* A **página inicial** não pode ser personalizada.  Ele exibe atalhos para serviços populares do Azure e lista os recursos que você usou mais recentemente. Também fornecemos links úteis para recursos como Microsoft Learn e o roteiro do Azure.
* Os painéis podem ser personalizados para criar um espaço de trabalho criado apenas para você. Por exemplo, você pode criar um painel com foco no projeto, na tarefa ou na função. Se você selecionar **painel**, sua exibição padrão vai para o painel usado mais recentemente.

### <a name="choose-a-portal-menu-mode"></a>Escolher um modo de menu do portal

O modo padrão do menu do portal controla a quantidade de espaço que o menu do portal ocupa na página.

* Quando o menu do portal estiver no modo de **submenu** , ele ficará oculto até que você precise dele. Selecione o ícone de menu para abrir ou fechar o menu.
* Se você escolher o modo **encaixado** para o menu do portal, ele estará sempre visível. Você pode recolher o menu para fornecer mais espaço de trabalho. 

### <a name="choose-a-theme"></a>Escolher um tema

O tema escolhido afeta as cores de plano de fundo e fonte que aparecem no portal do Azure. Você pode selecionar um dos quatro temas de cor predefinidos. Selecione cada miniatura para encontrar o tema mais adequado para você.

   ![Captura de tela mostrando configurações de portal do Azure com temas realçados](./media/set-preferences/theme.png)

Você pode escolher um dos temas de alto contraste em vez disso. As configurações de alto contraste tornam a portal do Azure mais fácil de ler para usuários com deficiência visual e substituir todas as outras seleções de tema. Para obter mais informações, consulte [ativar o alto contraste ou alterar o tema](azure-portal-change-theme-high-contrast.md).

### <a name="enable-or-disable-pop-up-notifications"></a>Habilitar ou desabilitar notificações pop-up

As notificações são mensagens do sistema relacionadas à sessão atual. Eles fornecem informações como seu saldo de crédito atual, quando os recursos que você acabou de criar ficam disponíveis ou confirmar sua última ação, por exemplo. Quando as notificações pop-up são ativadas, as mensagens são exibidas brevemente no canto superior da tela. 

Para habilitar ou desabilitar notificações pop-up, marque ou desmarque a caixa de seleção **habilitar notificações pop-up** .

   ![Captura de tela mostrando configurações de portal do Azure com notificações pop-up realçadas](./media/set-preferences/popup-notifications.png)

Para ler todas as notificações recebidas durante a sessão atual, selecione **notificações** no cabeçalho global.

   ![Captura de tela mostrando portal do Azure cabeçalho global com notificações realçadas](./media/set-preferences/read-notifications.png)

Se você quiser ler notificações de sessões anteriores, Procure eventos no log de atividades. Para saber mais, leia [Exibir e recuperar eventos do log de atividades do Azure](/azure/azure-monitor/platform/activity-log-view).

### <a name="settings-under-useful-links"></a>Configurações em links úteis

Se você fez alterações nas configurações de portal do Azure e deseja descartá-las, selecione **Restaurar configurações padrão**. As alterações feitas nas configurações do Portal serão perdidas. Essa opção não afeta as personalizações do painel.

Para obter mais informações sobre como **exportar todas as configurações** ou **excluir todas as configurações e painéis particulares**, consulte [exportar ou excluir configurações do usuário](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Alterar idioma e configurações regionais

Há duas configurações que controlam como o texto no portal do Azure é exibido. A configuração de **idioma** controla o idioma que você vê para texto na portal do Azure. O **formato regional** controla a maneira como as datas, a hora, os números e a moeda são mostrados.

Para alterar o idioma usado no portal do Azure, use o menu suspenso para selecionar na lista de idiomas disponíveis.

A seleção de formato regional muda para exibir opções regionais somente para o idioma selecionado. Para alterar a seleção automática, use a lista suspensa para escolher o formato regional desejado.

Por exemplo, se você selecionar inglês como seu idioma e, em seguida, selecionar Estados Unidos como o formato regional, a moeda será mostrada em dólares americanos. Se você selecionar inglês como o idioma e, em seguida, selecionar Europa como o formato regional, a moeda será mostrada em euros.

Selecione **aplicar** para atualizar suas configurações de idioma e formato regional.

   ![Captura de tela mostrando configurações de idioma e formato regional](./media/set-preferences/language.png)

>[!NOTE]
>Essas configurações regionais e de idioma afetam apenas o portal do Azure. Os links de documentação que abrem em uma nova guia ou janela usarão as configurações de idioma do navegador para determinar o idioma a ser exibido.
>

## <a name="next-steps"></a>Passos seguintes

* [Criar e compartilhar painéis personalizados](azure-portal-dashboards.md)
* [Série de vídeos de instruções portal do Azure](azure-portal-video-series.md)
