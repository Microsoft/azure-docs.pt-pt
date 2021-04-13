---
title: Programa de Dispositivos Certificados Azure - Tutorial - Adicionar detalhes do dispositivo
description: Um guia passo a passo para adicionar detalhes do dispositivo ao seu projeto no portal Azure Certified Device
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: f4f3d045a2530fa54d22bec789918454cba80097
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310432"
---
# <a name="tutorial-add-device-details"></a>Tutorial: Adicionar detalhes do dispositivo

Agora criou o seu projeto para o seu dispositivo, e está pronto para iniciar o processo de certificação! Primeiro, vamos adicionar os detalhes do seu dispositivo. Estas incluem especificações técnicas que os seus clientes poderão visualizar no catálogo de Dispositivos Certificados Azure e os detalhes de marketing que utilizarão para comprar uma vez que tenham uma decisão.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicione detalhes do dispositivo utilizando as funcionalidades de Componentes e Dependências
> * Faça upload de um guia Get Start para o seu dispositivo
> * Especifique detalhes de marketing para os clientes comprarem o seu dispositivo
> * Identificar opcionalmente quaisquer certificações do setor

## <a name="prerequisites"></a>Pré-requisitos

* Deverá fazer o seu contrato e ter um projeto para o seu dispositivo criado no [portal Azure Certified Device](https://certify.azure.com). Para mais informações, consulte o [tutorial.](tutorial-01-creating-your-project.md)
* Deverá ter um guia Get Started para o seu dispositivo em formato PDF. Fornecemos uma série de modelos Get Started para que possa utilizar, dependendo tanto do programa de certificação como do seu idioma preferido. Os modelos estão disponíveis na nossa [localização gitHub de modelos get.](https://aka.ms/GSTemplate "Começar modelos")

## <a name="adding-technical-device-details"></a>Adicionar detalhes técnicos do dispositivo

A primeira secção da sua página do projeto, denominada "Detalhes do dispositivo de entrada", permite-lhe fornecer informações sobre as capacidades de hardware fundamentais do seu dispositivo, tais como nome do dispositivo, descrição, processador, sistema operativo, opções de conectividade, interfaces de hardware, protocolos da indústria, dimensões físicas e muito mais. Embora muitos dos campos sejam opcionais, a maioria desta informação será disponibilizada a potenciais clientes no catálogo de Dispositivos Certificados Azure se optar por publicar o seu dispositivo depois de ter sido certificado.

1. Clique `Add` na secção 'Inserir detalhes do dispositivo' na página de resumo do seu projeto para abrir a secção de detalhes do dispositivo. Verá cinco secções para completar.

![Imagem da página de detalhes do projeto](./media/images/device-details-menu.png)

2. Reveja as informações que forneceu anteriormente quando criou o projeto no `Basics` separador.
1. Reveja as certificações que está a solicitar com o seu dispositivo no `Certifications` separador.
1. Abra a `Product details` lingueta e selecione pelo menos um sistema operativo.
1. Adicione **pelo menos** um componente discreto que descreva o seu dispositivo. Pode ver orientações adicionais sobre a utilização do componente [aqui.](how-to-using-the-components-feature.md)
1. Clique em `Save`. Poderá então editar o seu dispositivo componente e adicionar detalhes mais avançados.
1. Indique detalhes adicionais do dispositivo não capturados pelos detalhes do componente em `Additional product details` .
1. Se marcar `Other` em qualquer um dos campos componentes ou tiver uma circunstância especial que gostaria de sinalizar com a equipa de Certificação Azure, deixe um comentário esclarecedor na `Comments for reviewer` secção.
1. Utilize o `Dependencies` separador para listar quaisquer dependências se o seu dispositivo necessitar de hardware ou serviços adicionais para enviar dados para o Azure. Pode ver orientações adicionais sobre as dependências de listagem [aqui.](how-to-indirectly-connected-devices.md)
1. Uma vez satisfeito com a informação que forneceu, pode utilizar o `Review` separador apenas para uma visão geral do conjunto completo de detalhes do dispositivo que foram introduzidos.
1. Clique `Project summary` no topo da página para voltar à sua página de resumo.

![Rever página de detalhes do projeto](./media/images/sample-device-details.png)

## <a name="uploading-a-get-started-guide"></a>Upload de um guia Get Started

O guia Get Started é um documento PDF para simplificar a configuração e configuração e gestão do seu produto. O seu objetivo é tornar simples para os clientes conectar e suportar dispositivos no Azure usando o seu dispositivo. Como parte do processo de certificação, exigimos que os nossos parceiros forneçam **um** guia Get Started para o seu programa de certificação mais relevante.

1. Verifique duas vezes se forneceu todas as informações solicitadas no seu guia Get Started PDF de acordo com os [modelos](https://aka.ms/GSTemplate)fornecidos . O modelo que utiliza deve ser determinado pelo crachá de certificação para o que está a candidatar. (Por exemplo, um dispositivo IoT Plug and Play utilizará o modelo IoT Plug and Play. Os dispositivos que se aplicam *apenas* à certificação de base do dispositivo certificado Azure utilizarão o modelo de dispositivo certificado Azure.)
1. Clique `Add` na secção guia 'Get Started' da página de resumo do projeto.

![Imagem do botão GSG](./media/images/gsg-menu.png)

2. Clique em 'Escolher Ficheiro' para fazer o upload do seu PDF.
1. Reveja o documento na pré-visualização para formatação.
1. Guarde o seu upload clicando no botão 'Guardar'.
1. Clique `Project summary` no topo da página para voltar à sua página de resumo.

## <a name="providing-marketing-details"></a>Fornecer detalhes de marketing

Nesta área, irá fornecer informações de marketing prontas para o cliente para o seu dispositivo. Estes campos serão apresentados no catálogo de Dispositivos Certificados Azure se optar por publicar o seu dispositivo certificado.

1. Clique `Add` na secção 'Adicionar detalhes de marketing' para abrir a página de detalhes de marketing.

![Secção de detalhes de marketing](./media/images/marketing-details.png)

1. Faça upload de uma foto do produto em formato JPEG ou PNG que será usada no catálogo.
1. Escreva uma breve descrição do seu dispositivo que será exibida na página de descrição do produto do catálogo.
1. Indicar disponibilidade geográfica do seu dispositivo.
1. Forneça um link para a página de marketing do fabricante para este dispositivo. Este deve ser um link para um site que fornece informações adicionais sobre o dispositivo.
    > [!Note]
    > Certifique-se de que todos os URLs fornecidos são válidos ou estarão ativos no momento da publicação após aprovação.*)

1. Indique até 3 indústrias-alvo para as quais o seu dispositivo está otimizado.
1. Forneça informações para até 5 distribuidores do seu dispositivo. Isto pode incluir o próprio site do fabricante.

    > [!Note]
    > Se não for fornecido um URL da página do produto distribuidor, o `Shop` botão do catálogo ficará por defeito no link fornecido para `Distributor page` o qual pode não ser específico do dispositivo. Idealmente, o URL do distribuidor deve levar a uma página específica onde um cliente pode comprar um dispositivo, mas não é obrigatório. Se o distribuidor for o mesmo que o fabricante, este URL pode ser o mesmo que a página de marketing do fabricante.*)

1. Clique `Save` para confirmar a sua informação.
1. Clique `Project summary` no topo da página para voltar à sua página de resumo.

## <a name="declaring-additional-industry-certifications"></a>Declarar certificações adicionais da indústria

Também pode promover certificações adicionais da indústria que pode ter recebido para o seu dispositivo. Estas certificações podem ajudar a fornecer mais clareza sobre a utilização pretendida do seu dispositivo e serão pessizáveis no catálogo de Dispositivos Certificados Azure.

1. Clique `Add` na secção "Fornecer certificações da indústria".
1. Clique `Add a certification` para selecionar a partir de uma lista dos programas comuns de certificação da indústria. Se o seu produto tiver obtido uma certificação não na nossa lista, pode especificar um valor de cadeia personalizado selecionando `Other (please specify)` .
1. Opcionalmente, forneça uma descrição ou notas ao revisor. No entanto, estas notas não estarão disponíveis publicamente para visualizar no catálogo.
1. Clique `Save` para confirmar a sua informação.
1. Clique `Project summary` no topo da página para voltar à sua página de resumo.

## <a name="next-steps"></a>Passos seguintes

Agora que concluiu o processo de descrição do seu dispositivo! Isto ajudará a equipa de revisão de dispositivos certificados Azure e o seu cliente a compreender melhor o seu produto. Uma vez satisfeito com a informação que forneceu, está agora pronto para passar à fase de testes do processo de certificação.
> [!div class="nextstepaction"]
> [Tutorial: Testar o seu dispositivo](tutorial-03-testing-your-device.md)
