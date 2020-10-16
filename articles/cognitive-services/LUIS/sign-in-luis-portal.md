---
title: Iniciar sessão no portal LUIS
description: Se for um novo utilizador a iniciar sessão no portal LUIS, a experiência de inscrição será ligeiramente diferente com base na sua conta de utilizador corrente.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: a-sakand
author: skandil
ms.openlocfilehash: d801971ca62c416c66608b40aab3e8052fe941a1
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931415"
---
# <a name="sign-in-to-luis-portal"></a>Iniciar sessão no portal LUIS

Se for um novo utilizador a iniciar sessão no portal LUIS, a experiência de inscrição será ligeiramente diferente com base na sua conta de utilizador atual:
  * Associado a uma assinatura Azure
  * Não associado a uma assinatura Azure

## <a name="determine-account-type"></a>Determinar o tipo de conta

Quando iniciar sutilizá-lo pela primeira vez no portal LUIS, utilize os seguintes indicadores visuais para determinar o tipo de conta.

### <a name="account-without-azure-subscription"></a>Conta sem subscrição do Azure

Uma conta, que não está associada a uma subscrição do Azure, tem o ícone Azure na barra de navegação de cima para a direita. Uma vez migrado para o tipo de conta associado, o ícone já não aparece.

:::image type="content" source="media/sign-in/sign-in-with-account-without-azure-subscription.png" alt-text="Imagem parcial da barra de navegação LUIS com ícone Azure.":::

### <a name="account-with-azure-subscription"></a>Conta com subscrição do Azure

Uma conta associada a uma subscrição do Azure permite-lhe selecionar a sua subscrição e recurso para utilizar.

:::image type="content" source="media/sign-in/resource-selection.png" alt-text="Imagem parcial da barra de navegação LUIS com ícone Azure.":::

## <a name="sign-in-with-account-associated-with-an-azure-subscription"></a>Inscreva-se com conta associada a uma subscrição do Azure

