---
title: Gerir as definições e as preferências do portal do Azure
description: Pode alterar as definições padrão do portal Azure para satisfazer as suas preferências. As definições incluem tempo limite de sessão inativo, vista padrão, modo menu, contraste, tema, notificações e formatos linguísticos e regionais
keywords: configurações, timeout, linguagem, regional
ms.date: 08/05/2020
ms.topic: how-to
ms.openlocfilehash: 591b1ce6c2c069f9c0266588bf32f7303bf49708
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588805"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Gerir as definições e as preferências do portal do Azure

Pode alterar as definições predefinidas do portal Azure para satisfazer as suas preferências. A maioria das definições estão disponíveis no menu **Definições** no cabeçalho de página global.

![Screenshot mostrando ícones de cabeçalho de página global com definições realçadas](./media/set-preferences/header-settings.png)


## <a name="choose-your-default-subscription"></a>Escolha a sua subscrição padrão

Pode alterar a subscrição que abre por predefinição quando iniciar sedições no portal Azure. Isto é útil se tiver uma subscrição primária com a qual trabalha, mas use outros ocasionalmente. 

:::image type="content" source="media/set-preferences/filter-subscription-default-view.png" alt-text="Filtrar a lista de recursos por subscrição.":::

1. Selecione o ícone do diretório e do filtro de subscrição no cabeçalho de página global.

1. Selecione as subscrições que pretende como as subscrições predefinidos quando lançar o portal. 

    :::image type="content" source="media/set-preferences/default-directory-subscription-filter.png" alt-text="Selecione as subscrições que pretende como as subscrições predefinidos quando lançar o portal."::: 


## <a name="choose-your-default-view"></a>Escolha a sua vista padrão 

Pode alterar a página que abre por predefinição quando iniciar súb9 no portal Azure.

![Screenshot mostrando as definições do portal Azure com vista padrão realçada](./media/set-preferences/default-view.png)

- **A casa** não pode ser personalizada.  Exibe atalhos para os populares serviços Azure e lista os recursos que utilizou mais recentemente. Também lhe damos links úteis para recursos como o Microsoft Learn e o roteiro Azure.

- Os dashboards podem ser personalizados para criar um espaço de trabalho projetado apenas para si. Por exemplo, pode construir um dashboard que seja projetado, tarefa ou função focado. Se selecionar **o Dashboard,** a sua vista padrão irá para o seu painel de instrumentos mais recentemente utilizado. Para obter mais informações, consulte [Criar e partilhar dashboards no portal Azure](azure-portal-dashboards.md).

## <a name="choose-a-portal-menu-mode"></a>Escolha um modo de menu portal

O modo predefinido para o menu do portal controla o espaço que o menu do portal ocupa na página.

![Screenshot que mostra como definir o modo predefinido para o menu do portal.](./media/set-preferences/menu-mode.png)

- Quando o menu do portal está no modo **Flyout,** está escondido até precisar. Selecione o ícone do menu para abrir ou fechar o menu.

- Se escolher **o modo Docked** para o menu do portal, é sempre visível. Pode colapsar o menu para fornecer mais espaço de trabalho.

## <a name="choose-a-theme-or-enable-high-contrast"></a>Escolha um tema ou permita um alto contraste

O tema que escolhe afeta as cores de fundo e de fonte que aparecem no portal Azure. Pode selecionar entre um dos quatro temas de cores predefinidos. Selecione cada miniatura para encontrar o tema que melhor lhe convém.

Em alternativa, pode escolher um dos temas de alto contraste. Os temas de alto contraste tornam o portal Azure mais fácil de ler para pessoas com deficiência visual; sobrepõem-se a todas as outras seleções temáticas.

![Screenshot mostrando definições do portal Azure com temas em destaque](./media/set-preferences/theme.png)

## <a name="enable-or-disable-pop-up-notifications"></a>Ativar ou desativar notificações pop-up

