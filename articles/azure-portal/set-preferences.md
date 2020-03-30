---
title: Detete as suas preferências do portal Azure [ Microsoft Docs
description: Pode alterar as definições padrão do portal Azure para satisfazer as suas preferências. As definições incluem tempo de intervalo de sessão inativa, vista predefinida, modo menu, contraste, tema, notificações e formatos linguísticos e regionais
services: azure-portal
keywords: configurações, timeout, linguagem, regional
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7bcfdeec832b14eb53c0dab6cb2f53970d85c804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310684"
---
# <a name="set-your-azure-portal-preferences"></a>Definir as preferências do portal do Azure

Pode alterar as definições predefinidas do portal Azure para satisfazer as suas preferências. Cada uma das definições listadas abaixo pode ser alterada:

* [Tempo de sessão inativo](#change-the-idle-duration-for-inactive-sign-out)
* [Vista predefinida](#choose-your-default-view)
* [Modo menu portal](#choose-a-portal-menu-mode)
* [Cor e tema de alto contraste](#choose-a-theme)
* [Notificações pop-up](#enable-or-disable-pop-up-notifications)
* [Idioma e formato regional](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Alterar as definições gerais do portal

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Definições** a partir do cabeçalho de página global.

    ![Screenshot mostrando ícones globais de cabeçalho de página com definições destacadas](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Alterar a duração inativa para a inscrição inativa

A definição de tempo limite de inatividade ajuda a proteger os recursos de acesso não autorizado se se esquecer de garantir o seu posto de trabalho. Depois de ficar inativo por um tempo, é automaticamente assinado fora da sua sessão do portal Azure.

Selecione a descida sob o **sign-out quando estiver inativa**. Escolha a duração após a qual a sua sessão do portal Azure é assinada se estiver inativo.

   ![Screenshot mostrando definições do portal com definições inativas de tempo de tempo realçadas](./media/set-preferences/inactive-signout-user.png)

A alteração é guardada automaticamente. Se estiver inativo, a sua sessão do portal Azure assinará após a duração que definiu.

Esta definição também pode ser feita por um administrador ao nível do diretório para impor um tempo máximo de inatividade. Se um administrador tiver feito um intervalo de tempo de nível de diretório, ainda pode definir a sua própria duração de inscrição inativa. Escolha uma definição de tempo inferior à definida ao nível do diretório.

Se o seu administrador tiver ativado uma política de tempo limite de inatividade, selecione a caixa de verificação da política de tempo de inatividade de **Override.** Estabeleça um intervalo de tempo inferior ao ajuste de políticas.

   ![Screenshot mostrando configurações do portal com anular a definição de política de tempo limite de inatividade do diretório em destaque](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Se você é um administrador, e você quer impor um tempo de tempo inativo para todos os utilizadores do portal Azure, consulte [set nível de inatividade para utilizadores do portal Azure](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>Escolha a sua vista padrão 

Pode alterar a página que abre por defeito quando iniciar a sua sessão no portal Azure.

   ![Screenshot mostrando definições do portal Azure com vista padrão realçada](./media/set-preferences/default-view.png)

A definição de visualização predefinida controla os controlos que o portal Azure visualiza quando iniciar sessão. Pode optar por abrir o Azure Home por defeito ou a vista dashboard.

* **A casa** não pode ser personalizada.  Exibe atalhos para serviços populares do Azure e lista os recursos que utilizou mais recentemente. Também lhe damos links úteis para recursos como o Microsoft Learn e o roteiro do Azure.
* Os dashboards podem ser personalizados para criar um espaço de trabalho projetado apenas para si. Por exemplo, pode construir um dashboard que seja projeto, tarefa ou papel focado. Se selecionar **o Dashboard,** a sua vista padrão irá para o seu painel de instrumentos mais recentemente utilizado.

### <a name="choose-a-portal-menu-mode"></a>Escolha um modo de menu portal

O modo predefinido para o menu do portal controla a quantidade de espaço que o menu do portal ocupa na página.

* Quando o menu do portal está em modo **flyout,** está escondido até precisar. Selecione o ícone do menu para abrir ou fechar o menu.
* Se escolher o modo **atracado** para o menu do portal, é sempre visível. Você pode colapsar o menu para fornecer mais espaço de trabalho. 

### <a name="choose-a-theme"></a>Escolher um tema

O tema que escolhe afeta o fundo e as cores de fonte que aparecem no portal Azure. Pode selecionar entre um dos quatro temas de cores predefinidos. Selecione cada miniatura para encontrar o tema que melhor lhe convém.

   ![Screenshot mostrando definições do portal Azure com temas em destaque](./media/set-preferences/theme.png)

Em vez disso, pode escolher um dos temas de alto contraste. As configurações de alto contraste tornam o portal Azure mais fácil de ler para utilizadores com deficiência sonora e sobrepor-se a todas as outras seleções temáticas. Para mais informações, consulte [Ligar o alto contraste ou alterar o tema](azure-portal-change-theme-high-contrast.md).

### <a name="enable-or-disable-pop-up-notifications"></a>Ativar ou desativar notificações pop-up

As notificações são mensagens do sistema relacionadas com a sua sessão atual. Eles fornecem informações como o seu saldo de crédito atual, quando os recursos que acabou de criar ficam disponíveis, ou confirmam a sua última ação, por exemplo. Quando as notificações pop-up são ligadas, as mensagens exibem brevemente no canto superior do ecrã. 

Para ativar ou desativar as notificações pop-up, selecione ou desselecione a caixa de verificação de **notificações pop-up Enable.**

   ![Screenshot mostrando definições do portal Azure com notificações pop-up em destaque](./media/set-preferences/popup-notifications.png)

Para ler todas as notificações recebidas durante a sua sessão atual, selecione **Notificações** do cabeçalho global.

   ![Screenshot mostrando o portal Azure cabeçalho global com notificações em destaque](./media/set-preferences/read-notifications.png)

Se quiser ler notificações de sessões anteriores, procure eventos no registo de Atividades. Para saber mais, leia Ver e recuperar eventos de [log da Atividade Azure.](/azure/azure-monitor/platform/activity-log-view)

### <a name="settings-under-useful-links"></a>Definições sob links úteis

Se tiver feito alterações nas definições do portal Azure e pretender descartá-las, selecione **definições predefinidas restaurar**. Quaisquer alterações que tenha feito às definições do portal perder-se-ão. Esta opção não afeta as personalizações do dashboard.

Para mais informações sobre **exportar todas as definições** ou **eliminar todas as definições e dashboards privados,** consulte [Exportar ou excluir as definições do utilizador](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Alterar a linguagem e as configurações regionais

Existem duas definições que controlam a forma como o texto aparece no portal Azure. A definição de **idioma** controla a linguagem que vê para texto no portal Azure. **O formato regional** controla a forma como as datas, a hora, os números e a moeda são mostrados.

Para alterar a linguagem que é usada no portal Azure, utilize a queda para selecionar a partir da lista de idiomas disponíveis.

A seleção de formato regional muda para exibir opções regionais apenas para a língua que selecionou. Para alterar essa seleção automática, utilize o drop-down para escolher o formato regional que deseja.

Por exemplo, se selecionar o inglês como língua e, em seguida, selecionar os Estados Unidos como formato regional, a moeda é mostrada em dólares americanos. Se selecionar o inglês como língua e, em seguida, selecionar a Europa como formato regional, a moeda é mostrada em euros.

Selecione **Aplicar** para atualizar as definições do seu idioma e formato regional.

   ![Screenshot mostrando definições de idioma e formato regional](./media/set-preferences/language.png)

>[!NOTE]
>Estas configurações linguísticas e regionais afetam apenas o portal Azure. Os links de documentação que se abrem num novo separador ou janela usarão as definições de idioma do seu navegador para determinar o idioma para exibir.
>

## <a name="next-steps"></a>Passos seguintes

* [Criar e partilhar dashboards personalizados](azure-portal-dashboards.md)
* [Séries de vídeos de procedimentos do portal do Azure](azure-portal-video-series.md)
