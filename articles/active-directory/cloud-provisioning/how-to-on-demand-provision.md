---
title: Azure AD Connect cloud provisioning on demand provisioning
description: Este artigo descreve a funcionalidade de provisionamento a pedido.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72623d69f9fb0cbc68758a362bb977e5e02d2c00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91637303"
---
# <a name="azure-ad-connect-cloud-provisioning-on-demand-provisioning"></a>Azure AD Connect cloud provisioning on demand provisioning

O fornecimento de nuvem AD Connect Azure introduziu uma nova funcionalidade que lhe permitirá testar alterações de configuração, aplicando estas alterações a um único utilizador.  Pode utilizar isto para validar e verificar se as alterações efetuadas à configuração foram corretamente aplicadas e estão a ser corretamente sincronizadas para a Azure AD.  

> [!IMPORTANT] 
> Quando utilizar o provisionamento a pedido, os filtros de escoamento não são aplicados ao utilizador selecionado.  Isto significa que pode utilizar o provisionamento a pedido em utilizadores que estão fora das OUs que especificou.


## <a name="using-on-demand-provisioning"></a>Utilização de provisões a pedido
Para utilizar a nova funcionalidade, siga os passos abaixo.


1.  No portal Azure, selecione **Azure Ative Directory**.
2.  Selecione **Azure Ad Connect**.
3.  **Selecione Gerir o provisionamento**.

    ![Gerir o provisionamento](media/how-to-configure/manage1.png)
4. Em **Configuração,** selecione a sua configuração.
5. Em **Validação** clique no botão **'Disposição'.** 

 ![Provisionamento de um utilizador](media/how-to-on-demand-provision/on-demand2.png)

6. No ecrã de provisionamento a pedido.  Introduza o **nome distinto** de um utilizador e clique no botão **Provision.**  
 
 ![Provisionamento a pedido](media/how-to-on-demand-provision/on-demand3.png)
7. Uma vez concluído, deverá ver um ecrã de sucesso e 4 caixas de verificação verdes indicando que foi a provisionada com sucesso.  Quaisquer erros aparecerão à esquerda.

  ![Success](media/how-to-on-demand-provision/on-demand4.png)

Agora pode olhar para o utilizador e determinar se as alterações efetuadas na configuração foram aplicadas.  O restante deste documento descreverá as secções individuais que são apresentadas nos detalhes de um utilizador sincronizado com sucesso.

## <a name="import-user-details"></a>Importar detalhes do utilizador
Esta secção fornece informações sobre o utilizador que foi importado do Ative Directory.  Isto é o que o utilizador parece antes de ser provisionado no Azure AD.  Clique no link **ver detalhes** para exibir esta informação.

![Utilizador de importação](media/how-to-on-demand-provision/on-demand5.png)

Usando esta informação, você pode ver os vários atributos, e seus valores, que foram importados.  Se criou um mapeamento de atributos personalizados, poderá ver o valor aqui.
![Importar detalhes do utilizador](media/how-to-on-demand-provision/on-demand6.png)

## <a name="determine-if-user-is-in-scope-details"></a>Determinar se o utilizador está em detalhes de âmbito
Esta secção fornece informações sobre se o utilizador que foi importado para a Azure AD está no âmbito.  Clique no link **ver detalhes** para exibir esta informação.

![Âmbito do utilizador](media/how-to-on-demand-provision/on-demand7.png)

Utilizando estas informações, pode consultar informações adicionais sobre o âmbito dos seus utilizadores.

![Detalhes do âmbito do utilizador](media/how-to-on-demand-provision/on-demand10a.png)

## <a name="match-user-between-source-and-target-system-details"></a>Combine o utilizador entre os detalhes do sistema de origem e alvo
Esta secção fornece informações sobre se o utilizador já existe no Azure AD e se ocorrer uma junção em vez de fornecer um novo utilizador.  Clique no link **ver detalhes** para exibir esta informação.
![Ver detalhes](media/how-to-on-demand-provision/on-demand8.png)

Utilizando estas informações, pode ver se foi encontrado um fósforo ou se um novo utilizador será criado.

![Informações do utilizador](media/how-to-on-demand-provision/on-demand11.png)

Os detalhes de Matching mostrarão uma mensagem com uma das três operações seguintes.  A saber:
- Criar - um utilizador é criado em Azure AD
- Atualização - um utilizador é atualizado com base numa alteração feita na configuração
- Excluir - um utilizador é removido do Azure AD.

Dependendo do tipo de operação que efetuou, a mensagem variará.

## <a name="perform-action-details"></a>Realizar detalhes de ação
Esta secção fornece informações sobre o utilizador que foi a provisionado ou exportado para a Azure AD após a aplicação da configuração.  Isto é o que o utilizador parece uma vez que é provisionado em Azure AD.  Clique no link **ver detalhes** para exibir esta informação.
![Realizar detalhes de ação](media/how-to-on-demand-provision/on-demand9.png)

Utilizando esta informação, pode ver os valores dos atributos após a aplicação da configuração.  Parecem-se com o que foi importado ou são diferentes?  A configuração aplica-se com sucesso?  

Isto irá permitir-lhe rastrear a transformação do atributo à medida que se move através da nuvem e para o seu inquilino AD AZure.

![atributo traço](media/how-to-on-demand-provision/on-demand12.png)

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
- [Como instalar o provisionamento em nuvem Azure AD Connect](how-to-install.md)
 