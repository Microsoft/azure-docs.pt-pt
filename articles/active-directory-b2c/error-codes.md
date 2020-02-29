---
title: Referência de código de erro
titleSuffix: Azure AD B2C
description: Uma lista dos códigos de erro que podem ser devolvidos pelo serviço Azure Ative Directory B2C.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b09b3c19ab1c5b23e56e25afc1d9631cd1caa68
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188669"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Códigos de erro: Diretório Ativo Azure B2C

Os seguintes erros podem ser devolvidos pelo serviço Azure Ative Directory B2C.

| Código de erro | Mensagem |
| ---------- | ------- |
| `AADB2C90002` | O recurso CORS '{0}' devolveu um 404 não encontrado. |
| `AADB2C90006` | O redirecionamento do{0}' de URI ' fornecido no pedido não está registado para o id do cliente '{1}'. |
| `AADB2C90007` | A aplicação associada ao id do cliente '{0}' não tem URIs redirecionais registados. |
| `AADB2C90008` | O pedido não contém um parâmetro de identificação do cliente. |
| `AADB2C90010` | O pedido não contém um parâmetro de âmbito. |
| `AADB2C90011` | O{0}do cliente ' fornecido no pedido não corresponde ao id do cliente '{1}' registado na política. |
| `AADB2C90012` | O âmbito de aplicação{0}" previsto a pedido não é apoiado. |
| `AADB2C90013` | O tipo de resposta solicitado '{0}' previsto no pedido não é apoiado. |
| `AADB2C90014` | O modo de resposta solicitado '{0}' fornecido no pedido não é suportado. |
| `AADB2C90016` | O tipo de afirmação do cliente solicitado '{0}' não corresponde ao tipo esperado '{1}'. |
| `AADB2C90017` | A afirmação do cliente fornecida no pedido é inválida: {0} |
| `AADB2C90018` | O cliente id '{0}' especificado no pedido não está registado em{1}' inquilino '. |
| `AADB2C90019` | O recipiente-chave com id '{0}' in tenant '{1}' não tem uma chave válida. Razão: {2}. |
| `AADB2C90021` | O perfil técnico "{0}" não existe na política "{1}" do inquilino '{2}'. |
| `AADB2C90022` | Incapaz de devolver metadados para a política '{0}' em{1}' inquilino '. |
| `AADB2C90023` | O perfil '{0}' não contém a chave de metadados necessária '{1}'. |
| `AADB2C90025` | Perfil '{0}' na política '{1}' em{2}' inquilino ' não contém a chave criptográfica necessária '{3}'. |
| `AADB2C90027` | As credenciais básicas especificadas para "{0}" são inválidas. Verifique se as credenciais estão corretas e que o acesso foi concedido pelo recurso. |
| `AADB2C90028` | O certificado de cliente especificado para '{0}' é inválido. Verifique se o certificado está correto, contém uma chave privada e que o acesso foi concedido pelo recurso. |
| `AADB2C90031` | A política '{0}' não especifica uma viagem padrão do utilizador. Certifique-se de que a apólice ou os seus pais especificam uma viagem de utilizador padrão como parte de uma secção de partido sinuosa. |
| `AADB2C90035` | O serviço está temporariamente indisponível. Por favor, tente de novo depois de alguns minutos. |
| `AADB2C90036` | O pedido não contém um URI para redirecionar o utilizador para o registo de correio. Especifique um URI no campo de parâmetros post_logout_redirect_uri. |
| `AADB2C90037` | Ocorreu um erro ao processar o pedido. Contacte o administrador do site a que está a tentar aceder. |
| `AADB2C90039` | O pedido contém uma afirmação do cliente, mas a política fornecida '{0}' in tenant '{1}' está faltando um client_secret na RelyingPartyPolicy. |
| `AADB2C90040` | A viagem do utilizador '{0}' não contém um passo de reclamações de envio. |
| `AADB2C90043` | O pedido incluído no pedido contém valores inválidos. Esperava 'nenhum', 'login', 'consentimento' ou 'select_account'. |
| `AADB2C90044` | A alegação "{0}' não é apoiada pela reivindicação resolver "{1}'. |
| `AADB2C90046` | Estamos a ter problemas em carregar o seu estado atual. Talvez queira tentar recomeçar a sua sessão desde o início. |
| `AADB2C90047` | O{0}do recurso contém erros de script que impedem que seja carregado. |
| `AADB2C90048` | Ocorreu uma exceção não tratada no servidor. |
| `AADB2C90051` | Não foram encontrados fornecedores de sinistros adequados. |
| `AADB2C90052` | Nome de utilizador ou palavra-passe inválido. |
| `AADB2C90053` | Não foi possível encontrar um utilizador com a credencial especificada. |
| `AADB2C90054` | Nome de utilizador ou palavra-passe inválido. |
| `AADB2C90055` | O âmbito de aplicação{0}previsto para o pedido deve especificar um recurso, como "https://example.com/calendar.read". |
| `AADB2C90057` | A aplicação fornecida não está configurada para permitir o fluxo Implícito OAuth. |
| `AADB2C90058` | A aplicação fornecida não está configurada para permitir clientes públicos. |
| `AADB2C90067` | O registo pós-logout redireciona o{0}' tem um formato inválido. Especifique um URL baseado em https, como 'https://example.com/return' ou para clientes nativos, utilize o cliente nativo iETF URI 'urn:ietf:wg:oauth:2.0:oob'. |
| `AADB2C90068` | O pedido fornecido com{0}' de identificação ' não é válido contra este serviço. Por favor, utilize uma aplicação criada através do portal B2C e tente novamente. |
| `AADB2C90075` | A troca de reclamações '{0}' especificado na etapa '{1}' retornou a resposta de erro http com código '{2}' e Razão '{3}'. |
| `AADB2C90077` | O utilizador não tem uma sessão existente e o parâmetro de solicitação tem um valor de '{0}'. |
| `AADB2C90079` | Os clientes devem enviar um client_secret ao resgatar uma subvenção confidencial. |
| `AADB2C90080` | A subvenção fornecida expirou. Por favor, reatenite e tente de novo. Tempo atual: {0}, Grant tempo emitido: {1}, Grant deslizar tempo de validade da janela: {2}. |
| `AADB2C90081` | O client_secret especificado não corresponde ao valor esperado para este cliente. Por favor, corrija o client_secret e tente de novo. |
| `AADB2C90083` | Falta o parâmetro necessário para o pedido: {0}. |
| `AADB2C90084` | Os clientes públicos não devem enviar uma client_secret ao resgatar uma subvenção adquirida publicamente. |
| `AADB2C90085` | O serviço encontrou um erro interno. Por favor, reatenite e tente de novo. |
| `AADB2C90086` | O grant_type fornecido [{0}] não é suportado. |
| `AADB2C90087` | A subvenção fornecida não foi emitida para esta versão do ponto final do protocolo. |
| `AADB2C90088` | A subvenção fornecida não foi emitida para este ponto final. Valor Real : {0} e Valor Esperado : {1} |
| `AADB2C90092` | O pedido fornecido com{0}de identificação é desativado para o inquilino '{1}'. Por favor, ative a aplicação e tente novamente. |
| `AADB2C90107` | O pedido com{0}de identificação não pode obter um token de identificação, quer porque o âmbito aberto não foi fornecido no pedido ou o pedido não está autorizado para o mesmo. |
| `AADB2C90108` | O passo de orquestração '{0}' não especifica um CpimIssuerTechnicalProfileReferenceId quando se esperava. |
| `AADB2C90110` | O parâmetro de âmbito deve incluir "openid" ao solicitar uma response_type que inclua "id_token". |
| `AADB2C90111` | A sua conta está trancada. Contacte a sua pessoa de apoio para desbloqueá-la e tente novamente. |
| `AADB2C90114` | A sua conta está temporariamente bloqueada para evitar uma utilização não autorizada. Tente novamente mais tarde. |
| `AADB2C90115` | Ao solicitar o 'código' response_type, o parâmetro de âmbito deve incluir um recurso ou identificação do cliente para fichas de acesso e 'openid' para fichas de identificação. Além disso, inclua 'offline_access' para fichas de atualização. |
| `AADB2C90117` | O âmbito de aplicação{0}previsto no pedido não é apoiado. |
| `AADB2C90118` | O utilizador esqueceu-se da sua senha. |
| `AADB2C90120` | O parâmetro de idade máxima '{0}' especificado no pedido é inválido. A idade máxima deve ser um inteiro entre "{1}" e '{2}' inclusive. |
| `AADB2C90122` | A entrada de '{0}' recebida no pedido falhou na validação do pedido http. Certifique-se de que a entrada não contém caracteres como < ou & |
| `AADB2C90128` | A conta associada a esta subvenção já não existe. Por favor, reatenite e tente de novo. |
| `AADB2C90129` | A subvenção fornecida foi revogada. Por favor, reatenite e tente de novo. |
| `AADB2C90145` | Não foram encontrados números de telefone não verificados e a política não permite que um utilizador tenha entrado no número. |
| `AADB2C90146` | O âmbito{0}previsto no pedido especifica mais de um recurso para um sinal de acesso, que não é suportado. |
| `AADB2C90149` | O guião '{0}' não carregou. |
| `AADB2C90151` | O utilizador excedeu o número máximo de repetições para a autenticação de vários fatores. |
| `AADB2C90152` | Um pedido de sondagem multifactor não obteve uma resposta do serviço. |
| `AADB2C90154` | Um pedido de verificação de vários fatores não conseguiu obter uma identificação de sessão do serviço. |
| `AADB2C90155` | Um pedido de verificação de vários fatores falhou com a razão{0}". |
| `AADB2C90156` | Um pedido de validação de vários fatores falhou com a razão "{0}'. |
| `AADB2C90157` | O utilizador excedeu o número máximo de tentativas de retry para um passo autoafirmado. |
| `AADB2C90158` | Um pedido de validação autoafirmado falhou com a razão "{0}'. |
| `AADB2C90159` | Um pedido de verificação autoafirmado falhou com a razão "{0}'. |
| `AADB2C90161` | Uma resposta de envio autoafirmada falhou com a razão "{0}'. |
| `AADB2C90165` | A mensagem de início da SAML com '{0}' não pode ser encontrada no estado. |
| `AADB2C90168` | O pedido http-redirecionamento não contém o parâmetro exigido{0}para um pedido assinado. |
| `AADB2C90178` | O certificado de assinatura '{0}' não tem chave privada. |
| `AADB2C90182` | O code_verifier fornecido não corresponde a code_challenge associadas |
| `AADB2C90183` | O code_verifier fornecido é inválido |
| `AADB2C90184` | O code_challenge_method fornecido não é suportado. Os valores suportados são simples ou S256 |
| `AADB2C90188` | O perfil técnico saml '{0}' especifica um URL de Entidade parceira de '{1}', mas buscar os metadados falha com a razão '{2}'. |
| `AADB2C90194` | A reclamação '{0}' especificado para o token portador não está presente nos pedidos disponíveis. Reivindicações disponíveis{1}'. |
| `AADB2C90205` | Esta aplicação não dispõe de permissões suficientes contra este recurso web para realizar a operação. |
| `AADB2C90206` | Ocorreu uma saída de tempo na inicialização do cliente. |
| `AADB2C90208` | Expira o parâmetro id_token_hint fornecido. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90209` | O parâmetro id_token_hint fornecido não contém um público aceite. Valores de audiência válidos: '{0}'. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90210` | O parâmetro id_token_hint fornecido não pôde ser validado. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90211` | O pedido continha um cookie de estado incompleto. |
| `AADB2C90212` | O pedido continha um cookie estatal inválido. |
| `AADB2C90220` | O contentor-chave do{0}do arrendatário com identificador de armazenamento '{1}' existe, mas não contém um certificado válido. O certificado pode caducar ou o seu certificado poderá tornar-se ativo no futuro (nbf). |
| `AADB2C90223` | Ocorreu um erro que sanitizou o recurso CORS. |
| `AADB2C90224` | O fluxo do proprietário de recursos não foi ativado para a aplicação. |
| `AADB2C90225` | O nome de utilizador ou palavra-passe fornecido no pedido são inválidos. |
| `AADB2C90226` | A troca de fichas especificada só é suportada em HTTP POST. |
| `AADB2C90232` | O parâmetro id_token_hint fornecido não contém um emitente aceite. Emitentes válidos: '{0}'. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90233` | O parâmetro id_token_hint fornecido falhou na validação da assinatura. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90235` | O id_token fornecido está expirado. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90237` | O id_token fornecido não contém um público válido. Valores de audiência válidos: '{0}'. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90238` | O id_token fornecido não contém um emitente válido. Valores emitentes válidos: '{0}'. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90239` | O id_token validação de assinatura falhada. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90240` | O id_token fornecido está mal formado e não pôde ser analisado. Por favor, forneça outro símbolo e tente de novo. |
| `AADB2C90242` | O perfil técnico SAML '{0}' especifica A Entidade Parceira CDATA que não pode ser carregada por razão '{1}'. |
| `AADB2C90243` | A chave/segredo do cliente do IDP não está devidamente configurada. |
| `AADB2C90244` | Há muitos pedidos neste momento. Por favor, espere um pouco e tente de novo. |
| `AADB2C90248` | O fluxo do proprietário de recursos só pode ser utilizado através de aplicações criadas através do portal de administração B2C. |
| `AADB2C90250` | O ponto final genérico de login não é suportado. |
| `AADB2C90255` | A troca de sinistros especificada no perfil técnico{0}não foi concluída como esperado. Talvez queira tentar recomeçar a sua sessão desde o início. |
| `AADB2C90261` | A troca de reclamações '{0}' especificado em passo '{1}' retornou a resposta de erro HTTP que não podia ser analisada. |
| `AADB2C90272` | O parâmetro id_token_hint não foi especificado no pedido. Por favor, forneça ficha e tente de novo. |
| `AADB2C90273` | Foi recebida uma resposta inválida: '{0}' |
| `AADB2C90274` | Os metadados do fornecedor não especificam um único serviço de logout ou a ligação do ponto final não é de 'urn:oásis:nomes:tc:SAML:2.0:bindings:HTTP-Redirect' ou 'urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST'. |
| `AADB2C90276` | O pedido não é coerente com a definição de controlo '{0}': '{1}' em{2}' de perfil técnico ' para a política '{3}' inquilino '{4}'. |
| `AADB2C90277` | O passo de orquestração '{0}' da viagem do utilizador '{1}' da política '{2}' não contém uma referência de definição de conteúdo. |
| `AADB2C90279` | O id '{0}' do cliente fornecido não corresponde ao id do cliente que emitiu a subvenção. |
| `AADB2C90284` | O pedido com{0}identificador não foi autorizado e não pode ser utilizado para contas locais. |
| `AADB2C90285` | O pedido com{0}identificador não foi encontrado. |
| `AADB2C90288` | UserJourney com id '{0}' referenciado em{1}' TechnicalProfile ' para refrescamento de redenção simbólica para o{2}' do inquilino ' não existe na política '{3}' ou qualquer uma das suas políticas base. |
| `AADB2C90289` | Deparámo-nos com um erro de ligação ao fornecedor de identidade. Por favor, tente de novo mais tarde. |
| `AADB2C90296` | A aplicação não foi configurada corretamente. Contacte o administrador do site a que está a tentar aceder. |
| `AADB2C99005` | O pedido contém um parâmetro de âmbito inválido que inclui um carácter ilegal{0}". |
| `AADB2C99006` | O Azure AD B2C não consegue encontrar a aplicação de extensões com{0}de identificação de aplicações. Visite https://go.microsoft.com/fwlink/?linkid=851224 para mais informações. |
| `AADB2C99011` | O valor dos metadados '{0}' não foi especificado no{1}' de perfil técnico ' na política '{2}'. |
| `AADB2C99013` | A combinação de grant_type fornecidas [{0}] e token_type [{1}] não é suportada. |
| `AADB2C99015` | Profile '{0}' na política '{1}' em{2}' inquilino ' está faltando todas as InputClaims necessárias para fluxo de credenciais de senha do proprietário de recursos. |
