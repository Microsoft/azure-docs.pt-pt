---
title: Exportar uma aplicação Azure IoT Central | Microsoft Docs
description: Como gestor de soluções, quero exportar um modelo de candidatura para poder reutilizá-lo.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 15daaa7d64bb6719807d2930691ff9e9a4ca3902
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061473"
---
# <a name="export-your-application"></a>Exportar a aplicação

Este artigo descreve como, como gestor de soluções, exportar uma aplicação IoT Central para poder reutilizá-la.

Tem duas opções:

- Pode criar uma cópia da sua aplicação se apenas precisar de criar uma cópia duplicada da sua aplicação.
- Pode criar um modelo de aplicação a partir da sua aplicação se pretender criar várias cópias.

## <a name="copy-your-application"></a>Copie a sua aplicação

Pode criar uma cópia de qualquer aplicação, menos quaisquer instâncias do dispositivo, histórico de dados do dispositivo e dados do utilizador. A cópia usa um plano de preços padrão para o qual será cobrado. Não é possível criar uma aplicação que utilize o plano de preços gratuito copiando uma aplicação.

Selecione **Copiar**. Na caixa de diálogo, insira os detalhes da nova aplicação. Em seguida, **selecione Copy** para confirmar que deseja continuar. Para saber mais sobre os campos na forma, consulte o Criar um quickstart [de aplicação.](quick-deploy-iot-central.md)

![Screenshot que mostra a página de definições "Copy Application".](media/howto-use-app-templates/appcopy2.png)

Depois de a operação de cópia da aplicação ter sucesso, pode navegar para a nova aplicação utilizando o link.

![Página de definições de aplicação](media/howto-use-app-templates/appcopy3a.png)

Copiar uma aplicação também copia a definição de regras e ação por e-mail. Algumas ações, como As Aplicações Flow e Logic, estão ligadas a regras específicas através do ID de Regra. Quando uma regra é copiada para uma aplicação diferente, obtém o seu próprio ID de regra. Neste caso, os utilizadores terão de criar uma nova ação e depois associar a nova regra à sua. Em geral, é uma boa ideia verificar as regras e ações para garantir que estão atualizadas na nova app.

> [!WARNING]
> Se um dashboard incluir azulejos que exibem informações sobre dispositivos específicos, então esses azulejos mostram que o **recurso solicitado não foi encontrado** na nova aplicação. Tem de reconfigurar estes azulejos para apresentar informações sobre dispositivos na sua nova aplicação.

## <a name="create-an-application-template"></a>Criar um modelo de aplicação

Quando cria uma aplicação Azure IoT Central, tem uma escolha de modelos de amostra incorporados. Também pode criar os seus próprios modelos de aplicação a partir de aplicações IoT Central existentes. Em seguida, pode utilizar os seus próprios modelos de aplicação quando criar novas aplicações.

Quando cria um modelo de aplicação, inclui os seguintes itens da sua aplicação existente:

- O painel de aplicações padrão, incluindo o layout do painel de instrumentos e todos os azulejos que definiu.
- Modelos do dispositivo, incluindo medições, configurações, propriedades, comandos e painel de instrumentos.
- As regras. Todas as definições de regras estão incluídas. No entanto, as ações, exceto as ações de e-mail, não estão incluídas.
- Conjuntos de dispositivos, incluindo as suas condições e tabliers.

> [!WARNING]
> Se um dashboard incluir azulejos que exibem informações sobre dispositivos específicos, então esses azulejos mostram que o **recurso solicitado não foi encontrado** na nova aplicação. Tem de reconfigurar estes azulejos para apresentar informações sobre dispositivos na sua nova aplicação.

Quando cria um modelo de aplicação, não inclui os seguintes itens:

- Dispositivos
- Utilizadores
- Definições contínuas de exportação de dados

Adicione estes itens manualmente a quaisquer aplicações criadas a partir de um modelo de aplicação.

Para criar um modelo de aplicação a partir de uma aplicação IoT Central existente:

1. Vá à secção **de Administração** na sua candidatura.
1. Selecione **exportação de modelo de aplicação.**
1. Na página de **exportação do modelo de aplicação,** insira um nome e descrição para o seu modelo.
1. Selecione o **botão Exportação** para criar o modelo de aplicação. Agora pode copiar o **Link Shareable** que permite a alguém criar uma nova aplicação a partir do modelo:

![Criar um modelo de aplicação](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Use um modelo de aplicação

Para utilizar um modelo de aplicação para criar uma nova aplicação IoT Central, precisa de um **Link Shareable** previamente criado. Cole o **Link Shareable** na barra de endereços do seu navegador. A **configuração de uma** página de aplicação apresenta com o seu modelo de aplicação personalizado selecionado:

![Criar uma aplicação a partir de um modelo](media/howto-use-app-templates/create-app.png)

Selecione o seu plano de preços e preencha os outros campos no formulário. Em seguida, **selecione Criar** para criar uma nova aplicação IoT Central a partir do modelo de aplicação.

### <a name="manage-application-templates"></a>Gerir modelos de aplicação

Na página **de exportação do modelo de aplicação,** pode eliminar ou atualizar o modelo de aplicação.

Se eliminar um modelo de aplicação, já não pode utilizar o link partilhável anteriormente gerado para criar novas aplicações.

Para atualizar o seu modelo de aplicação, altere o nome do modelo ou descrição na página **de Exportação do Modelo de Aplicação.** Em seguida, selecione novamente o botão **Exportação.** Esta ação gera um novo **link Shareable** e invalida qualquer URL de **ligação partilhada** anterior.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar modelos de aplicação, o próximo passo sugerido é aprender a monitorizar a [saúde geral dos dispositivos ligados a uma aplicação IoT Central](howto-monitor-application-health.md)
