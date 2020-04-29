---
title: Codificar ou descodificar ficheiros planos
description: Codificar ou descodificar ficheiros planos para integração empresarial com aplicações da Lógica Azure e pacote de integração empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 2d6182ba01507c2fb361628e01bb52e1ea821f44
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77152657"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Codificar ou descodificar ficheiros planos com aplicações da Lógica Azure e pacote de integração empresarial

Pode querer codificar o conteúdo xml antes de enviá-lo a um parceiro de negócios num cenário de negócio-a-negócio (B2B). Numa aplicação lógica, pode utilizar o conector de codificação de ficheiros planos para o fazer. A aplicação lógica que cria pode obter o seu conteúdo XML a partir de uma variedade de fontes, incluindo a partir de um gatilho de pedido HTTP, de outra aplicação, ou mesmo de um dos muitos [conectores.](../connectors/apis-list.md) Para obter mais informações sobre aplicações lógicas, consulte a documentação das [aplicações lógicas.](logic-apps-overview.md "Saiba mais sobre aplicações lógicas")  

## <a name="create-the-flat-file-encoding-connector"></a>Criar o conector de codificação de ficheiros planos
Siga estes passos para adicionar um conector de codificação de ficheiros planos à sua aplicação lógica.

1. Crie uma aplicação lógica e [ligue-a à sua conta de integração.](logic-apps-enterprise-integration-accounts.md "Aprenda a ligar uma conta de integração a uma aplicação Lógica") Esta conta contém o esquema que utilizará para codificar os dados XML.  

1. No Logic App Designer, adicione o **pedido Quando um PEDIDO HTTP é recebido** gatilho para a sua aplicação lógica.

1. Adicione a ação de codificação de ficheiros planos, da seguinte forma:

   a. Selecione o sinal **de mais.**

   b. Selecione o link **Adicionar um** link de ação (aparece depois de ter selecionado o sinal de mais).

   c. Na caixa de pesquisa, introduza *o Flat* para filtrar todas as ações para aquela que pretende utilizar.

   d. Selecione a opção de codificação de **ficheiroplano plano** da lista.   

      ![Screenshot da opção de codificação de ficheiroplano plano](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Na caixa de diálogo de codificação de **ficheiroplano plano,** selecione a caixa de texto **Content.**  

   ![Screenshot da caixa de texto de conteúdo](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  

1. Selecione a etiqueta corporal como o conteúdo que pretende codificar. A etiqueta corporal vai povoar o campo de conteúdo.     

   ![Screenshot da etiqueta do corpo](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  

1. Selecione a caixa da lista **de nomes Schema** e escolha o esquema que pretende utilizar para codificar o conteúdo de entrada.    

   ![Screenshot da caixa da lista de nomes de Schema](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  

1. Guarde o seu trabalho.

   ![Screenshot do ícone save](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Neste ponto, está acabado de configurar o seu conector de codificação de ficheiros planos. Numa aplicação no mundo real, pode querer armazenar os dados codificados numa aplicação de linha de negócio, como a Salesforce. Ou pode enviar esses dados codificados a um parceiro comercial. Pode facilmente adicionar uma ação para enviar a saída da ação de codificação à Salesforce, ou ao seu parceiro de negociação, utilizando qualquer um dos outros conectores fornecidos.

Agora pode testar o seu conector fazendo um pedido ao ponto final http, incluindo o conteúdo XML no corpo do pedido.  

## <a name="create-the-flat-file-decoding-connector"></a>Criar o conector de descodificação de ficheiros planos

> [!NOTE]
> Para completar estes passos, precisa de ter um ficheiro de esquemajá enviado para a sua conta de integração.

1. No Logic App Designer, adicione o **pedido Quando um PEDIDO HTTP é recebido** gatilho para a sua aplicação lógica.

1. Adicione a ação de descodificação de ficheiros planos, da seguinte forma:

   a. Selecione o sinal **de mais.**

   b. Selecione o link **Adicionar um** link de ação (aparece depois de ter selecionado o sinal de mais).

   c. Na caixa de pesquisa, introduza *o Flat* para filtrar todas as ações para aquela que pretende utilizar.

   d. Selecione a opção **dedescodificação de ficheiros planos** da lista.   

      ![Screenshot da opção de descodificação de ficheiros planos](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Selecione o controlo **de conteúdo.** Isto produz uma lista do conteúdo de passos anteriores que pode usar como conteúdo para descodificar. Note que o *Corpo* do pedido http que está disponível para ser usado como conteúdo para descodificar. Também pode introduzir o conteúdo para descodificar diretamente o controlo de **Conteúdo.**     

1. Selecione a etiqueta *Body.* Note que a etiqueta corporal está agora no controlo de **conteúdo.**

1. Selecione o nome do esquema que pretende utilizar para descodificar o conteúdo. A imagem que se segue mostra que o *OrderFile* é o nome de esquema selecionado. Este nome de esquema tinha sido enviado para a conta de integração anteriormente.

   ![Screenshot da caixa de diálogo de descodificação de ficheiroplano plano](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png) 

1. Guarde o seu trabalho.  

   ![Screenshot do ícone save](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Neste ponto, está acabado de configurar o seu conector de descodificação de ficheiros planos. Numa aplicação no mundo real, pode querer armazenar os dados descodificados numa aplicação de linha de negócio, como a Salesforce. Pode facilmente adicionar uma ação para enviar a saída da ação de descodificação para a Salesforce.

Agora pode testar o seu conector fazendo um pedido para o ponto final http e incluindo o conteúdo XML que pretende descodificar no corpo do pedido.  

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o Pacote de [Integração Empresarial.](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Pacote de Integração Empresarial")  

