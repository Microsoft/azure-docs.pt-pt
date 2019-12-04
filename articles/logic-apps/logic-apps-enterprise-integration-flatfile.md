---
title: Codificar ou decodificar arquivos simples
description: Codificar ou decodificar arquivos simples para integração corporativa com os aplicativos lógicos do Azure e Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 53d9935f68c21238cb4f90f78e543a7ab343ced2
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792529"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Codificar ou decodificar arquivos simples com aplicativos lógicos do Azure e Enterprise Integration Pack

Talvez você queira codificar o conteúdo XML antes de enviá-lo para um parceiro de negócios em um cenário B2B (entre empresas). Em um aplicativo lógico, você pode usar o conector de codificação de arquivo simples para fazer isso. O aplicativo lógico que você cria pode obter seu conteúdo XML de uma variedade de fontes, incluindo de um gatilho de solicitação HTTP, de outro aplicativo ou até mesmo de um dos muitos [conectores](../connectors/apis-list.md). Para obter mais informações sobre aplicativos lógicos, consulte a [documentação dos aplicativos lógicos](logic-apps-overview.md "Saiba mais sobre os aplicativos lógicos").  

## <a name="create-the-flat-file-encoding-connector"></a>Criar o conector de codificação de arquivo simples
Siga estas etapas para adicionar um conector de codificação de arquivo simples ao seu aplicativo lógico.

1. Crie um aplicativo lógico e [vincule-o à sua conta de integração](logic-apps-enterprise-integration-accounts.md "Saiba como vincular uma conta de integração a um aplicativo lógico"). Essa conta contém o esquema que você usará para codificar os dados XML.  
1. Adicione um gatilho **solicitação-quando uma solicitação HTTP é recebida** para seu aplicativo lógico.  
   ![captura de tela do gatilho para selecionar](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
1. Adicione a ação codificação de arquivo simples, da seguinte maneira:
   
    a. Selecione o sinal de **adição** .
   
    b. Selecione o link **Adicionar uma ação** (aparece depois de selecionar o sinal de adição).
   
    c. Na caixa de pesquisa, insira *plano* para filtrar todas as ações para aquela que você deseja usar.
   
    d. Selecione a opção **codificação de arquivo simples** na lista.   
   ![captura de tela da opção de codificação de arquivo simples](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
1. Na caixa de diálogo **codificação de arquivo simples** , selecione a caixa de texto **conteúdo** .  
   ![captura de tela da caixa de texto de conteúdo](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
1. Selecione a marca body como o conteúdo que você deseja codificar. A marca body preencherá o campo Content.     
   ![Captura de tela da marca body](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
1. Selecione a caixa de listagem **nome do esquema** e escolha o esquema que você deseja usar para codificar o conteúdo de entrada.    
   ![captura de tela da caixa de listagem nome do esquema](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
1. Guarde o seu trabalho.   
   ![Captura de tela do ícone salvar](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Neste ponto, você concluiu a configuração do conector de codificação de arquivo simples. Em um aplicativo do mundo real, talvez você queira armazenar os dados codificados em um aplicativo de linha de negócios, como o Salesforce. Ou você pode enviar esses dados codificados para um parceiro comercial. Você pode adicionar facilmente uma ação para enviar a saída da ação de codificação para o Salesforce ou para seu parceiro comercial, usando qualquer um dos outros conectores fornecidos.

Agora você pode testar seu conector fazendo uma solicitação para o ponto de extremidade HTTP e incluindo o conteúdo XML no corpo da solicitação.  

## <a name="create-the-flat-file-decoding-connector"></a>Criar o conector de decodificação de arquivo simples

> [!NOTE]
> Para concluir essas etapas, você precisa ter um arquivo de esquema já carregado na conta de integração.

1. Adicione um gatilho **solicitação-quando uma solicitação HTTP é recebida** para seu aplicativo lógico.  
   ![captura de tela do gatilho para selecionar](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
1. Adicione a ação de decodificação de arquivo simples, da seguinte maneira:
   
    a. Selecione o sinal de **adição** .
   
    b. Selecione o link **Adicionar uma ação** (aparece depois de selecionar o sinal de adição).
   
    c. Na caixa de pesquisa, insira *plano* para filtrar todas as ações para aquela que você deseja usar.
   
    d. Selecione a opção **decodificação de arquivo simples** na lista.   
   ![captura de tela da opção de decodificação de arquivo simples](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
1. Selecione o controle de **conteúdo** . Isso produz uma lista do conteúdo de etapas anteriores que você pode usar como o conteúdo a ser decodificado. Observe que o *corpo* da solicitação HTTP de entrada está disponível para ser usado como o conteúdo a ser decodificado. Você também pode inserir o conteúdo para decodificar diretamente no controle de **conteúdo** .     
1. Selecione a marca *Body* . Observe que a marca body agora está no controle **Content** .
1. Selecione o nome do esquema que você deseja usar para decodificar o conteúdo. A captura de tela a seguir mostra que *orderfile* é o nome de esquema selecionado. Esse nome de esquema foi carregado anteriormente na conta de integração.
   
   ![Captura de tela da caixa de diálogo decodificação de arquivo simples](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
1. Guarde o seu trabalho.  
   ![Captura de tela do ícone salvar](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Neste ponto, você concluiu a configuração do conector de decodificação de arquivo simples. Em um aplicativo real, talvez você queira armazenar os dados decodificados em um aplicativo de linha de negócios, como o Salesforce. Você pode adicionar facilmente uma ação para enviar a saída da ação de decodificação para o Salesforce.

Agora você pode testar seu conector fazendo uma solicitação para o ponto de extremidade HTTP e incluindo o conteúdo XML que você deseja decodificar no corpo da solicitação.  

## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack").  