As notificações são mensagens do sistema relacionadas com a sessão atual. Eles fornecem informações como o seu saldo de crédito atual, quando os recursos que acabou de criar ficam disponíveis, ou confirmam a sua última ação, por exemplo. Quando as notificações pop-up são ligadas, as mensagens são exibidas brevemente no canto superior do ecrã. 

Para ativar ou desativar notificações pop-up, selecione ou limpe **As notificações pop-up**.

![Screenshot mostrando as definições do portal Azure com notificações pop-up em destaque](./media/set-preferences/popup-notifications.png)

Para ler todas as notificações recebidas durante a sessão atual, **selecione Notificações** do cabeçalho global.

![Screenshot mostrando cabeçalho global do portal Azure com notificações em destaque](./media/set-preferences/read-notifications.png)

Se pretender ler notificações de sessões anteriores, procure eventos no registo de Atividades. Para mais informações, consulte [o registo de Atividades.](../azure-monitor/essentials/activity-log.md#view-the-activity-log) 

## <a name="change-the-inactivity-timeout-setting"></a>Alterar a definição de tempo de inatividade

A definição de tempo de inatividade ajuda a proteger os recursos de acesso não autorizado se se esquecer de proteger a sua estação de trabalho. Depois de ter estado inativo durante algum tempo, é automaticamente contratado para fora da sessão do portal Azure. Como indivíduo, pode alterar a definição de tempo limite para si mesmo. Se for administrador, pode defini-lo ao nível do diretório para todos os seus utilizadores no diretório.

### <a name="change-your-individual-timeout-setting-user"></a>Altere a definição de tempo limite individual (utilizador)

Selecione o drop-down em **Sign me out quando estiver inativo**. Escolha a duração após a qual a sua sessão do portal Azure está assinada se estiver inativa.

![Screenshot mostrando definições do portal com definições de tempo limite inativas em destaque](./media/set-preferences/inactive-signout-user.png)

A alteração é guardada automaticamente. Se estiver inativo, a sessão do portal Azure será assinalada após a duração definida.

Se o seu administrador tiver ativado uma política de tempo de inatividade, ainda pode definir a sua própria, desde que seja inferior à definição de nível de diretório. Selecione **Override a política de tempo de tempo de inatividade do diretório** e, em seguida, desaperte um intervalo de tempo.

![Screenshot mostrando definições do portal com sobreposição da definição de política de tempo de inatividade do diretório realçada](./media/set-preferences/inactive-signout-override.png)

### <a name="change-the-directory-timeout-setting-admin"></a>Alterar a definição de tempo limite de diretório (administrador)

Os administradores na [função de Administrador Global](../active-directory/roles/permissions-reference.md#global-administrator) podem impor o tempo máximo de inatividade antes de uma sessão ser assinada. A definição de tempo de inatividade aplica-se ao nível do diretório. A definição entra em vigor para novas sessões. Não se aplicará imediatamente a utilizadores que já estejam inscritos. Para obter mais informações sobre diretórios, consulte a [Visão geral dos Serviços de Domínio do Diretório Ativo.](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)

Se você é um Administrador Global, e deseja impor uma definição de tempo de tempo ocioso para todos os utilizadores do portal Azure, siga estes passos:

1. Selecione o tempo limite **de tempo do diretório de configuração do** texto de ligação .

    ![Screenshot mostrando as definições do portal com texto de ligação realçado](./media/set-preferences/settings-admin.png)

1. Na página de **tempo limite de tempo de inatividade do nível de diretório configurar,** selecione Ativar o tempo limite de marcha lenta para o portal **Azure** para ativar a definição.

1. Em seguida, insira as **Horas** e **Minutos** pelo tempo máximo que um utilizador pode ficar inativo antes da sua sessão ser automaticamente assinada.

1. Selecione **Aplicar**.

    ![Screenshot mostrando página para definir tempo de inatividade de nível de diretório](./media/set-preferences/configure.png)

Para confirmar que a política de tempo limite de inatividade está corretamente definida, selecione **Notificações** do cabeçalho da página global. Verifique se está listada uma notificação de sucesso.

![Screenshot mostrando mensagem de notificação bem sucedida para tempo de inatividade ao nível do diretório](./media/set-preferences/confirmation.png)

## <a name="restore-default-settings"></a>Restaurar predefinições

Se tiver escamado as definições do portal Azure e quiser descartá-las, selecione **Restaurar as definições predefinidas**. Quaisquer alterações que tenha feito nas definições do portal serão perdidas. Esta opção não afeta as personalizações do dashboard.

![Screenshot mostrando a restauração das definições predefinidos](./media/set-preferences/useful-links-restore-defaults.png)

## <a name="export-user-settings"></a>Definições de utilizador de exportação

As informações sobre as suas definições personalizadas são armazenadas no Azure. Pode exportar os seguintes dados do utilizador:

* Dashboards privados no portal Azure
* Definições de utilizadores como subscrições ou diretórios favoritos e último diretório iniciado
* Temas e outras configurações personalizadas do portal

É uma boa ideia exportar e rever as suas definições se planeia eliminá-las. A reconstrução de painéis ou a remodelação das definições podem ser morosas.

Para exportar as definições do seu portal, **selecione Exporte todas as definições**.

![Screenshot mostrando exportação de configurações](./media/set-preferences/useful-links-export-settings.png)

As definições de exportação criam um ficheiro *.json* que contém as definições do utilizador como o seu tema de cor, favoritos e dashboards privados. Devido à natureza dinâmica das configurações do utilizador e ao risco de corrupção de dados, não é possível importar configurações a partir do ficheiro *.json.*

## <a name="delete-user-settings-and-dashboards"></a>Eliminar as definições do utilizador e os dashboards

As informações sobre as suas definições personalizadas são armazenadas no Azure. Pode eliminar os seguintes dados do utilizador:

* Dashboards privados no portal Azure
* Definições de utilizadores como subscrições ou diretórios favoritos e último diretório iniciado
* Temas e outras configurações personalizadas do portal

É uma boa ideia exportar e rever as suas definições antes de as apagar. Reconstruir painéis ou refazer as definições personalizadas pode ser demorado.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Para eliminar as definições do seu portal, **selecione Eliminar todas as definições e dashboards privados**.

![Screenshot mostrando excluir as definições](./media/set-preferences/useful-links-delete-settings.png)

## <a name="change-language-and-regional-settings"></a>Alterar a linguagem e as configurações regionais

Existem duas definições que controlam a forma como o texto no portal Azure aparece: 
- A definição **de idioma** controla o idioma que vê para texto no portal Azure. 

- **O formato regional** controla a forma como são mostradas as datas, a hora, os números e a moeda.

Para alterar o idioma que é usado no portal Azure, use o drop-down para selecionar a partir da lista de idiomas disponíveis.

A seleção de formato regional muda para exibir opções regionais apenas para o idioma que selecionou. Para alterar essa seleção automática, utilize o drop-down para escolher o formato regional que pretende.

Por exemplo, se selecionar o inglês como língua e, em seguida, selecionar estados unidos como formato regional, a moeda é mostrada em dólares americanos. Se selecionar o inglês como língua e selecionar a Europa como formato regional, a moeda é mostrada em euros.

Selecione **Aplicar** para atualizar as definições do seu idioma e formato regional.

   ![Screenshot mostrando definições de linguagem e formato regional](./media/set-preferences/language.png)

>[!NOTE]
>Estas configurações linguísticas e regionais afetam apenas o portal Azure. Os links de documentação que abrem num novo separador ou janela utilizam as definições de idioma do seu navegador para determinar o idioma a exibir.
>

## <a name="next-steps"></a>Passos seguintes

- [Atalhos de teclado no portal Azure](azure-portal-keyboard-shortcuts.md)
- [Dispositivos e browsers suportados](azure-portal-supported-browsers-devices.md)
- [Adicione, remova e reorganize os favoritos](azure-portal-add-remove-sort-favorites.md)
- [Criar e partilhar dashboards personalizados](azure-portal-dashboards.md)
- [Séries de vídeos de procedimentos do portal do Azure](azure-portal-video-series.md)
