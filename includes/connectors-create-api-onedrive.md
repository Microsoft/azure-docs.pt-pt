---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 8b5f72f18ee5bf9c1e4dbaaa87d6705f55b09bc5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524217"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure; pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Antes de poder utilizar a sua conta OneDrive numa aplicação lógica, autorize a aplicação lógica para se ligar à sua conta OneDrive.  Pode fazê-lo facilmente dentro da sua aplicação lógica no portal Azure. 

Autorize a sua aplicação lógica para ligar à sua conta OneDrive utilizando os seguintes passos:

1. Criar uma aplicação lógica. No designer de Aplicações Lógicas, selecione **Mostrar APIs geridos pela Microsoft** na lista de drop down e, em seguida, insira "onedrive" na caixa de pesquisa. Selecione um dos gatilhos ou ações:  
   ![Uma caixa de diálogo intitulada "Show Microsoft managed APIs" tem uma caixa de pesquisa que contém "onedrive". Abaixo está uma lista de quatro gatilhos. O primeiro da lista é "OneDrive - Quando um ficheiro é criado", que é selecionado.](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Se ainda não criou nenhuma ligação ao OneDrive, é-lhe pedido que faça sê-lo utilizando as suas credenciais OneDrive:  
   ![Uma caixa de diálogo intitulada "OneDrive - Quando um ficheiro é criado" tem um botão com a etiqueta "Iniciar sação".](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Selecione **Iniciar sôm,** e insira o seu nome de utilizador e senha. Selecione **Iniciar sção em**:  
   ![Uma caixa de diálogo intitulada "Iniciar sê-lo" instrui-o a "Utilizar a sua conta Microsoft". Tem duas caixas de texto com a etiqueta "Email ou telefone" e "Password" Tem também uma caixa de verificação com a etiqueta "Mantenha-me inscrito", e um botão com a etiqueta "Iniciar sê-lo".](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Estas credenciais são usadas para autorizar a sua aplicação lógica a ligar-se e aceder aos dados na sua conta OneDrive. 
4. Selecione **Sim** para autorizar a aplicação lógica a utilizar a sua conta OneDrive:  
   ![Uma caixa de diálogo intitulada "Deixe esta aplicação aceder à sua informação?" pede permissão para fazer as seguintes quatro coisas: 1) "Iniciar automaticamente", 2) "Aceder aos seus endereços de e-mail", 3) "Aceder à sua informação a qualquer momento", e 4) "Aceder aos ficheiros OneDrive". Há um botão "Sim" para dar permissão, e um botão "Não" para negá-lo. Existe um link para alterar estas permissões de aplicação.](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Note que a ligação foi criada. Agora, proceda com os outros passos na sua aplicação lógica:  
   ![Uma caixa de diálogo intitulada "Quando um ficheiro é criado" tem uma caixa de texto intitulada "FOLDER" com um botão de navegação associado.](./media/connectors-create-api-onedrive/onedrive-5.png)

