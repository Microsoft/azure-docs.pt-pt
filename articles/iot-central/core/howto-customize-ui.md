---
title: Personalize o Azure IoT Central UI | Microsoft Docs
description: Como personalizar o tema e ajudar links para a sua aplicação central Azure IoT
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: cc3a90d078966df030bd772a9444c449d48249d4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057495"
---
# <a name="customize-the-azure-iot-central-ui"></a>Personalize o Azure IoT Central UI

Este artigo descreve como, como administrador, pode personalizar a UI da sua aplicação aplicando temas personalizados e modificando os links de ajuda para apontar para os seus próprios recursos de ajuda personalizados. 

A imagem que se segue mostra uma página utilizando o tema padrão:

![Tema padrão IoT Central](./media/howto-customize-ui/standard-ui.png)

A imagem a seguir mostra uma página usando uma imagem personalizada com os elementos de UI personalizados destacados:

![Tema central personalizado IoT](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Criar tema

Para criar um tema personalizado, navegue para personalizar a sua página **de aplicação** na secção **Administração:**

![Temas IoT Central](./media/howto-customize-ui/themes.png)

Nesta página, pode personalizar os seguintes aspetos da sua aplicação:

### <a name="application-logo"></a>Logotipo da aplicação

Uma imagem PNG, não maior que 1 MB, com um fundo transparente. Este logótipo apresenta à esquerda na barra de título de aplicação IoT Central.

Se a sua imagem de logotipo incluir o nome da sua aplicação, pode ocultar o texto do nome da aplicação. Para mais informações, consulte [Gerir a sua aplicação.](howto-administer.md#change-application-name-and-url)

### <a name="browser-icon-favicon"></a>Ícone do navegador (favicon)

Uma imagem PNG, não maior que 32 x 32 pixels, com um fundo transparente. Um navegador web pode usar esta imagem na barra de endereços, histórico, marcadores e separador de navegador.

### <a name="browser-colors"></a>Cores do navegador

Pode alterar a cor do cabeçalho da página e a cor utilizada para acentuar botões e outros destaques. Utilize um valor de cor hexáxola de seis caracteres no formato `##ff6347` . Para obter mais informações sobre a notação de cor **HEX Value,** consulte [as cores HTML](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Pode sempre voltar às opções predefinidas na página **Desativar a sua aplicação.**

### <a name="changes-for-operators"></a>Alterações para operadores

Se um administrador criar um tema personalizado, os operadores e outros utilizadores da sua aplicação já não podem escolher um tema em **Definições**.

## <a name="replace-help-links"></a>Substituir links de ajuda

Para fornecer informações de ajuda personalizadas aos seus operadores e outros utilizadores, pode modificar os links no menu **ajuda da** aplicação.

Para modificar os links de ajuda, navegue para a página **de ajuda Personalizar** na secção **Administração:**

![Personalize links de ajuda IoT Central](./media/howto-customize-ui/help-links.png)

Também pode adicionar novas entradas no menu de ajuda e remover entradas predefinidas:

![Ajuda central ioT personalizada](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Pode sempre voltar às ligações de ajuda predefinidas na página **de ajuda De Personalizar.**

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a personalizar a UI na sua aplicação IoT Central, aqui estão alguns passos sugeridos seguintes:

- [Administrar a sua aplicação](./howto-administer.md)
- [Adicionar mosaicos ao dashboard](howto-add-tiles-to-your-dashboard.md)