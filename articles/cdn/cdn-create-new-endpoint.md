---
title: Início Rápido – Criar um perfil e um ponto final da CDN do Azure | Microsoft Docs
description: Este início rápido mostra como ativar a CDN do Azure através da criação de um novo perfil e ponto final da CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/13/2018
ms.author: mazha
ms.custom: mvc
ms.openlocfilehash: 91bb23b2790ed23c831bb1680f6f2bb2cdecc38f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2018
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Início Rápido: Criar um perfil e um ponto final da CDN do Azure
Neste início rápido, vai ativar a Rede de Entrega de Conteúdos (CDN) através da criação de um novo perfil da CDN e ponto final da CDN. Depois de criar um perfil e um ponto final, pode começar a entrega de conteúdos aos seus clientes.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para efeitos deste início rápido, tem de ter criado uma conta de armazenamento com o nome *mystorageacct123*, que utiliza para o nome do anfitrião de origem. Para obter mais informações, veja [Integrar uma conta de armazenamento do Azure com a CDN do Azure](cdn-create-a-storage-account-with-cdn.md)

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com) com a sua conta do Azure.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Criar um novo ponto final da CDN

Depois de criar um perfil da CDN, pode utilizá-lo para criar um ponto final.

1. No Portal do Azure, selecione no dashboard o perfil da CDN que criou. Se não o conseguir encontrar, selecione **Todos os serviços** e, em seguida, selecione **Perfis de CDN**. Na página **Perfis de CDN**, selecione o perfil que pretende utilizar. 
   
    É apresentada a página do perfil de CDN.

2. Selecione o **Ponto Final**.
   
    ![Perfil da CDN](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    A página **Adicionar um ponto final** é apresentada.

    Utilize as definições especificadas na tabela abaixo da imagem.
   
    ![Painel Adicionar ponto final](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

    | Definição | Valor |
    | ------- | ----- |
    | **Nome** | Introduza *my-endpoint-123* no nome de anfitrião do ponto final. Este nome deve ser globalmente exclusivo; se já se encontrar em utilização, pode introduzir um diferente. Este nome é utilizado para aceder aos seus recursos em cache no domínio _&lt;nome do ponto final&gt;_.azureedge.net.|
    | **Tipo de origem** | Selecione **Armazenamento**. | 
    | **Nome de anfitrião da origem** | Introduza *mystorageacct123.blob.core.windows.net* no nome de anfitrião. Este nome deve ser globalmente exclusivo; se já se encontrar em utilização, pode introduzir um diferente |
    | **Caminho de origem** | Deixe em branco. |
    | **Cabeçalho de anfitrião de origem** | Deixe o valor predefinido gerado. |  
    | **Protocolo** | Deixe as opções **HTTP** e **HTTPS** predefinidas selecionadas. |
    | **Porta de origem** | Deixe os valores de porta predefinidos. | 
    | **Otimizado para** | Deixe a seleção predefinida, **Entrega geral Web**. |
    
3. Selecione **Adicionar** para criar o novo ponto final.
   
   Depois de o ponto final ser criado, aparece na lista de pontos finais para o perfil.
    
   ![Ponto final da CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   Dado que a propagação do registo demora algum tempo, o ponto final não está imediatamente disponível para utilização: 
   - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em dez minutos. 
   - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
   - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída no prazo de 90 minutos. 

## <a name="clean-up-resources"></a>Limpar recursos
Nos passos anteriores, criou um perfil e um ponto final de CDN num grupo de recursos. Guarde estes recursos, se pretender aceder aos [Passos seguintes](#next-steps) e aprender a adicionar um domínio personalizado ao ponto final. No entanto, se não pretende utilizar estes recursos no futuro pode eliminá-los, ao eliminar o grupo de recursos, evitando assim encargos adicionais:

1. No menu esquerdo do Portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione **my-resource-group-123**.

2. Na página **Grupo de recursos**, selecione **Eliminar grupo de recursos**, introduza *my-resource-group-123* na caixa de texto e, em seguida, selecione **Eliminar**.

    Esta ação irá eliminar o grupo de recursos, o perfil e o ponto final que criou neste início rápido.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como adicionar um domínio personalizado ao ponto final de CDN, veja o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Adicionar a CDN do Azure a uma aplicação web](app-service-web-tutorial-content-delivery-network.md)


