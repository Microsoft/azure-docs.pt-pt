---
title: Desconfiem das preferências do portal Azure ! Microsoft Docs
description: Pode alterar as definições padrão do portal Azure para satisfazer as suas preferências. As definições incluem tempo limite de sessão inativo, vista padrão, modo menu, contraste, tema, notificações e formatos linguísticos e regionais
services: azure-portal
keywords: configurações, timeout, linguagem, regional
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: a3f51c356f4476782bb830b2702a8fe87c79235c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84308069"
---
# <a name="set-your-azure-portal-preferences"></a>Definir as preferências do portal do Azure

Pode alterar as definições predefinidas do portal Azure para satisfazer as suas preferências. Cada uma das definições listadas abaixo pode ser alterada:

* [Tempo de ssões inativos](#change-the-idle-duration-for-inactive-sign-out)
* [Vista padrão](#choose-your-default-view)
* [Modo de menu portal](#choose-a-portal-menu-mode)
* [Cor e tema de alto contraste](#choose-a-theme)
* [Notificações pop-up](#enable-or-disable-pop-up-notifications)
* [Idioma e formato regional](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Alterar definições gerais do portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Definições** do cabeçalho de página global.

    ![Screenshot mostrando ícones de cabeçalho de página global com definições realçadas](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Alterar a duração do inativo para a sência inativa

A definição de tempo de inatividade ajuda a proteger os recursos de acesso não autorizado se se esquecer de proteger a sua estação de trabalho. Depois de ter estado inativo durante algum tempo, é automaticamente contratado para fora da sessão do portal Azure.

Selecione o drop-down em **Sign me out quando estiver inativo**. Escolha a duração após a qual a sua sessão do portal Azure está assinada se estiver inativa.

   ![Screenshot mostrando definições do portal com definições de tempo limite inativas em destaque](./media/set-preferences/inactive-signout-user.png)

A alteração é guardada automaticamente. Se estiver inativo, a sessão do portal Azure será assinalada após a duração definida.

Esta definição também pode ser feita por um administrador ao nível do diretório para impor um tempo máximo de marcha lenta. Se um administrador tiver feito uma definição de tempo limite de nível de diretório, ainda pode definir a sua própria duração de inscrição inativa. Escolha uma definição de tempo inferior ao definido ao nível do diretório.

Se o seu administrador tiver ativado uma política de tempo de tempo de inatividade, selecione a caixa **de verificação da política de tempo de inatividade do diretório.** Defina um intervalo de tempo inferior à definição de política.

   ![Screenshot mostrando definições do portal com sobreposição da definição de política de tempo de inatividade do diretório realçada](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Se você é um administrador, e deseja impor uma definição de tempo inativa para todos os utilizadores do portal Azure, consulte [o tempo de inatividade de nível de diretório definido para os utilizadores do portal Azure](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>Escolha a sua vista padrão 

Pode alterar a página que abre por predefinição quando o seu sômbro no portal Azure.

   ![Screenshot mostrando as definições do portal Azure com vista padrão realçada](./media/set-preferences/default-view.png)

A definição de visualização predefinida controla a visualização do portal Azure quando iniciar sing. Pode optar por abrir o Azure Home por predefinição ou a vista do Dashboard.

* **A casa** não pode ser personalizada.  Exibe atalhos para os populares serviços Azure e lista os recursos que utilizou mais recentemente. Também lhe damos links úteis para recursos como o Microsoft Learn e o roteiro Azure.
* Os dashboards podem ser personalizados para criar um espaço de trabalho projetado apenas para si. Por exemplo, pode construir um dashboard que seja projetado, tarefa ou função focado. Se selecionar **o Dashboard,** a sua vista padrão irá para o seu painel de instrumentos mais recentemente utilizado.

### <a name="choose-a-portal-menu-mode"></a>Escolha um modo de menu portal

O modo predefinido para o menu do portal controla o espaço que o menu do portal ocupa na página.

* Quando o menu do portal está em modo **flyout,** está escondido até precisar. Selecione o ícone do menu para abrir ou fechar o menu.
* Se escolher o modo **docked** para o menu do portal, é sempre visível. Pode colapsar o menu para fornecer mais espaço de trabalho. 

### <a name="choose-a-theme"></a>Escolher um tema

O tema que escolhe afeta as cores de fundo e de fonte que aparecem no portal Azure. Pode selecionar entre um dos quatro temas de cores predefinidos. Selecione cada miniatura para encontrar o tema que melhor lhe convém.

   ![Screenshot mostrando definições do portal Azure com temas em destaque](./media/set-preferences/theme.png)

Em vez disso, pode escolher um dos temas de alto contraste. As configurações de alto contraste tornam o portal Azure mais fácil de ler para pessoas que têm uma deficiência visual e sobrepõem-se a todas as outras seleções temáticas. Para obter mais informações, consulte [Ligue o tema de alto contraste ou mude o tema](azure-portal-change-theme-high-contrast.md).

### <a name="enable-or-disable-pop-up-notifications"></a>Ativar ou desativar notificações pop-up

As notificações são mensagens do sistema relacionadas com a sessão atual. Eles fornecem informações como o seu saldo de crédito atual, quando os recursos que acabou de criar ficam disponíveis, ou confirmam a sua última ação, por exemplo. Quando as notificações pop-up são ligadas, as mensagens são exibidas brevemente no canto superior do ecrã. 

Para ativar ou desativar notificações pop-up, selecione ou desescode a caixa **de verificação de notificações pop-up Ativa.**

   ![Screenshot mostrando as definições do portal Azure com notificações pop-up em destaque](./media/set-preferences/popup-notifications.png)

Para ler todas as notificações recebidas durante a sessão atual, **selecione Notificações** do cabeçalho global.

   ![Screenshot mostrando cabeçalho global do portal Azure com notificações em destaque](./media/set-preferences/read-notifications.png)

Se pretender ler notificações de sessões anteriores, procure eventos no registo de Atividades. Para saber mais, leia [Ver e recuperar eventos de registo de atividades Azure](/azure/azure-monitor/platform/activity-log-view).

### <a name="settings-under-useful-links"></a>Definições em links úteis

Se tiver escamado as definições do portal Azure e quiser descartá-las, selecione **Restaurar as definições predefinidas**. Quaisquer alterações que tenha feito nas definições do portal serão perdidas. Esta opção não afeta as personalizações do dashboard.

Para obter mais informações sobre **exportar todas as definições** ou **Eliminar todas as definições e dashboards privados,** consulte [Exportar ou eliminar as definições do utilizador](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Alterar a linguagem e as configurações regionais

Existem duas definições que controlam a forma como o texto aparece no portal Azure. A definição **de idioma** controla o idioma que vê para texto no portal Azure. **O formato regional** controla a forma como são mostradas as datas, a hora, os números e a moeda.

Para alterar o idioma que é usado no portal Azure, use o drop-down para selecionar a partir da lista de idiomas disponíveis.

A seleção de formato regional muda para exibir opções regionais apenas para o idioma que selecionou. Para alterar essa seleção automática, utilize o drop-down para escolher o formato regional que pretende.

Por exemplo, se selecionar o inglês como língua e, em seguida, selecionar estados unidos como formato regional, a moeda é mostrada em dólares americanos. Se selecionar o inglês como língua e selecionar a Europa como formato regional, a moeda é mostrada em euros.

Selecione **Aplicar** para atualizar as definições do seu idioma e formato regional.

   ![Screenshot mostrando definições de linguagem e formato regional](./media/set-preferences/language.png)

>[!NOTE]
>Estas configurações linguísticas e regionais afetam apenas o portal Azure. Os links de documentação que abrem num novo separador ou janela utilizarão as definições de idioma do seu navegador para determinar o idioma a exibir.
>

## <a name="next-steps"></a>Passos seguintes

* [Criar e partilhar dashboards personalizados](azure-portal-dashboards.md)
* [Séries de vídeos de procedimentos do portal do Azure](azure-portal-video-series.md)
