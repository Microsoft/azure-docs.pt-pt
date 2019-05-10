---
title: Personalizar o Azure IoT Central da interface do Usuário | Documentos da Microsoft
description: Como personalizar as ligações de tema e ajuda para a sua aplicação de central de IoT do Azure
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0256396cd228898f3852772b113e6064a0656746
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237661"
---
# <a name="customize-the-azure-iot-central-ui"></a>Personalizar o Azure IoT Central da interface do Usuário 

*Este artigo aplica-se aos administradores.*

IoT Central permite-lhe personalizar a interface do Usuário do seu aplicativo ao aplicar temas personalizados e modificar as ligações de ajuda para apontar para seus próprios recursos de ajuda personalizada. Captura de ecrã seguinte mostra uma página usando o tema padrão:

![Tema padrão do Centro de IoT](./media/howto-customize-ui/standard-ui.png)

Captura de ecrã seguinte mostra uma página usando uma captura de tela personalizada com os elementos de interface do Usuário personalizados realçados:

![Tema Central de IoT personalizado](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Criar o tema

Para criar um tema personalizado, navegue para o **personalizar seu aplicativo** página no **administração** secção:

![Temas do Centro de IoT](./media/howto-customize-ui/themes.png)

Nesta página, pode personalizar os seguintes aspetos da sua aplicação:

### <a name="application-logo"></a>Logótipo da aplicação

Uma imagem PNG, não maior do que 1 MB, com um plano de fundo transparente. Este logótipo apresenta para a esquerda na barra de título do aplicativo de IoT Central.

Se a sua imagem de logótipo inclui o nome do seu aplicativo, pode ocultar o texto do nome de aplicação. Para obter mais informações, consulte [gerir definições da aplicação](./howto-administer.md#manage-application-settings).

### <a name="browser-icon-favicon"></a>Ícone de browser (favicon)

Uma imagem PNG, não maior do que 32 x 32 pixéis, com um plano de fundo transparente. Um navegador da web pode utilizar esta imagem na barra de endereço, histórico, indicadores e separador do browser.

### <a name="browser-colors"></a>Cores de browser

Pode alterar a cor do cabeçalho da página e a cor utilizada para accenting botões e outros destaques. Utilizar um valor de cor hexadecimal de seis caracteres no formato `##ff6347`. Para obter mais informações sobre **valor HEXA** notação de cor, consulte [HTML cores](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Sempre pode reverter para as opções predefinidas no **personalizar seu aplicativo** página.

### <a name="changes-for-operators"></a>Alterações para os operadores

Se um administrador cria um tema personalizado, operadores e outros utilizadores da sua aplicação já não podem escolher um tema no **definições**.

## <a name="replace-help-links"></a>Substitua as ligações de ajuda

Para fornecer informações de ajuda personalizada para os operadores e outros utilizadores, pode modificar os links no aplicativo **ajudar** menu.

Para modificar as ligações de ajuda, navegue para o **personalizar ajuda** página no **administração** secção:

![Personalizar as ligações de ajuda do Centro de IoT](./media/howto-customize-ui/help-links.png)

Também pode adicionar novas entradas ao menu de ajuda e remover as entradas predefinidas:

![Ajuda personalizada do Centro de IoT](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Sempre pode reverter para as ligações de ajuda padrão no **personalizar ajuda** página.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como personalizar a interface do Usuário em seu aplicativo de IoT Central, seguem-se alguns passos seguintes sugeridos:

> [!div class="nextstepaction"]
> [Administrar o seu aplicativo](./howto-administer.md)
> [configurar o dashboard de aplicações](./howto-configure-homepage.md)