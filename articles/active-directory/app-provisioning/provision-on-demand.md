---
title: Provisionar um utilizador a pedido utilizando o Azure Ative Directory
description: Sincronização de força
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: f7cbe9e9f81b3b71ee7da2feac2908c36f1777e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629017"
---
# <a name="on-demand-provisioning"></a>Aprovisionamento a pedido
Utilize o provisionamento a pedido para doar um utilizador numa aplicação em segundos. Entre outras coisas, pode utilizar esta capacidade para:

* Problemas de configuração de resolução de problemas rapidamente.
* Valide expressões que definiu.
* Teste filtros de deteção.

## <a name="how-to-use-on-demand-provisioning"></a>Como utilizar o provisionamento a pedido

1. Inicie sessão no **portal do Azure**.
1. Vá a **todos os serviços**  >  **Aplicações da Empresa.**
1. Selecione a sua aplicação e, em seguida, vá para a página de configuração de provisionamento.
1. Configure o provisionamento fornecendo as suas credenciais de administração.
1. Selecione **Provisão a pedido**.
1. Procure por um utilizador com o primeiro nome, apelido, nome de exibição, nome principal do utilizador ou endereço de e-mail.
   > [!NOTE]
   > Para a aplicação de provisionamento cloud HR (Workday/SuccessFactors to AD/Azure AD), o valor de entrada é diferente. Para o cenário workday, forneça "WID" do utilizador no Workday. Para o cenário SuccessFactors, forneça "personIdExternal" do utilizador em SuccessFactors. 
 
1. Selecione **Disposição** na parte inferior da página.

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="Screenshot que mostra o portal Azure UI para o fornecimento de um utilizador a pedido.":::

## <a name="understand-the-provisioning-steps"></a>Compreender os passos de provisionamento

O processo de provisionamento a pedido tenta mostrar as medidas que o serviço de fornecimento toma ao providenciar um utilizador. Normalmente, existem cinco passos para o fornecimento de um utilizador. Uma ou mais dessas etapas, explicadas nas seguintes secções, serão mostradas durante a experiência de provisionamento a pedido.

### <a name="step-1-test-connection"></a>Passo 1: Ligação de teste

O serviço de prestação de serviços tenta autorizar o acesso à aplicação-alvo, fazendo um pedido para um "utilizador de teste". O serviço de prestação de serviços espera uma resposta que indique que o serviço autorizado a continuar com as etapas de provisionamento. Este passo só é mostrado quando falha. Não é mostrado durante a experiência de provisão a pedido quando o passo é bem sucedido.

#### <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

