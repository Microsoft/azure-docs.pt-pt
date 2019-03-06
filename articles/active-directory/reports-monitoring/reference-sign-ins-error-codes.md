---
title: Códigos de erro dos relatórios de atividades de início de sessão no portal do Azure Active Directory | Microsoft Docs
description: Referência aos códigos de erro dos relatórios de atividades de início de sessão.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f1ce786b748fedd1ec4c722b28bc11c28672c2f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443407"
---
# <a name="sign-in-activity-report-error-codes"></a>Códigos de erro de relatório de atividades de início de sessão 

Com as informações fornecidas pelos [relatório de inícios de sessão do utilizador](concept-sign-ins.md), encontrará respostas a perguntas como:

- Quem iniciou sessão no meu aplicativo?
- Quais aplicativos foram a sessão iniciados no?
- Os inícios de sessão falhou por quê?

Quando um início de sessão falha, verá um código de erro correspondente para a falha. Este artigo lista os códigos de erro e suas descrições, juntamente com uma sugestão de curso de ação, quando aplicável. 

## <a name="how-can-i-display-failed-sign-ins"></a>Como posso apresentar inícios de sessão com falha? 

Navegue para o [relatório de inícios de sessão](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns) no [portal do Azure](https://portal.azure.com).

![Atividade de início de sessão](./media/reference-sign-ins-error-codes/61.png "Atividade de início de sessão")

Filtrar o relatório para apresentar inícios de sessão falhas todas selecionando **falha** partir a **estado de início de sessão** caixa pendente.

![Atividade de início de sessão](./media/reference-sign-ins-error-codes/06.png "Atividade de início de sessão")

Selecionar um item na lista filtrada abre o **detalhes da atividade: Inícios de sessão** painel. Esta vista fornece-lhe obter informações adicionais sobre o evento início de sessão falhada, incluindo o **código de erro de início de sessão** e **motivo da falha**.

![Atividade de início de sessão](./media/reference-sign-ins-error-codes/05.png "Atividade de início de sessão")

Pode também acessar programaticamente os dados de início de sessão com o [reporting API](concept-reporting-api.md).

## <a name="error-codes"></a>Códigos de erro


|Erro|Descrição|
|---|---|
|16000|Este é um detalhe de implementação interna e não uma condição de erro. Pode ignorar com segurança essa referência.|
|20001|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|20012|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|20033|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|40008|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|40009|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|40014|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|50000|Há um problema no nosso serviço de início de sessão. [Crie um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para resolver este problema.|
|50001|O nome do principal de serviço não foi encontrado neste inquilino. Isto pode acontecer se o aplicativo não tiver sido instalado pelo administrador do inquilino, ou se o recurso principal não foi localizado no diretório ou é inválido.|
|50002|O início de sessão falhou devido a acesso limitado ao proxy no inquilino. Se estiver em sua própria política de inquilino, pode alterar as definições de inquilino restrito para corrigir este problema.|
|50003|O início de sessão falhou devido à falta da chave de assinatura ou do certificado. Isto poderá dever-se ao facto de não existir nenhuma chave de assinatura configurada na aplicação. Veja as resoluções descritas em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Se o problema persistir, contacte o proprietário da aplicação ou o administrador do aplicativo.|
|50005|O utilizador tentou iniciar sessão num dispositivo a partir de uma plataforma que não é atualmente suportada através da política de acesso condicional.|
|50006| A verificação da assinatura falhou devido a assinatura inválida. Veja a resolução descrita em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Se o problema persistir, contacte o proprietário da aplicação ou o administrador da aplicação.|
|50007|O certificado de encriptação do parceiro não foi encontrado para esta aplicação. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com a Microsoft para obter isso.|
|50008|A asserção SAML está em falta ou não está devidamente configurada no token. Contacte o seu fornecedor de federação.|
|50010|A validação do URI de audiência da aplicação falhou, pois não foram configuradas audiências de token. Contacte o proprietário da aplicação para a resolução.|
|50011|O endereço de resposta está em falta, está mal configurado ou não corresponde aos endereços de resposta configurados para a aplicação. Experimente a resolução listada em [ https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application ](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Se o problema persistir, contacte o proprietário da aplicação ou o administrador da aplicação.|
|50012| Esta é uma mensagem de erro genérico que indica que a autenticação falhou. Isto pode acontecer por motivos como em falta ou inválido credenciais ou afirmações no pedido. Certifique-se de que a solicitação é enviada com as credenciais corretas e afirmações. |
|50013|Asserção é inválida devido a vários motivos. Por exemplo, o emissor de tokens não corresponde a versão de api dentro do seu intervalo de tempo válido, o token é expirados ou com formato incorreto ou o token de atualização a asserção não é um token de atualização principal.|
|50017|A validação da certificação falhou devido a um dos seguintes motivos:<ul><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>Não foi possível encontrar o CrlSegment esperado</li><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>O ponto de distribuição de Delta CRL está configurado sem um ponto de distribuição de CRL correspondente</li><li>Não é possível obter segmentos de CRL válidos devido a um problema de tempo de ligação excedido</li><li>Não é possível transferir a CRL</li></ul>Contacte o administrador de inquilino.|
|50020|O utilizador não está autorizado para um dos seguintes motivos.<ul><li>O utilizador está a tentar iniciar sessão com uma conta MSA com o ponto final v1</li><li>O utilizador não existe no inquilino.</li></ul> Contacte o proprietário da aplicação.|
|50027|Token JWT inválido devido aos seguintes motivos:<ul><li>Não contém a afirmação nonce, subafirmação</li><li>erro de correspondência do identificador de requerente</li><li>afirmação duplicada nas afirmações idToken</li><li>emissor inesperado</li><li>audiência inesperada</li><li>não está dentro do intervalo de tempo válido </li><li>o formato do token não é adequado</li><li>a verificação de assinatura falhou no token de ID externo do emissor</li></ul>Contacte o proprietário da aplicação.|
|50029|URI inválido: o nome de domínio contém carateres inválidos. Contacte o administrador de inquilino.|
|50034|O utilizador não existe no diretório. Contacte o administrador de inquilino.|
|50042|O salt necessário para gerar um identificador pairwise está em falta no princípio. Contacte o administrador de inquilino.|
|50048|O requerente não corresponde à afirmação do Emissor na asserção do cliente. Contacte o administrador de inquilino.|
|50050|O formato do pedido é incorreto. Contacte o proprietário da aplicação.|
|50053|Conta foi bloqueada porque o utilizador tentou iniciar sessão demasiadas vezes com um ID de utilizador incorretas ou a palavra-passe.|
|50055|Palavra-passe inválida; palavra-passe expirada introduzida.|
|50056|Palavra-passe inválido ou nulo – palavra-passe não existe no arquivo para este utilizador.|
|50057|A conta de utilizador está desativada. A conta foi desativada por um administrador.|
|50058|A aplicação tentou executar um início de sessão automático e não foi possível iniciar a sessão do utilizador de forma silenciosa. A aplicação tem de iniciar um fluxo interativo, proporcionando aos usuários uma opção para início de sessão. Contacte o proprietário da aplicação.|
|50059|O utilizador não existe no diretório. Contacte o administrador de inquilino.|
|50061|O pedido de fim de sessão é inválido. Contacte o proprietário da aplicação.|
|50072|Utilizador tem de se inscrever para a autenticação de dois fatores (interativa).|
|50074|O utilizador não passou na submissão da MFA.|
|50076|Utilizador não passou na submissão da MFA (não interativa).|
|50079|Utilizador tem de se inscrever para autenticação de dois fatores (inícios de sessão não interativo).|
|50085|O token de atualização precisa de início de sessão de IDP social. Tem o utilizador tentar iniciar sessão novamente com o respetivo nome de utilizador e palavra-passe.|
|50089|Fluxo de token expirou – falha na autenticação. Ter o utilizador tentar iniciar sessão novamente com o respetivo nome de utilizador e palavra-passe|
|50097|Autenticação de dispositivo necessária. Isto pode ocorrer porque são as afirmações DeviceId ou DeviceAltSecId **nulo**, ou não se existe nenhum dispositivo correspondente para o identificador de dispositivo.|
|50099|A assinatura JWT é inválida. Contacte o proprietário da aplicação.|
|50105|O utilizador com sessão iniciada não tem uma função atribuída na aplicação em que a sessão está iniciada. Atribua o utilizador à aplicação. Para obter mais informações: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|O objeto de realm de federação pedido não existe. Contacte o administrador de inquilino.|
|50120|Emitir com o cabeçalho JWT. Contacte o administrador de inquilino.|
|50124|A Transformação de Afirmações contém um parâmetro de entrada inválido. Contacte o administrador de inquilino para atualizar a política.|
|50125|Início de sessão foi interrompido devido a uma reposição de palavra-passe ou a entrada de registo de palavra-passe.|
|50126|Nome de utilizador inválido ou a palavra-passe, ou o nome de utilizador inválido no local ou a palavra-passe.|
|50127|Utilizador tem de instalar uma aplicação de Mediador para obter acesso a este conteúdo.|
|50128|Nome de domínio inválido - não existem informações de identificação de inquilino encontrado na solicitação explícita ou implícita por quaisquer credenciais fornecidas.|
|50129|Dispositivo não estiver associado a um - à área de trabalho **associação à área de trabalho** é necessária para registar o dispositivo.|
|50130|Valor de afirmação não pode ser interpretado como método de autenticação conhecidos.|
|50131|Utilizado em vários erros de acesso condicional. Por exemplo, Mau estado do dispositivo Windows, pedido bloqueado devido a atividades suspeitas, decisões de políticas de acesso e de políticas de segurança.|
|50132|As credenciais foram revogadas devido aos seguintes motivos:<ul><li>O artefacto de SSO é inválido ou expirou</li><li>A sessão não é suficientemente nova para a aplicação</li><li>Foi enviado um início de sessão silencioso, mas a sessão do utilizador com o Azure AD é inválida ou expirou.</li></ul>|
|50133|A sessão é inválida devido à palavra-passe ter expirado ou ter sido alterada recentemente.|
|50135|Alteração de palavra-passe é necessária devido ao risco de conta.|
|50136|Redirecione sessão MSA para aplicação - detetada MSA única sessão. |
|50140|Este erro ocorreu devido à interrupção de "Manter sessão iniciada" quando o utilizador estava a iniciar sessão. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o ID de Correlação, o ID do Pedido e o código de Erro para obter mais detalhes. |
|50143|Erro de correspondência de sessão - sessão é inválida porque o inquilino do utilizador não coincide com a sugestão de domínio devido a recursos diferentes.  [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o código de ID de correlação, o ID do pedido e o erro para obter mais detalhes.|
|50144|A palavra-passe do Active Directory do utilizador expirou. Gerar uma nova palavra-passe para o utilizador ou tem o utilizador final com a ferramenta de reposição de self-service.|
|50146|Esta aplicação tem de ser configurada com uma chave de assinatura específica da aplicação. Ou não está configurada com uma chave destas ou a chave expirou ou ainda não é válida. Contacte o proprietário da aplicação.|
|50148|O code_verifier não corresponde ao code_challenge fornecido no pedido de autorização do PKCE. Contacte o programador da aplicação. |
|50155|Falha na autenticação de dispositivo para este utilizador.|
|50158|Desafio de segurança externa não foi cumprido.|
|50161|Declarações enviadas por fornecedor externo não é suficiente, ou em falta afirmação pediu para fornecedor externo.|
|50166|Falha ao enviar pedido para o fornecedor de afirmações.|
|50169|O realm não é um realm configurado do espaço de nomes do serviço atual.|
|50172|O fornecedor de afirmações externo não foi aprovado. Contacte o administrador de inquilino|
|50173|É necessário um token de autenticação atualizado. Tem o utilizador inicie sessão novamente com as credenciais de raiz.|
|50177|Desafio externo não é suportado para os utilizadores de pass-through.|
|50178|Controlo de sessão não é suportado para os utilizadores de pass-through.|
|50180|É necessária a Autenticação Integrada do Windows Ativar o inquilino para SSO Totalmente Integrado.|
|51001|Sugestão de domínio não está presente com o identificador de segurança no local - UPN no local.|
|51004|A conta de utilizador não existe no diretório.|
|51006|É necessária a Autenticação Integrada do Windows Utilizador iniciado sessão com o token de sessão que está em falta por meio de afirmação. Pedir ao utilizador que volte a iniciar sessão.|
|52004|O utilizador não deu consentimento de acesso a recursos do LinkedIn. |
|53000|A política de acesso condicional requer um dispositivo em conformidade e o dispositivo não está em conformidade. Peça ao utilizador inscrever o dispositivo com um fornecedor de MDM aprovado, como o Intune.|
|53001|A política de acesso condicional requer um dispositivo associado a um domínio, o que não é o caso deste dispositivo. Tem do utilizador utilize um domínio associado ao dispositivo.|
|53002|A aplicação utilizada não é uma aplicação aprovada para acesso condicional. Para obter acesso, o utilizador tem de utilizar uma das aplicações na lista de aplicações aprovadas.|
|53003|O acesso foi bloqueado devido a políticas de acesso condicional.|
|53004|O utilizador tem de concluir o processo de registo na autenticação multifator antes de aceder a este conteúdo. O utilizador deve registar-se na autenticação multifator.|
|65001|A aplicação X não tem permissão para aceder à aplicação Y ou a permissão foi revogada. Ou o utilizador ou o administrador não permitiu utilizar a aplicação com o ID X. Envie um pedido de autorização interativo para este utilizador e este recurso. Ou o utilizador ou administrador não permitiu utilizar a aplicação com o ID X. envie um pedido de autorização para o seu administrador de inquilino para agir em nome da aplicação: Y para o recurso: Z.|
|65004|O utilizador recusou dar autorização para aceder à aplicação. Pedir ao utilizador que repita o início de sessão e que autorize o acesso à aplicação|
|65005|A lista de acesso a recursos necessária para a aplicação não contém aplicações detetáveis pelo recurso ou a aplicação cliente pediu acesso a um recurso que não foi especificado nesta lista de acesso a recursos necessária ou o serviço Graph devolveu um pedido inválido ou o recurso não foi encontrado. Se a aplicação suportar SAML, poderá ter configurado a aplicação com o identificador (Entidade) incorreto. Experimente a resolução indicada para SAML através da ligação abaixo: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|Concessão inválida devido aos seguintes motivos:<ul><li>A asserção de SAML 2.0 pedida tem um Método de Confirmação de Requerente inválido</li><li>O fluxo OnBehalfOf da aplicação não é suportado em V2</li><li>O token de atualização principal não está assinado com a chave de sessão</li><li>Token de atualização externo inválido</li><li>A concessão de acesso foi obtida para outro inquilino.</li></ul>|
|70001|A aplicação com o nome X não foi encontrada no inquilino com o nome Y. Este erro pode acontecer se a aplicação com o identificador X não tiver sido instalada pelo administrador do inquilino ou não tiver sido permitida por qualquer utilizador do inquilino. Poderá ter configurado incorretamente o valor do identificador para a aplicação ou enviado o pedido de autenticação para o inquilino errado.|
|70002|A aplicação devolveu credenciais de cliente inválidas. Contacte o proprietário da aplicação.|
|70003|A aplicação devolveu um tipo de concessão não suportada. Contacte o proprietário da aplicação.|
|70004|A aplicação devolveu um URI de redirecionamento inválido. O endereço de redirecionamento especificado pelo cliente não corresponde a nenhum dos endereços configurados ou a nenhum dos endereços na lista de aprovações OIDC. Contacte o proprietário da aplicação.|
|70005|A aplicação devolveu um tipo de resposta não suportada devido aos seguintes motivos:<ul><li>o tipo de resposta “token” não está ativado na aplicação</li><li>o tipo de resposta “id_token” requer o âmbito “OpenID”; contém um valor de parâmetro de OAuth não suportado no wctx codificado</li></ul>Contacte o proprietário da aplicação.|
|70007|A aplicação devolveu um valor não suportado de “response_mode” durante um pedido de token. Contacte o proprietário da aplicação.|
|70008|O código de autorização fornecido ou atualização de token expirou ou foi revogado. Tem a repetição de utilizador a iniciar sessão.|
|70011|O âmbito que a aplicação pediu é inválido. Contacte o proprietário da aplicação.|
|70012|Ocorreu um erro de servidor ao autenticar um utilizador MSA (consumidor). Repita o início de sessão, e se o problema persistir, [abrir um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Código de verificação inválido devido ao Utilizador ter introduzido o código de utilizador errado no fluxo de códigos do dispositivo. Autorização não está aprovada.|
|70019|O código de verificação expirou. Peça ao utilizador repita o início de sessão.|
|70037|Foi fornecida uma resposta errada para o desafio. A sessão de autenticação remota foi recusada.|
|75001|Ocorreu um erro durante o enlace de mensagem SAML.|
|75003|A aplicação devolveu um erro relacionado com o Enlace não suportado (a resposta do protocolo SAML não pode ser enviada através de enlaces que não HTTP POST). Contacte o proprietário da aplicação.|
|75005|O Azure AD não suporta o Pedido SAML enviado pela aplicação para Início de Sessão Único. Contacte o proprietário da aplicação.|
|75008|O pedido da aplicação foi recusado porque o pedido SAML tinha um destino inesperado. Contacte o proprietário da aplicação.|
|75011|O método de autenticação através do qual o utilizador se autenticou no serviço não corresponde ao método de autenticação pedido. Contacte o proprietário da aplicação.|
|75016|O Pedido de Autenticação SAML2 tem uma NameIdPolicy inválida. Contacte o proprietário da aplicação.|
|80001|O Agente de Autenticação não se consegue ligar ao Active Directory. Confirme que o agente de autenticação está instalado num computador associado a um domínio que tenha linha de visão para um controlador de domínio que possa servir o pedido de início de sessão do utilizador.|
|80002|Erro interno. O pedido de validação da palavra-passe atingiu o tempo limite. Não foi possível enviar o pedido de autenticação para o serviço de identidade híbrido interno. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80003|O Agente de Autenticação recebeu uma resposta inválida. Ocorreu um erro desconhecido ao tentar autenticar no Active Directory no local. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80005|Agente de autenticação: Ocorreu um erro desconhecido ao processar a resposta do agente de autenticação. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80007|O Agente de Autenticação não conseguiu validar a palavra-passe do utilizador.|
|80010|O Agente de Autenticação não conseguiu desencriptar a palavra-passe. |
|80011|O Agente de Autenticação não conseguiu obter a chave de desencriptação.|
|80012|Os utilizadores tentaram iniciar sessão fora permitidos horas (isto é especificado no AD).|
|80013|Não foi possível concluir a tentativa de autenticação devido a um desfasamento de horas entre o computador que está a executar o agente de autenticação e o AD. Corrija os problemas de sincronização de hora.|
|80014|O agente de autenticação excedeu o tempo limite. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o código de erro, ID de correlação e Datetime para obter mais detalhes sobre este erro.|
|81001|A permissão do Kerberos do utilizador é demasiado grande. Isto pode acontecer se o utilizador estiver em demasiados grupos e, por conseguinte, a permissão do Kerberos contém demasiadas adesões a grupos. Reduza as adesões a grupos do utilizador e tente novamente.|
|81005|Pacote de autenticação não suportado.|
|81007|Inquilino não está ativado para SSO totalmente integrado.|
|81012|Não se trata de uma condição de erro. Ele indica que o utilizador tentar iniciar sessão com o Azure AD é diferente do utilizador com sessão iniciado no dispositivo. Pode ignorar este código nos registos.|
|90010|O pedido não é suportado por vários motivos. Por exemplo, a solicitação é feita usando um método de pedido não suportado (o único método de mensagem é suportado) ou o algoritmo de assinatura de token que foi pedido não é suportado. Contacte o programador da aplicação.|
|90014| Um campo obrigatório de uma mensagem de protocolo estava em falta, contacte o proprietário da aplicação. Se for o proprietário da aplicação, certifique-se de que tem todos os parâmetros necessários para o pedido de início de sessão. |
|90072| A conta tem de ser adicionado como um utilizador externo no inquilino pela primeira vez. Fim de sessão e inicie sessão novamente com um diferente do Azure AD conta.|
|90094| A concessão de permissões de administrador. Peça ao administrador de inquilino para fornecer o consentimento para esta aplicação.|
|500133| A declaração não está dentro do seu intervalo de tempo válida. Certifique-se de que o token de acesso não expirou antes de o utilizar para a asserção de utilizador ou pedir um novo token.|

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral de relatórios de inícios de sessão](concept-sign-ins.md)
* [Acesso programático aos relatórios do Azure AD](concept-reporting-api.md)