1. Inscreva-se no [portal LUIS](https://www.luis.ai) e concorde com os termos de utilização.

1. Terá duas opções de inscrição:

    * Continue a utilizar um recurso Azure, que é o caminho recomendado e em breve será o único caminho disponível. Este caminho permite-lhe ligar a sua conta LUIS a um recurso de Autoria Azure enquanto se inscreve, escolhendo um recurso existente na sua subscrição ou criando um novo recurso. Isto equivale a inscrever-se migrado sem a necessidade de passar pelo processo de [migração](luis-migration-authoring.md#what-is-migration) mais tarde. Todos os utilizadores serão obrigados a migrar até 2 de novembro de 2020.

    * Continue a utilizar a chave de arranque ou de teste. Este caminho permite-lhe iniciar sessão no LUIS com o arranque ou o recurso experimental que está a ser fornecido sem ter de criar quaisquer recursos. Se escolher este caminho, acabará por ser obrigado a [migrar](luis-migration-authoring.md#migration-steps) a sua conta e a ligar as suas aplicações a um recurso de autoria. É por isso que é recomendável escolher o caminho onde continua com o seu recurso Azure.

    [Saiba mais sobre as teclas de autoria e arranque.](luis-how-to-azure-subscription.md#luis-resources) Ambos os recursos dão-lhe 1 milhão de transações de autoria gratuitas e 1000 transações de ponto final de previsão gratuita.

    :::image type="content" source="media/sign-in/signup-landing-page.png" alt-text="Imagem parcial da barra de navegação LUIS com ícone Azure.":::

1. Utilize um recurso de autoria existente

    :::image type="content" source="media/sign-in/signup-choose-resource.png" alt-text="Imagem parcial da barra de navegação LUIS com ícone Azure.":::

    Se já tem recursos de autoria LUIS na sua subscrição e associa um à sua conta LUIS durante a sing-in, escolha a opção **Utilização do Recurso de Autoria existente** e forneça as seguintes informações:

    * **Inquilino** - o inquilino a que a sua assinatura Azure está associada. Não poderá trocar os inquilinos da janela existente. Você pode trocar de inquilino selecionando o avatar mais à direita, que contém as suas iniciais na barra superior.
    * **Nome da assinatura** - a subscrição que será associada ao recurso. Se tiver mais do que uma subscrição que pertença ao seu inquilino, selecione a que deseja da lista de espera.
    * **Nome do recurso** - O recurso de autoria a que pretende estar associado.

    > [!Note]
    > Se o recurso de autoria que procura está acinzentado na lista de abandono, isso significa que se inscreveu num portal regional diferente. [Compreender o conceito de portais regionais.](luis-reference-regions.md#luis-authoring-regions)

1. Criar um novo recurso de autoria

    :::image type="content" source="media/sign-in/signup-create-resource.png" alt-text="Imagem parcial da barra de navegação LUIS com ícone Azure.":::

    Ao **criar um novo recurso de autoria,** forneça as seguintes informações:

    * **Inquilino** - o inquilino a que a sua assinatura Azure está associada. Não poderá trocar os inquilinos da janela existente. Você pode trocar de inquilino selecionando o avatar mais à direita, que contém as suas iniciais na barra superior.
    * **Nome do recurso** - um nome personalizado que escolher, usado como parte do URL para as suas transações de autoria. O seu nome de recurso só pode incluir caracteres alfanuméricos, '-', e não pode começar ou terminar com '-'. Se outros símbolos estiverem incluídos no nome, a criação de um recurso falhará.
    * **Nome da assinatura** - a subscrição que será associada ao recurso. Se tiver mais do que uma subscrição que pertença ao seu inquilino, selecione a que deseja da lista de espera.
    * **Grupo de recursos** - um nome de grupo de recursos personalizado que escolher na sua subscrição. Os grupos de recursos permitem-lhe agrupar recursos Azure para acesso e gestão. Se atualmente não tiver um grupo de recursos na sua subscrição, não poderá criar um no portal LUIS. Vá ao [portal Azure](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) para criar um e depois vá ao LUIS para continuar o processo de inscrição.

1. Depois de escolher o seu caminho, pode demorar alguns segundos até que apareça um sinal que diga "A sua conta foi migrada com sucesso. Termine selecionando **Continue**.

    :::image type="content" source="media/sign-in/signup-confirm-2.png" alt-text="Imagem parcial da barra de navegação LUIS com ícone Azure.":::

    > [!Note]
    > Se tiver uma subscrição e pelo menos um recurso de autoria na região igual ao que está a inscrever-se no portal, poderá iniciar sessão automática na LUIS migrada e associada a um recurso sem a necessidade de escolher qual o caminho a seguir.


## <a name="sign-in-with-user-account-not-associated-with-an-azure-subscription"></a>Inscreva-se com conta de utilizador não associada a uma subscrição do Azure

1. Inscreva-se no [portal LUIS](https://www.luis.ai) e verifique se concorda com os termos de utilização.

1. Termine selecionando **Continue**. Iniciará automaticamente o sôm- se-á com uma chave de teste/arranque. Isto significa que eventualmente será obrigado a [migrar](luis-migration-authoring.md#migration-steps) a sua conta e a ligar as suas aplicações a um recurso de autoria. Para passar pelo processo de migração, terá de se inscrever para um [Azure Free Trial](https://azure.microsoft.com/free/).

    :::image type="content" source="media/sign-in/signup-no-subscription.png" alt-text="Imagem parcial da barra de navegação LUIS com ícone Azure.":::

## <a name="troubleshooting"></a>Resolução de problemas

* Se criar um recurso de autoria a partir do portal Azure numa região diferente do portal a que está a iniciar sessão, o recurso de autoria será acinzentado.
* Ao criar um novo recurso, certifique-se de que o nome do recurso apenas inclui caracteres alfanuméricos, '-', e não pode começar ou terminar com '-'. Caso contrário, falhará.
* Certifique-se de que tem as [permissões adequadas na sua subscrição para criar um recurso Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Se não tiver as permissões adequadas, contacte a administração da sua subscrição para lhe dar permissões suficientes.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [iniciar uma nova aplicação](luis-how-to-start-new-app.md)
