---
title: Criar, preparar, publicar a base de dados de conhecimento – Criador de FAQ
titleSuffix: Azure Cognitive Services
description: Pode criar uma Base de Dados de Conhecimento do Criador de FAQ a partir dos seus próprios conteúdos, como as FAQ ou os manuais de produtos. A Base de Dados de Conhecimento do Criador de FAQ neste exemplo é criada a partir de uma página Web de FAQ simples para responder a perguntas sobre recuperação de chaves do BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 3f7b24af3d48075150ae7c192ccf421b928a7aa3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113704"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>Criar, preparar e publicar a sua base de dados de conhecimento do Criador de FAQ

Pode criar uma Base de Dados de Conhecimento do Criador de FAQ a partir dos seus próprios conteúdos, como as FAQ ou os manuais de produtos. A Base de Dados de Conhecimento do Criador de FAQ neste exemplo é criada a partir de uma página Web de FAQ simples para responder a perguntas sobre recuperação de chaves do BitLocker.

## <a name="prerequisite"></a>Pré-requisito

> [!div class="checklist"]
> * Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-qna-maker-knowledge-base"></a>Criar uma Base de Dados de Conhecimento do Criador de FAQ

1. Inicie sessão no QnAMaker.ai com as suas credenciais do Azure.

2. No site do Criador de FAQ, selecione **Criar uma base de dados de conhecimento**.

   ![Criar uma Base de Dados de Conhecimento nova](../media/qna-maker-create-kb.png)

3. Na pagina **Criar**, no passo 1, selecione **Criar um serviço do Criador de FAQ**. Será direcionado para o [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar um serviço do Criador de FAQ na sua subscrição. Se o portal do Azure exceder o tempo limite, selecione **Tentar novamente** no site. Depois de estabelecer ligação, o dashboard do Azure é apresentado.

4. Depois de criar um novo serviço do Criador de FAQ no Azure com êxito, volte a qnamaker.ai/criar. Selecione o seu serviço de FAQ nas listas pendentes no passo 2. Se tiver criado um novo serviço de FAQ, certifique-se de que atualiza a página.

   ![Selecione um serviço de Base de dados de conhecimento de FAQ](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. No passo 3, atribua **A Minha BDC do Criador de FAQ de exemplo** como nome da sua BDC.

6. Para adicionar conteúdos à sua BDC, selecione três tipos de origens de dados. No passo 4, em **Preencher a sua BDC**, adicione o URL [FAQ de Recuperação do BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) à **caixa**.

   ![Selecione um serviço de Base de dados de conhecimento de FAQ](../media/qnamaker-quickstart-kb/add-datasources.png)

7. No passo 5, selecione **Criar a sua BDC**.

8. Enquanto a BDC está a ser criada é apresentada uma janela de pop-up. O processo de extração demora alguns minutos a ler a página HTML e a identificar as perguntas e as respostas.

9. Depois de a BDC ter sido criada com êxito, a página **Base de dados de conhecimento** abre-se. Pode editar os conteúdos da BDC nesta página.

10. No canto superior direito, selecione **Adicionar Criador de FAQ** para adicionar uma linha nova à secção **Editorial** da BDC. Em **Pergunta**, introduza **Olá.** Em **Resposta**, introduza **Hello. Faça-me perguntas do bitlocker.**

    ![Adicione um par de FAQ](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. No canto superior direito, selecione **Guardar e preparar** para guardar as edições e preparar o modelo do Criador de FAQ. As edições só são mantidas se tiverem sido guardadas.

12. No canto superior direito, selecione **Testar** para testar se as alterações que introduziu foram efetuadas. ENTER `hi there` na caixa e selecione Enter. Deverá ver a resposta que criou como resposta.

13. Selecione **Inspecionar** para analisar a resposta de forma mais detalhada. A janela de teste é utilizada para testar as alterações à BDC antes de estas serem publicadas.

    ![Painel de Teste](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. Selecione **Testar** novamente para fechar a pop-up **Testar**.

15. No menu ao lado de **Editar**, selecione **Publicar**. Depois, para confirmar, selecione **Publicar** na página.

16. O serviço Criador de FAQ é agora publicado com êxito. Pode utilizar o ponto final na sua aplicação ou código de bot.

    ![Publicar](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](../How-To/create-knowledge-base.md)
