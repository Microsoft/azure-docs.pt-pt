---
title: Handling Azure application review feedback [ Mercado Azure
description: Explica como usar o Azure DevOps para lidar com o feedback de revisão das ofertas de aplicações do Azure para o Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: dsindona
ms.openlocfilehash: b5f290bae908ac753801eef2c9b8394ca1bb7a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285304"
---
# <a name="handling-review-feedback"></a>Processar comentários de crítica

Este artigo explica como aceder ao ambiente Azure DevOps utilizado pela equipa de revisão do Microsoft Azure Marketplace.  Se forem encontrados problemas críticos na sua oferta de aplicação Azure durante a fase de revisão da **Microsoft,** pode iniciar sessão neste sistema para visualizar informações detalhadas sobre estes problemas (feedback de revisão).  Depois de corrigir todos estes problemas, deve reenviar a sua oferta para continuar a publicá-la no Azure Marketplace.  O diagrama seguinte ilustra como este processo de feedback se relaciona com o processo de publicação.

![Etapas de publicação com feedback de Azure DevOps](./media/pub-flow-vsts-access.png)

Tipicamente, as questões de revisão são referenciadas como pedido de puxão (PR).  Cada PR está ligado a um item online [Azure DevOps](https://azure.microsoft.com/services/devops/) (anteriormente chamado Visual Studio Team Services (VSTS),que contém detalhes sobre o problema.  A imagem que se segue apresenta um exemplo de uma referência de relações públicas de revisão.  Para situações complexas, as equipas de revisão e suporte também podem enviar-lhe um e-mail. 

![Separador de estado que exibe feedback de revisão](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Acesso azure DevOps

Para ver os artigos de RELAÇões Públicas referenciados no feedback da revisão, os editores devem primeiro ter uma autorização adequada.  Caso contrário, os novos `401 - Not Authorized` editores recebem uma página de resposta ao tentarem visualizar os PRs.  Para solicitar o acesso a este repositório Azure DevOps, execute os seguintes passos:

1. Recolha as seguintes informações:
    - O seu nome de editor e id
    - Tipo de oferta (app Azure), nome da oferta e ID SKU
    - O link de pedido `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` de puxão, por exemplo: Este URL pode ser recuperado a partir da mensagem de notificação ou do endereço da página de resposta 401.
    - O endereço de e-mail(es) dos indivíduos da sua organização editorial a que pretende ter acesso concedido.  Esta lista deve incluir o endereço(es) do proprietário fornecido ao registar-se como editor no Portal do Parceiro cloud.
2. Criar um incidente de apoio.  Na barra de título do Portal do Parceiro cloud, selecione o botão **Ajuda** e, em seguida, escolha **suporte** no menu.  O navegador de web predefinido deve ser lançado e navegar para a nova página de incidentes de suporte da Microsoft.  (Pode ter que assinar primeiro.)
3. Especifique o **tipo problema** como **marketplace onboarding** e **Categoria** como problema de **acesso,** em seguida, selecione **pedido iniciar**.

    ![Categoria de bilhetes de apoio](./media/support-incident1.png)

4. Na **página 1 do passo 1 de 2,** forneça as suas informações de contacto e selecione **Continue**.
5. No **passo 2 de 2** página, especifique um título de incidente (por exemplo) `Request Azure DevOps access`e forneça as informações recolhidas no primeiro passo (acima).  Leia e aceite o acordo, em seguida, selecione **Enviar**.

Se a criação do incidente foi bem sucedida, uma página de confirmação é exibida.  Guarde as informações de confirmação nesta página para a sua referência.  A Equipa de Suporte da Microsoft deve responder ao seu pedido de acesso dentro de alguns dias úteis.


## <a name="reviewing-the-pull-request"></a>Rever o pedido de puxão 

Utilize o seguinte procedimento para rever as questões documentadas no pedido de retirada.

1. Na secção de revisão da **Microsoft** do formulário de **etapas** de publicação, clique num link de RELAÇÕES para lançar o seu navegador e navegar para a página **de Visão Geral** (casa) para este PR.  A imagem que se segue retrata uma página inicial de emissão crítica para a oferta da aplicação da amostra Contoso.  Esta página contém informações sumárias úteis sobre os problemas de revisão encontrados na aplicação Azure.  

    [![Puxe a página inicial do pedido de pedido](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Clique na imagem para expandir.*
    
2. (Opcional) No lado direito da janela, na secção **Políticas,** clique na mensagem de emissão (neste exemplo: **Validação de Políticas falhou)** para investigar os detalhes de baixo nível do problema, incluindo os ficheiros de registo associados.  Os erros são normalmente apresentados na parte inferior dos ficheiros de registo.

3. No menu do lado esquerdo da página inicial, selecione **Ficheiros** para exibir os ficheiros da lista que compõem os ativos técnicos para esta oferta.  Os revisores da Microsoft deveriam ter adicionado comentários descrevendo os problemas críticos descobertos.  No exemplo seguinte, foram descobertas duas questões. 

    [![Puxe a página inicial do pedido de pedido](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Clique na imagem para expandir.*

4. Clique em cada nó de comentário na árvore esquerda para navegar para o comentário no contexto do código circundante.  Corrija o seu código fonte no projeto da sua equipa para corrigir o problema descrito pelo comentário.

> [!Note]
> Não é possível editar os ativos técnicos da sua oferta no ambiente Azure DevOps da equipa de revisão.  Para os editores, este é um ambiente só para leitura para o código fonte contido.  No entanto, pode deixar respostas aos comentários em benefício da equipa de revisão da Microsoft.

   No exemplo seguinte, a editora reviu, corrigiu e respondeu à primeira edição.

   ![Primeira correção e resposta de comentário](./media/first-comment-reply.png)


## <a name="next-steps"></a>Passos seguintes

Depois de corrigir as questões críticas documentadas na revisão pr(s), deve voltar a publicar a sua oferta de [aplicações Azure](./cpp-publish-offer.md).
