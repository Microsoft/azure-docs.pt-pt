---
title: Personalize a UI Central Azure IoT [ Microsoft Docs
description: Como personalizar o tema e ajudar links para a sua aplicação central Azure IoT
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8f76f143b6c6a26b88b78e20d8d5d8ae1ae48553
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80158029"
---
# <a name="customize-the-azure-iot-central-ui"></a>Personalize a UI Central Azure IoT

Este artigo descreve como, como administrador, pode personalizar o UI da sua aplicação aplicando temas personalizados e modificando os links de ajuda para apontar aos seus próprios recursos de ajuda personalizada. 



A imagem que se segue mostra uma página usando o tema padrão:

![Tema central padrão IoT](./media/howto-customize-ui/standard-ui.png)

A imagem seguinte mostra uma página usando uma imagem personalizada com os elementos UI personalizados destacados:

![Tema central personalizado ioT](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Criar tema

Para criar um tema personalizado, navegue para a página personalizar a **sua página de candidatura** na secção **Administração:**

![Temas centrais do IoT](./media/howto-customize-ui/themes.png)

Nesta página, pode personalizar os seguintes aspetos da sua aplicação:

### <a name="application-logo"></a>Logotipo de candidatura

Uma imagem png, não maior que 1 MB, com um fundo transparente. Este logótipo exibe à esquerda na barra de títulos de aplicação IoT Central.

Se a sua imagem de logotipo incluir o nome da sua aplicação, pode ocultar o texto do nome da aplicação. Para mais informações, consulte [Gerir a sua aplicação](howto-administer.md#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Ícone do navegador (favicon)

Uma imagem PNG, não maior do que 32 x 32 pixels, com um fundo transparente. Um navegador web pode usar esta imagem na barra de endereços, histórico, marcadores e separador do navegador.

### <a name="browser-colors"></a>Cores do navegador

Pode alterar a cor do cabeçalho da página e a cor utilizada para acentuar botões e outros destaques. Use um valor de cor hexaque de seis caracteres no formato `##ff6347`. Para obter mais informações sobre a notação de cor **HEX Value,** consulte [cores HTML](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Pode sempre voltar às opções predefinidas na página Personalizar a **sua aplicação.**

### <a name="changes-for-operators"></a>Alterações para os operadores

Se um administrador criar um tema personalizado, os operadores e outros utilizadores da sua aplicação já não podem escolher um tema em **Definições**.

## <a name="replace-help-links"></a>Substituir links de ajuda

Para fornecer informações personalizadas de ajuda aos seus operadores e outros utilizadores, pode modificar os links no menu **ajuda** da aplicação.

Para modificar os links de ajuda, navegue para a página de **ajuda Personalizar** na secção **Administração:**

![Personalizar ligações de ajuda IoT Central](./media/howto-customize-ui/help-links.png)

Também pode adicionar novas entradas ao menu de ajuda e remover as entradas predefinidas:

![Ajuda ioT central personalizada](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Pode sempre voltar aos links de ajuda predefinidos na página **de ajuda Personalizar.**

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a personalizar o UI na sua aplicação IoT Central, aqui estão alguns passos sugeridos:

- [Administrar a sua aplicação](./howto-administer.md)
- [Adicionar mosaicos ao dashboard](howto-add-tiles-to-your-dashboard.md)