---
title: Disposição de um utilizador a pedido utilizando o Azure Ative Directory
description: Sincronização de força
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/23/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 78a56b6a848139c47d7934a47decb126afe00b7a
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85297533"
---
# <a name="on-demand-provisioning"></a>Provisionamento a pedido
O provisionamento a pedido permite-lhe a provisionar um utilizador numa aplicação em segundos. Pode utilizar a capacidade para resolver rapidamente problemas de configuração, validar expressões que definiu, testar filtros de deteção e muito mais. 

## <a name="how-to-use-on-demand-provisioning"></a>Como utilizar o provisionamento a pedido 

1. Inicie sessão no **portal Azure**.
2. Navegue para **aplicações da Enterprise.**
3. Selecione a sua aplicação e navegue para a página de configuração de provisionamento.
4. Configure o provisionamento fornecendo as suas credenciais de administração.
5. Clique **na provisão a pedido.**
6. Procure um utilizador com o primeiro nome, apelido, nome de exibição, nome principal do utilizador ou e-mail.
7. Selecione a provisão na parte inferior da página.

## <a name="understanding-the-provisioning-steps"></a>Compreender os passos de provisionamento
A capacidade de provisionamento a pedido tenta mostrar as medidas que o serviço de fornecimento toma ao providenciar um utilizador. Existem normalmente cinco passos para o provisionamento de um utilizador, e um ou mais dos passos abaixo serão mostrados na experiência de fornecimento de pedidos a pedido.

### <a name="step-1-test-connection"></a>Passo 1: Ligação de teste
O serviço de prestação de serviços tenta autorizar o acesso à aplicação-alvo, fazendo um pedido para um "utilizador de teste". O serviço de fornecimento espera uma resposta indicando que está autorizado a continuar com as etapas de provisionamento. Este passo só é mostrado quando há uma falha no degrau. Não é mostrado na experiência de provisionamento a pedido quando o passo é bem sucedido. 

