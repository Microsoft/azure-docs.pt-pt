---
title: Codificar ou descodificar ficheiros simples - Azure Logic Apps | Documentos da Microsoft
description: Codificar ou descodificar ficheiros simples para a integração empresarial com o Azure Logic Apps e o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.date: 07/08/2016
ms.openlocfilehash: d0ef61b94d7bd604b6c0062341224510f3048c57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61467314"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Codificar ou descodificar ficheiros simples com o Azure Logic Apps e o Enterprise Integration Pack

Pode querer codificar o conteúdo XML antes de enviar a um parceiro de negócios num cenário do empresa-empresa (B2B). Numa aplicação lógica, pode utilizar o conector de codificação de ficheiro simples de fazer isso. A aplicação lógica que criar pode obter o seu XML conteúdo de uma variedade de origens, incluindo a partir de um acionador de pedido HTTP, de outro aplicativo ou mesmo a partir de um dos muitos [conectores](../connectors/apis-list.md). Para obter mais informações sobre o logic apps, consulte a [documentação do logic apps](logic-apps-overview.md "Saiba mais sobre o Logic apps").  

## <a name="create-the-flat-file-encoding-connector"></a>Criar o conector de codificação de ficheiro simples
Siga estes passos para adicionar um conector para a sua aplicação lógica de codificação de arquivo simples.

1. Criar uma aplicação lógica e [ligá-lo à sua conta de integração](logic-apps-enterprise-integration-accounts.md "aprender a ligar uma conta de integração para uma aplicação lógica"). Esta conta contém o esquema que irá utilizar para codificar os dados XML.  
1. Adicionar uma **pedido - pedido de HTTP de uma quando é recebido** acionador à sua aplicação lógica.  
   ![Captura de ecrã do acionador para selecionar](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
1. Adicione o ficheiro simples a codificação de ação, da seguinte forma:
   
    a. Selecione o **plus** início de sessão.
   
    b. Selecione o **adicionar uma ação** link (aparece depois de selecionar o sinal de adição).
   
    c. Na caixa de pesquisa, introduza *simples* para filtrar todas as ações para aquele que pretende utilizar.
   
    d. Selecione o **codificação de ficheiro simples** opção na lista.   
   ![Opção de captura de ecrã de simples codificação do ficheiro](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
1. Sobre o **codificação de ficheiro simples** caixa de diálogo, selecione a **conteúdo** caixa de texto.  
   ![Caixa de texto de captura de ecrã do conteúdo](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
1. Selecione a marca do corpo como o conteúdo que pretende codificar. A etiqueta body irá preencher o campo de conteúdo.     
   ![Captura de ecrã da marca body](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
1. Selecione o **nome do esquema** caixa de listagem e escolher o esquema que pretende utilizar para codificar o conteúdo de entrada.    
   ![Caixa de listagem de captura de ecrã do nome do esquema](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
1. Guarde o seu trabalho.   
   ![Ícone de captura de ecrã de guardar](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Neste ponto, estará pronto a configurar o conector de codificação de ficheiro simples. Num aplicativo do mundo real, pode querer armazenar os dados codificados num aplicativo de linha de negócio, como o Salesforce. Ou pode enviar a que dados codificados para uma troca de parceiros. Pode adicionar facilmente uma ação para enviar a saída da ação de codificação para o Salesforce ou para o seu parceiro comercial, utilizando qualquer um dos outros conectores fornecido.

Agora pode testar o conector ao efetuar um pedido para o ponto final HTTP e incluindo o conteúdo XML no corpo do pedido.  

## <a name="create-the-flat-file-decoding-connector"></a>Criar o ficheiro simples de decodificação de conector

> [!NOTE]
> Para concluir estes passos, tem de ter um ficheiro de esquema que já carregou para a conta de integração.

1. Adicionar uma **pedido - pedido de HTTP de uma quando é recebido** acionador à sua aplicação lógica.  
   ![Captura de ecrã do acionador para selecionar](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
1. Adicione o ficheiro simples decodificação ação, da seguinte forma:
   
    a. Selecione o **plus** início de sessão.
   
    b. Selecione o **adicionar uma ação** link (aparece depois de selecionar o sinal de adição).
   
    c. Na caixa de pesquisa, introduza *simples* para filtrar todas as ações para aquele que pretende utilizar.
   
    d. Selecione o **descodificação de ficheiro simples** opção na lista.   
   ![Opção de captura de ecrã de simples ficheiro descodificação](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
1. Selecione o **conteúdo** controle. Isso produz uma lista de conteúdos dos passos anteriores, que pode utilizar como o conteúdo a descodificar. Tenha em atenção que o *corpo* de entrada HTTP pedido está disponível para ser utilizado como o conteúdo a descodificar. Também pode introduzir o conteúdo para descodificar diretamente para o **conteúdo** controle.     
1. Selecione o *corpo* marca. Tenha em atenção a etiqueta body está agora no **conteúdo** controle.
1. Selecione o nome do esquema que pretende utilizar para decodificar o conteúdo. Captura de ecrã seguinte mostra que *OrderFile* é o nome de esquema selecionado. Este nome de esquema tinha sido carregado anteriormente para a conta de integração.
   
   ![Captura de ecrã de simples ficheiro Decodificação da caixa de diálogo](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
1. Guarde o seu trabalho.  
   ![Ícone de captura de ecrã de guardar](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Neste momento, tiver concluído a configuração de seu arquivo simples decodificar o conector. Num aplicativo do mundo real, pode querer armazenar os dados decodificados num aplicativo de linha de negócio como o Salesforce. Pode adicionar facilmente uma ação para enviar a saída da ação decodificação ao Salesforce.

Agora pode testar o seu conector ao efetuar um pedido para o ponto final HTTP e incluir o conteúdo XML que pretende descodificar no corpo do pedido.  

## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack").  

