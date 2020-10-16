---
title: 'Serviço de Peering Azure: Criar '
description: Neste tutorial aprenda a registar o Azure Peering Service e um prefixo.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/2/2020
ms.author: derekol
Customer intent: With Azure Peering service enhancing the customer connectivity to Microsoft cloud services .
ms.openlocfilehash: f8f0ee71a8dd00498d868bb6dc11b3fa083df766
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530223"
---
# <a name="tutorial-create-a-peering-service-connection"></a>Tutorial: Criar uma conexão de serviço de peering

Este tutorial mostra-lhe como criar um recurso de Serviço de Peering e como configurar uma ligação de Serviço de Peering. 

1. Para registar uma ligação de Serviço de Peering, **selecione Criar um**serviço  >  **de peering de**recursos .

 
    ![Registar Peering Service](./media/peering-service-portal/peering-servicecreate.png)

2. Introduza os seguintes detalhes no separador **Básicos** na página Criar uma página **de conexão de serviço de espreitar.**
 
3. Selecione a subscrição e o grupo de recursos associado à subscrição.

    ![Registre o separador básico do Serviço de Pares](./media/peering-service-portal/peering-servicebasics.png)

4. Introduza um **Nome** ao qual deve ser registada a instância do Serviço de Observação.

5. Agora, selecione o botão **Seguinte:Configuração** na parte inferior da página. A página **de Configuração** aparece.
## <a name="configure-the-peering-service-connection"></a>Configurar a ligação de Serviço de Peering

1. Na página **Configuração,** selecione o local ao qual o Serviço de Peering deve ser ativado selecionando o mesmo na lista de localização do **serviço de peering.**

1. Selecione o prestador de serviços a partir do qual o Serviço de Pares deve ser obtido selecionando um nome de fornecedor da lista de down-down do prestador de **serviços Peering.**
 
1. **Selecione Criar novo prefixo** na parte inferior da secção **Prefixos** e aparecer caixas de texto. Agora, insira o nome do recurso prefixo e os prefixos que estão associados ao prestador de serviços.

1. Selecione **a Chave Prefixo** e adicione a Chave prefixo que lhe foi dada pelo seu fornecedor (ISP ou IXP). Esta chave permite aos Ms validar o prefixo e o fornecedor que alocou o seu prefixo IP.

    ![A screenshot mostra o separador Configuração da página de ligação de serviço de espreitar onde pode introduzir a tecla Prefix.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Selecione o botão **'Rever +'** no lado inferior esquerdo da página. Aparece a página **Review + create** e o Azure valida a sua configuração.

 1. Quando vir a mensagem **de validação passada** como mostrado, selecione **Criar**.

> ![Screenshot mostra o separador Review + criar uma página de conexão de serviço de espreitar.](./media/peering-service-portal/peering-service-prefix.png)

1. Depois de registar uma ligação de Serviço de Peering, é realizada uma validação adicional nos prefixos incluídos. Pode rever o estado de validação na secção **Prefixos** do nome do recurso. Se a validação falhar, é apresentada uma das seguintes mensagens de erro:

   - Prefixo de Serviço de Pares Inválido, o prefixo deve ser um formato válido, apenas o prefixo Ipv4 é suportado.
   - O prefixo não foi recebido pelo prestador de serviços peering.
   - O anúncio do prefixo não tem uma comunidade BGP válida, por favor contacte o fornecedor de Serviços de Peering.
   - Rota de backup não encontrada, contacte o fornecedor de Serviços peering.
   - Prefixo recebido com caminho AS mais longo, contacte o prestador de serviços peering.
   - Prefixo recebido com AS privado no caminho, contacte o prestador de serviços de peering.

### <a name="add-or-remove-a-prefix"></a>Adicionar ou remover um prefixo

**Selecione Adicionar prefixos** na página **Prefixos** para adicionar prefixos.

Selecione a elipse (...) ao lado do prefixo listado e selecione a opção **Eliminar.**

### <a name="delete-a-peering-service-connection"></a>Eliminar uma ligação de Serviço de Peering

Na página **Todos os Recursos,** selecione a caixa de verificação no Serviço de Observação e selecione a opção **Eliminar** no topo da página.
## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a ligação do Serviço de Peering, consulte [a ligação do Serviço de Peering](connection.md).
- Para saber mais sobre a telemetria de conexão peering Service, consulte [a telemetria de conexão peering service](connection-telemetry.md).
- Para medir a telemetria, consulte [a telemetria de ligação medida](measure-connection-telemetry.md).
- Para registar a ligação utilizando a Azure PowerShell, consulte [registar uma ligação de Serviço de Pares - Azure PowerShell](powershell.md).
- Para registar a ligação utilizando o Azure CLI, consulte [registar uma ligação de Serviço de Pares - Azure CLI](cli.md).