**Sugestões de resolução de problemas**
* Certifique-se de que forneceu credenciais válidas à aplicação do alvo, como o token secreto e a URL do inquilino. As credenciais exigidas variam de acordo com a aplicação. Tutoriais de configuração detalhada podem ser encontrados [aqui.](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 
* Certifique-se de que a aplicação-alvo suporta a filtragem dos atributos correspondentes definidos na lâmina de mapeamento do atributo. Poderá ser necessário verificar a documentação da API fornecida pelo desenvolvedor de aplicações para compreender os filtros que suportam.  
* Para aplicações SCIM, pode utilizar uma ferramenta como carteiro para garantir que a aplicação responde aos pedidos de autorização como o serviço de fornecimento AZURE AD espera. Um pedido de exemplo pode ser encontrado [aqui.](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#request-3)

### <a name="step-2-import-user"></a>Passo 2: Utilizador de importação
Em seguida, o serviço de fornecimento recupera o utilizador do sistema de origem. O utilizador atribui que o serviço recupera mais tarde para avaliar se o utilizador está no âmbito do provisionamento, verificando o sistema-alvo de um utilizador existente e para determinar quais os atributos do utilizador a exportar para o sistema alvo. 

**Ver detalhes**

A secção de detalhes da visualização mostra as propriedades do utilizador que foram importadas do sistema de origem (por exemplo, Azure AD).

**Sugestões de resolução de problemas**
* Importar o utilizador pode falhar quando o atributo de correspondência faltar no objeto do utilizador no sistema de origem. Pode resolver esta falha atualizando o objeto do utilizador com um valor para o atributo de correspondência ou alterando o atributo de correspondência na sua configuração de provisionamento.  
* Se faltar um atributo que esperava da lista que foi importada, certifique-se de que o atributo tem um valor no objeto do utilizador no sistema de origem. O serviço de prestação de serviços atualmente não suporta a prestação de atributos nulos. 
* Certifique-se de que a sua página de mapeamento de atributos de provisionamento contém o atributo que está à espera. 

### <a name="step-3-determine-if-user-is-in-scope"></a>Passo 3: Determinar se o utilizador está no âmbito
Em seguida, o serviço de fornecimento determina se o utilizador está em [possibilidade de](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#scoping) provisão. O serviço irá considerar aspetos como se o utilizador está atribuído à aplicação, se o âmbito está definido para sincronizar atribuído ou sincronizar todos, e os filtros de digitalização definidos na sua configuração de provisionamento.  

**Ver detalhes**

A secção de detalhes da vista mostra as condições de deteção que foram avaliadas. Pode ver uma das seguintes propriedades:
* **Ativo no sistema de origem** indica que o utilizador tem a propriedade ativa definida para ser verdadeira em Azure AD.
* **Atribuída à aplicação** indica que o utilizador está atribuído à aplicação em Azure AD
* **A sincronização** do âmbito indica que a definição de âmbito permite a todos os utilizadores e grupos do arrendatário.
* **O utilizador requer uma função** que indica que o utilizador tem as funções necessárias para ser adsavisionado na aplicação. 
* **Os filtros de escotagem** também serão mostrados se tiver definido filtros de deteção para a sua aplicação. O filtro será apresentado com o seguinte formato - {scoping filter title} {atributo do filtro de escotagem} {operador de filtro de escotagem} {value de filtro de escotagem}. 

**Sugestões de resolução de problemas**
* Certifique-se de que definiu um papel de scoping válido. Por exemplo, evite utilizar o operador ["Maior do que"](https://docs.microsoft.com/azure/active-directory/app-provisioning/define-conditional-rules-for-provisioning-user-accounts#create-a-scoping-filter) com um valor não inteiro. 
* Se o utilizador não tiver o papel necessário, reveja as dicas [aqui descritas](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned#provisioning-users-assigned-to-the-default-access-role). 

### <a name="step-4-match-user-between-source-and-target"></a>Passo 4: Combinar o utilizador entre a fonte e o alvo
Neste passo. o serviço tenta corresponder o utilizador que foi recuperado no passo de importação com um utilizador no sistema-alvo. 

**Ver detalhes**

As páginas de detalhes da visualização mostram as propriedades do(s) utilizador(s) que foram correspondidas no sistema alvo. As propriedades que vê no painel de contexto variarão da seguinte forma:
* Se não houver utilizadores compatíveis no sistema alvo, não verá nenhuma propriedade.
* Se houver um utilizador compatível no sistema alvo, verá as propriedades desse utilizador compatível a partir do sistema alvo.
* Se vários utilizadores forem compatíveis, verá as propriedades de ambos os utilizadores compatíveis.
* Se vários atributos correspondentes forem parte dos mapeamentos do seu atributo, cada atributo correspondente será avaliado sequencialmente e os utilizadores correspondentes serão mostrados. 

**Detalhes da resolução de problemas**
* O serviço de fornecimento não consegue coincidir de forma única um utilizador na fonte com um utilizador no alvo. Isto pode ser resolvido garantindo que o atributo correspondente é único. 
* Certifique-se de que a aplicação-alvo suporta a filtragem no atributo definido como o atributo correspondente.  

### <a name="step-5-perform-action"></a>Passo 5: Realizar ação
Finalmente, o serviço de prestação de serviços toma uma ação como criar, atualizar, eliminar ou saltar o utilizador. 

**Ver detalhes**

A secção de detalhes de visualização exibe os atributos que foram modificados na aplicação alvo. Isto representa a produção final da atividade de serviço de prestação de serviços e os atributos que foram exportados. Se este passo falhar, os atributos apresentados representam os atributos que o serviço de fornecimento tentou modificar.  

**Sugestões de resolução de problemas**
* As falhas nas exportações podem variar muito. Consulte a [documentação](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs#error-codes) dos registos de provisionamento para falhas comuns.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
**Precisa de desligar o provisionamento para utilizar o provisionamento a pedido?** Para aplicações que utilizem um token portador de longa duração ou nome de utilizador e senha para autorização, não são necessários passos adicionais. Os pedidos que utilizam o OAuth para autorização exigem atualmente que o trabalho de provisionamento seja interrompido antes de utilizar o provisionamento a pedido. Aplicações como G Suite, Box, Workplace by Facebook e Slack enquadram-se nesta categoria. Estão em curso trabalhos para permitir o fornecimento a pedido de todos os pedidos, sem ter de deixar de provisões. 

**Quanto tempo demora o provisionamento a pedido?** Geralmente demora menos de 30 segundos. 

## <a name="known-limitations"></a>Limitações Conhecidas
Há algumas limitações conhecidas hoje. Por favor, publique no [UserVoice](https://aka.ms/appprovisioningfeaturerequest) para que possamos priorizar melhor quais as melhorias a fazer a seguir. Note que estas limitações são específicas da capacidade de provisionamento a pedido. para obter informações específicas sobre se uma aplicação suporta grupos de provisionamento, supressões, etc., verifique o tutorial de aplicação. 

* As aplicações Workday, AWS e SuccessFactors não apoiam o provisionamento a pedido.
* O provisionamento de grupos e papéis a pedido não é apoiado.
* Não é suportado a desativação ou eliminação de utilizadores e grupos.

## <a name="next-steps"></a>Passos Seguintes

* [Disposição relativa à resolução de problemas](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem)
