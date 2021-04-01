---
title: Referência de código de erro
titleSuffix: Azure AD B2C
description: Uma lista dos códigos de erro que podem ser devolvidos pelo serviço Azure Ative Directory B2C.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/02/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f6ae806b7666d83652e6b82bac16d89f2f9ce7aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215438"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Códigos de erro: Azure Ative Directory B2C

Os seguintes erros podem ser devolvidos pelo serviço Azure Ative Directory B2C.

| Código de erro | Mensagem |
| ---------- | ------- |
| `AADB2C90002` | O recurso CORS {0} ' ' devolveu um 404 não encontrado. |
| `AADB2C90006` | O redireccionamento URI {0} ' fornecido no pedido não está registado para o id do cliente {1} '. |
| `AADB2C90007` | A aplicação associada ao id ' do cliente {0} ' ' não tem URIs de redirecionamento registado. |
| `AADB2C90008` | O pedido não contém um parâmetro de identificação do cliente. |
| `AADB2C90010` | O pedido não contém um parâmetro de âmbito. |
| `AADB2C90011` | O id ' fornecido pelo cliente {0} no pedido não corresponde à identificação do cliente ' registada na {1} apólice. |
| `AADB2C90012` | O âmbito de aplicação {0} » fornecido a pedido não é apoiado. |
| `AADB2C90013` | O tipo de resposta solicitado {0} » »» fornecido no pedido não é apoiado. |
| `AADB2C90014` | O modo de resposta solicitado {0} » fornecido no pedido não é apoiado. |
| `AADB2C90016` | O tipo de afirmação do cliente solicitado {0} ' ' não corresponde ao tipo esperado {1} '. |
| `AADB2C90017` | A afirmação do cliente fornecida no pedido é inválida: {0} |
| `AADB2C90018` | O id ' do cliente {0} especificado no pedido não está registado no inquilino {1} '. |
| `AADB2C90019` | O recipiente-chave com id {0} ' in tenant ' ' não tem uma chave {1} válida. Razão: {2} . |
| `AADB2C90021` | O perfil técnico {0} ' ' não existe na política ' ' do inquilino {1} {2} '. |
| `AADB2C90022` | Incapaz de devolver metadados para a política ' {0} 'in tenant ' {1} '. |
| `AADB2C90023` | O perfil {0} ' ' não contém a tecla de metadados necessária {1} '. |
| `AADB2C90025` | O perfil {0} ' na política ' {1} 'in tenant ' ' ' não {2} contém a chave criptográfica necessária {3} '. |
| `AADB2C90027` | As credenciais básicas especificadas para ' {0} ' são inválidas. Verifique se as credenciais estão corretas e que o acesso foi concedido pelo recurso. |
| `AADB2C90028` | O certificado de cliente especificado para ' {0} ' ' é inválido. Verifique se o certificado está correto, contém uma chave privada e que o acesso foi concedido pelo recurso. |
| `AADB2C90031` | A política ' {0} ' não especifica uma viagem de utilizador padrão. Certifique-se de que a apólice ou os seus pais especificam uma viagem de utilizador predefinido como parte de uma secção de partidos dependentes. |
| `AADB2C90035` | O serviço está temporariamente indisponível. Por favor, reda o redando depois de alguns minutos. |
| `AADB2C90036` | O pedido não contém um URI para redirecionar o utilizador para post logout. Especifique um URI no campo post_logout_redirect_uri parâmetro. |
| `AADB2C90037` | Ocorreu um erro durante o processamento do pedido. Por favor contacte o administrador do site a que está a tentar aceder. |
| `AADB2C90039` | O pedido contém uma afirmação do cliente, mas a política fornecida ' {0} 'in tenant ' {1} ' ' está faltando um client_secret na Política DePartidríia. |
| `AADB2C90040` | A viagem do utilizador {0} ' ' não contém um passo de envio de reclamações. |
| `AADB2C90043` | O pedido incluído no pedido contém valores inválidos. Esperado 'nenhum', 'login', 'consent' ou 'select_account'. |
| `AADB2C90044` | A alegação ' {0} ' não é apoiada pela reivindicação resolver {1} '. |
| `AADB2C90046` | Estamos a ter problemas em carregar o seu estado atual. É melhor tentar começar a sua sessão desde o início. |
| `AADB2C90047` | O recurso {0} ' ' contém erros de script impedindo-o de ser carregado. |
| `AADB2C90048` | Ocorreu uma exceção não manipulada no servidor. |
| `AADB2C90051` | Não foram encontrados fornecedores de sinistros adequados. |
| `AADB2C90052` | Nome de utilizador ou senha inválido. |
| `AADB2C90053` | Não foi possível encontrar um utilizador com a credencial especificada. |
| `AADB2C90054` | Nome de utilizador ou senha inválido. |
| `AADB2C90055` | O âmbito {0} » »» fornecido a pedido deve especificar um recurso, como o https://example.com/calendar.read » |
| `AADB2C90057` | O pedido fornecido não está configurado para permitir o fluxo implícito de OAuth. |
| `AADB2C90058` | A aplicação fornecida não está configurada para permitir clientes públicos. |
| `AADB2C99059` | O pedido fornecido deve apresentar um code_challenge. Necessário para aplicações de uma página única usando o fluxo de código de autorização.| 
| `AADB2C90067` | O post logout redirecionando URI {0} ' ' tem um formato inválido. Especificar um URL baseado em https como https://example.com/return ' ou para clientes nativos usar o cliente nativo IETF URI 'urn:ietf:wg:oauth:2.0:oob'. |
| `AADB2C90068` | A aplicação fornecida com ID {0} ' ' não é válida contra este serviço. Por favor, use uma aplicação criada através do portal B2C e tente novamente. |
| `AADB2C90075` | A troca de reclamações {0} ' ' especificada em passo {1} ' ' devolveu resposta de erro HTTP com Código {2} ' e Razão {3} '. |
| `AADB2C90077` | O utilizador não tem uma sessão existente e o parâmetro de pedido de pedido tem um valor de ' {0} '. |
| `AADB2C90079` | Os clientes devem enviar um client_secret ao resgatar uma subvenção confidencial. |
| `AADB2C90080` | A subvenção concedida expirou. Por favor, volte a autenticar e tente de novo. Tempo atual: {0} , Tempo emitido por Subvenção: , Conceder tempo de {1} validade da janela deslizante: {2} . |
| `AADB2C90081` | A client_secret especificada não corresponde ao valor esperado para este cliente. Por favor, corrija o client_secret e tente de novo. |
| `AADB2C90083` | Falta o parâmetro requerido pelo pedido: {0} . |
| `AADB2C90084` | Os clientes públicos não devem enviar um client_secret quando resgatar uma subvenção adquirida publicamente. |
| `AADB2C90085` | O serviço encontrou um erro interno. Por favor, reauthentica e tente de novo. |
| `AADB2C90086` | O grant_type fornecido {0} não é suportado. |
| `AADB2C90087` | A subvenção concedida não foi emitida para esta versão do objetivo do protocolo. |
| `AADB2C90088` | A subvenção concedida não foi emitida para este ponto final. Valor Real : {0} e Valor Esperado : {1} |
| `AADB2C90091` | Cancelamento do utilizador. |
| `AADB2C90092` | O pedido fornecido com identificação {0} ' ' é desativado para o inquilino {1} '. Por favor, ative a aplicação e tente novamente. |
| `AADB2C90107` | O pedido com identificação {0} ' ' não pode obter um sinal de identificação também porque o âmbito aberto não foi fornecido no pedido ou o pedido não está autorizado para o mesmo. |
| `AADB2C90108` | O passo de orquestração {0} ' ' não especifica um CpimIssuerTechnicalProfileReferenceId quando se esperava. |
| `AADB2C90110` | O parâmetro de âmbito deve incluir o "openid" ao solicitar um response_type que inclua "id_token". |
| `AADB2C90111` | A sua conta está bloqueada. Contacte a sua pessoa de apoio para desbloqueá-la e tente novamente. |
| `AADB2C90114` | A sua conta está temporariamente bloqueada para evitar uma utilização não autorizada. Tente novamente mais tarde. |
| `AADB2C90115` | Ao solicitar o response_type 'código', o parâmetro de âmbito deve incluir um recurso ou identificação do cliente para fichas de acesso e 'openid' para fichas de identificação. Além disso, inclua "offline_access" para fichas de atualização. |
| `AADB2C90117` | O âmbito {0} de aplicação » previsto no pedido não é apoiado. |
| `AADB2C90118` | O utilizador esqueceu-se da sua senha. |
| `AADB2C90120` | O parâmetro da idade máxima {0} especificado no pedido é inválido. A idade máxima deve ser um inteiro entre ' {1} e {2} ' inclusive. |
| `AADB2C90122` | A entrada para ' {0} ' recebida no pedido falhou na validação do pedido HTTP. Certifique-se de que a entrada não contém caracteres como < ou &. |
| `AADB2C90128` | A conta associada a esta subvenção já não existe. Por favor, reauthentica e tente de novo. |
| `AADB2C90129` | A subvenção concedida foi revogada. Por favor, reauthentica e tente de novo. |
| `AADB2C90145` | Não foram encontrados números de telefone não verificados e a política não permite que um utilizador introduza o número. |
| `AADB2C90146` | O âmbito de aplicação {0} » fornecido a pedido especifica mais do que um recurso para um token de acesso, que não é suportado. |
| `AADB2C90149` | O guião {0} não conseguiu carregar. |
| `AADB2C90151` | O utilizador excedeu o número máximo de retrações para a autenticação de vários fatores. |
| `AADB2C90152` | Um pedido de pesquisa de vários fatores não obteve uma resposta do serviço. |
| `AADB2C90154` | Um pedido de verificação de vários fatores não conseguiu obter uma identificação de sessão do serviço. |
| `AADB2C90155` | Um pedido de verificação de vários fatores falhou com a {0} razão' |
| `AADB2C90156` | Um pedido de validação de vários fatores falhou com a {0} razão' |
| `AADB2C90157` | O utilizador excedeu o número máximo de retró seguintes para um passo autoafirmado. |
| `AADB2C90158` | Um pedido de validação autoafirmado falhou com a {0} razão. |
| `AADB2C90159` | Um pedido de verificação autoafirmado falhou com a {0} razão. |
| `AADB2C90161` | Uma resposta autoafirmada falhou com a {0} razão. |
| `AADB2C90165` | A mensagem de início da SAML com id {0} ' não pode ser encontrada no estado. |
| `AADB2C90168` | O pedido HTTP-Redirect não contém o parâmetro exigido {0} » para um pedido assinado. |
| `AADB2C90178` | O certificado de assinatura {0} ' ' não tem chave privada. |
| `AADB2C90182` | O code_verifier fornecido não corresponde a code_challenge associados |
| `AADB2C90183` | O code_verifier fornecido é inválido |
| `AADB2C90184` | O code_challenge_method fornecido não é suportado. Os valores suportados são simples ou S256 |
| `AADB2C90188` | O perfil técnico SAML {0} ' ' especifica um URL da Entidade de Parceiro de ' {1} ', mas a procura dos metadados falha com a razão {2} '. |
| `AADB2C90194` | A reclamação {0} » especificada para o token do portador não está presente nos créditos disponíveis. Reivindicações disponíveis ' {1} . |
| `AADB2C90205` | Esta aplicação não tem permissões suficientes contra este recurso web para realizar a operação. |
| `AADB2C90206` | Ocorreu uma inidência no tempo de iniciais do cliente. |
| `AADB2C90208` | O parâmetro id_token_hint fornecido expirou. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90209` | O parâmetro id_token_hint fornecido não contém uma audiência aceite. Valores de audiência válidos: {0} '. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90210` | O parâmetro id_token_hint previsto não pôde ser validado. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90211` | O pedido continha um cookie de estado incompleto. |
| `AADB2C90212` | O pedido continha um cookie de estado inválido. |
| `AADB2C90220` | O recipiente-chave no inquilino ' {0} ' com identificador de armazenamento {1} ' existe, mas não contém um certificado válido. O certificado pode ser expirado ou o seu certificado pode tornar-se ativo no futuro (nbf). |
| `AADB2C90223` | Ocorreu um erro na higienização do recurso CORS. |
| `AADB2C90224` | O fluxo do proprietário de recursos não foi ativado para a aplicação. |
| `AADB2C90225` | O nome de utilizador ou senha fornecido no pedido é inválido. |
| `AADB2C90226` | A troca de fichas especificada só é suportada por HTTP POST. |
| `AADB2C90232` | O parâmetro id_token_hint fornecido não contém um emitente aceite. Emitentes válidos: {0} '. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90233` | O parâmetro id_token_hint fornecido falhou na validação da assinatura. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90235` | O id_token fornecido expirou. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90237` | O id_token fornecido não contém um público válido. Valores de audiência válidos: {0} '. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90238` | O id_token fornecido não contém um emitente válido. Valores de emitente válidos: {0} ' ' Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90239` | A validação de assinaturas id_token fornecida. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90240` | O id_token fornecido é mal formado e não pode ser analisado. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90242` | O perfil técnico DA SAML {0} » especifica a Entidade Deseminada CDATA que não pode ser carregada por razão». {1} |
| `AADB2C90243` | A chave/segredo do cliente do IDP não está devidamente configurada. |
| `AADB2C90244` | Há muitos pedidos neste momento. Por favor, espere um pouco e tente de novo. |
| `AADB2C90248` | O fluxo do proprietário de recursos só pode ser utilizado por aplicações criadas através do portal de administração B2C. |
| `AADB2C90250` | O ponto final genérico de login não é suportado. |
| `AADB2C90255` | A troca de créditos especificada no perfil técnico {0} não foi concluída como esperado. É melhor tentar começar a sua sessão desde o início. |
| `AADB2C90261` | A troca de sinistros {0} ' especificada em passo ' {1} ' devolveu resposta de erro HTTP que não podia ser analisada. |
| `AADB2C90272` | O parâmetro id_token_hint não foi especificado no pedido. Por favor, forneça o símbolo e tente de novo. |
| `AADB2C90273` | Uma resposta inválida foi recebida: {0} ' |
| `AADB2C90274` | Os metadados do fornecedor não especificam um único serviço de logout ou a ligação ao ponto final não é um dos 'urn:oasis:names:tc:2.0:bindings:HTTP-Redirect' ou 'urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST'. |
| `AADB2C90276` | O pedido não é coerente com a definição de controlo {0} ' ' ' em técnica {1} {2} '' ' para a política ' {3} ' ' inquilino {4} '. |
| `AADB2C90277` | O passo de orquestração {0} ' da viagem de utilizador {1} ' ' da política ' ' não contém uma referência de {2} definição de conteúdo. |
| `AADB2C90279` | O id '' do cliente fornecido {0} não corresponde ao id cliente que emitiu a subvenção. |
| `AADB2C90284` | O pedido com identificador {0} » não foi autorizado e não pode ser utilizado para contas locais. |
| `AADB2C90285` | O pedido com identificador {0} ' não foi encontrado. |
| `AADB2C90288` | UserJourney com id {0} ' referenciado em TechnicalProfile {1} ' ' para atualização de resgate simbólico para inquilino {2} ' ' não existe na política ' ou em qualquer uma das suas políticas de {3} base. |
| `AADB2C90289` | Deparámo-nos com um erro de ligação ao fornecedor de identidade. Tente novamente mais tarde. |
| `AADB2C90296` | A aplicação não foi configurada corretamente. Por favor contacte o administrador do site a que está a tentar aceder. |
| `AADB2C99005` | O pedido contém um parâmetro de âmbito inválido que inclui um carácter ilegal ' {0} . |
| `AADB2C99006` | O Azure AD B2C não consegue encontrar a aplicação de extensões com id {0} '. Visite https://go.microsoft.com/fwlink/?linkid=851224 para mais informações. |
| `AADB2C99011` | O valor dos metadados {0} ' ' não foi especificado no 'TechnicalProfile' {1} 'in policy ' {2} '. |
| `AADB2C99013` | A combinação fornecida grant_type {0} [] e token_type {1} [] não é suportada. |
| `AADB2C99015` | O perfil ' {0} na política {1} ' 'in tenant ' ' ' está {2} faltando todos os InputClaims necessários para o fluxo credencial de senha do proprietário de recursos. |