* Certifique-se de que forneceu credenciais válidas, como o símbolo secreto e a URL do inquilino, para a aplicação do alvo. As credenciais exigidas variam de acordo com a aplicação. Para obter tutoriais de configuração detalhadas, consulte a [lista de tutoriais.](../saas-apps/tutorial-list.md) 
* Certifique-se de que a aplicação-alvo suporta a filtragem dos atributos correspondentes definidos no painel **de mapeamentos do Atributo.** Poderá ser necessário verificar a documentação da API fornecida pelo desenvolvedor de aplicações para compreender os filtros suportados.
* Para aplicações de Gestão de Identidade de Domínio Cruzado (SCIM), pode utilizar uma ferramenta como o Carteiro. Estas ferramentas ajudam-no a garantir que o pedido responde aos pedidos de autorização da forma que o serviço de fornecimento Azure Ative (Azure AD) espera. Veja um [pedido de exemplo.](./use-scim-to-provision-users-and-groups.md#request-3)

### <a name="step-2-import-user"></a>Passo 2: Utilizador de importação

Em seguida, o serviço de fornecimento recupera o utilizador do sistema de origem. O utilizador atribui que os recuperadores de serviço são usados mais tarde para:

* Avaliar se o utilizador está em possibilidade de provisão.
* Verifique o sistema alvo para um utilizador existente.
* Determine quais os atributos do utilizador para exportar para o sistema alvo.

#### <a name="view-details"></a>Ver detalhes


A secção **de detalhes do Ver** mostra as propriedades do utilizador que foram importadas do sistema de origem (por exemplo, Azure AD).

#### <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

* Importar o utilizador pode falhar quando o atributo de correspondência faltar no objeto do utilizador no sistema de origem. Para resolver este fracasso, experimente uma destas abordagens:

  * Atualize o objeto do utilizador com um valor para o atributo correspondente.
  * Altere o atributo de correspondência na sua configuração de provisionamento.

* Se faltar um atributo que esperava da lista importada, certifique-se de que o atributo tem um valor no objeto do utilizador no sistema de origem. O serviço de prestação de serviços atualmente não suporta a prestação de atributos nulos.
* Certifique-se de que a página de **mapeamento do Atributo** da sua configuração de provisionamento contém o atributo que espera.

### <a name="step-3-determine-if-user-is-in-scope"></a>Passo 3: Determinar se o utilizador está no âmbito

Em seguida, o serviço de fornecimento determina se o utilizador está em [possibilidade de](./how-provisioning-works.md#scoping) provisão. O serviço considera aspetos como:

* Se o utilizador está atribuído à aplicação.
* Se o âmbito está definido para **Sync atribuído** ou **Sync all**.
* Os filtros de deteção definidos na sua configuração de provisionamento.  

#### <a name="view-details"></a>Ver detalhes

A secção **'Ver detalhes'** mostra as condições de deteção que foram avaliadas. Pode ver uma ou mais das seguintes propriedades:

* **Ativo no sistema de origem** indica que o utilizador tem a propriedade `IsActive` definida como **verdadeira** em Azure AD.
* **Atribuído à aplicação** indica que o utilizador está atribuído à aplicação em Azure AD.
* **A sincronização** do âmbito indica que a definição de âmbito permite a todos os utilizadores e grupos do arrendatário.
* **O utilizador requer uma função** que indica que o utilizador tem as funções necessárias para ser adsavisionado na aplicação. 
* **Os filtros de escotagem** também são mostrados se tiver definido filtros de deteção para a sua aplicação. O filtro é apresentado com o seguinte formato: {scoping filter title} {atributo do filtro de escotagem} {operador de filtro de escotagem} {scoping filter value}.

#### <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

* Certifique-se de que definiu um papel de scoping válido. Por exemplo, evite utilizar o [operador Greater_Than](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) com um valor não inteiro.
* Se o utilizador não tiver o papel necessário, reveja as [dicas para o provisionamento dos utilizadores atribuídos à função de acesso predefinido](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role).

### <a name="step-4-match-user-between-source-and-target"></a>Passo 4: Combinar o utilizador entre a fonte e o alvo

Neste passo, o serviço tenta corresponder o utilizador que foi recuperado no passo de importação com um utilizador no sistema alvo.

#### <a name="view-details"></a>Ver detalhes

A página **de detalhes do Ver** mostra as propriedades dos utilizadores que foram correspondidas no sistema alvo. As propriedades que vê no painel de contexto variam da seguinte forma:

* Se nenhum utilizadores for compatível com o sistema alvo, não verá nenhuma propriedade.
* Se houver um utilizador compatível no sistema alvo, verá as propriedades desse utilizador compatível com o sistema alvo.
* Se vários utilizadores forem compatíveis, verá as propriedades de ambos os utilizadores compatíveis.
* Se vários atributos correspondentes forem parte dos mapeamentos do seu atributo, cada atributo correspondente é avaliado sequencialmente e os utilizadores correspondentes para esse atributo são mostrados.

#### <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

* O serviço de fornecimento pode não ser capaz de combinar um utilizador no sistema de origem de forma única com um utilizador no alvo. Resolva este problema garantindo que o atributo correspondente é único.
* Certifique-se de que a aplicação-alvo suporta a filtragem no atributo definido como o atributo correspondente.  

### <a name="step-5-perform-action"></a>Passo 5: Realizar ação

Finalmente, o serviço de prestação de serviços toma medidas, tais como criar, atualizar, eliminar ou saltar o utilizador.

Aqui está um exemplo do que você pode ver após o bem sucedido fornecimento a pedido de um utilizador:

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="Screenshot que mostra o portal Azure UI para o fornecimento de um utilizador a pedido.":::

#### <a name="view-details"></a>Ver detalhes

A secção **de detalhes do Ver** exibe os atributos que foram modificados na aplicação alvo. Este visor representa a produção final da atividade do serviço de fornecimento e os atributos que foram exportados. Se este passo falhar, os atributos apresentados representam os atributos que o serviço de fornecimento tentou modificar.

#### <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

* As falhas nas exportações podem variar muito. Verifique a [documentação para a disponibilização de registos](../reports-monitoring/concept-provisioning-logs.md#error-codes) de falhas comuns.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

* **Precisa de desligar o provisionamento para utilizar o provisionamento a pedido?** Para aplicações que utilizem um token ao portador de longa duração ou um nome de utilizador e senha para autorização, não são necessários passos adicionais. Os pedidos que utilizam o OAuth para autorização exigem atualmente que o trabalho de provisionamento seja interrompido antes de utilizar o provisionamento a pedido. Aplicações como G Suite, Box, Workplace by Facebook e Slack enquadram-se nesta categoria. Estão em curso trabalhos para apoiar o provisionamento a pedido de todos os pedidos sem ter de deixar de provisões.

* **Quanto tempo demora o provisionamento a pedido?** O fornecimento a pedido normalmente demora menos de 30 segundos.

## <a name="known-limitations"></a>Limitações conhecidas

Existem atualmente algumas limitações conhecidas para o provisionamento a pedido. Publique as suas [sugestões e feedback](https://aka.ms/appprovisioningfeaturerequest) para que possamos determinar melhor quais as melhorias a fazer a seguir.

> [!NOTE]
> As seguintes limitações são específicas da capacidade de provisionamento a pedido. Para obter informações sobre se uma aplicação suporta grupos de provisionamento, supressões ou outras capacidades, consulte o tutorial para essa aplicação.

* A aplicação Amazon Web Services (AWS) não suporta o fornecimento a pedido. 
* O fornecimento a pedido de grupos e funções não é apoiado.
* O fornecimento a pedido suporta a desativação de utilizadores que não tenham sido atribuídos à aplicação. No entanto, não suporta desativar ou eliminar utilizadores que tenham sido desativados ou eliminados do Azure AD. Esses utilizadores não aparecerão quando pesquisar por um utilizador.

## <a name="next-steps"></a>Passos seguintes

* [Disposição relativa à resolução de problemas](./application-provisioning-config-problem.md)