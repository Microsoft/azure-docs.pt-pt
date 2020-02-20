---
title: Códigos de erro no portal de Diretório Ativo Azure  Microsoft Docs
description: Referência aos códigos de erro dos relatórios de atividades de início de sessão.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 02/19/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd37374ab6341356d84f205e92c9612d8481818f
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468852"
---
# <a name="sign-in-activity-report-error-codes"></a>Códigos de erro do relatório de atividade sessão 

Com as informações fornecidas pelo [relatório de inscrição](concept-sign-ins.md)do utilizador, encontra respostas a questões como:

- Quem inscreveu o meu pedido?
- Em que candidaturas foram assinadas?
- Que inscrições falharam e porquê?

Quando um sessão falha, verá um código de erro correspondente à falha. Este artigo enumera os códigos de erro e as suas descrições, juntamente com um curso de ação sugerido, se aplicável. 

## <a name="how-can-i-display-failed-sign-ins"></a>Como posso apresentar inícios de sessão com falha? 

No menu do [portal Azure,](https://portal.azure.com) selecione **Azure Ative Directory,** ou procure e selecione **Azure Ative Directory** a partir de qualquer página.

![Selecione Diretório Ativo Azure](./media/reference-sign-ins-error-codes/select-azure-active-directory.png "Azure Active Directory")

Sob **monitorização**, selecione **Sign-ins** para abrir o [relatório Desine .](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)

![Atividade de inscrição](./media/reference-sign-ins-error-codes/monitoring-sign-ins-in-azure-active-directory.png "Atividade de início de sessão")

Filtre o relatório para exibir todos os insados falhados selecionando **a Falha** da caixa de entrada de estado **de entrada.**

![Atividade de inscrição](./media/reference-sign-ins-error-codes/06.png "Atividade de início de sessão")

A seleção de um item da lista filtrada abre os Detalhes da **Atividade: Lâmina de insacada.** Esta visão fornece-lhe informações adicionais sobre o caso de início de sessão falhado, incluindo o **código de erro de inscrição** e a **razão**de falha .

![Atividade de inscrição](./media/reference-sign-ins-error-codes/05.png "Atividade de início de sessão")

Também pode aceder programáticamente aos dados de sessão utilizando a [API reportando](concept-reporting-api.md).

## <a name="error-codes"></a>Códigos de erro


|Erro|Descrição|
|---|---|
|16000|Trata-se de um detalhe de implementação interna e não de uma condição de erro. Pode ignorar com segurança esta referência.|
|20001|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|20012|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|20033|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|40008|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|40009|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|40014|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|50000|Há um problema no nosso serviço de início de sessão. [Crie um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para resolver este problema.|
|50001|O nome do principal de serviço não foi encontrado neste inquilino. Isto pode acontecer se o pedido não tiver sido instalado pelo administrador do inquilino, ou se o diretor de recursos não tiver sido encontrado no diretório ou se for inválido.|
|50002|O início de sessão falhou devido a acesso limitado ao proxy no inquilino. Se for a sua própria política de inquilinos, pode alterar as suas configurações restritas de inquilino para resolver este problema.|
|50003|O início de sessão falhou devido à falta da chave de assinatura ou do certificado. Isto poderá dever-se ao facto de não existir nenhuma chave de assinatura configurada na aplicação. Veja as resoluções descritas em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Se o problema persistir, contacte o proprietário da candidatura ou o administrador de aplicação.|
|50005|O utilizador tentou iniciar sessão num dispositivo a partir de uma plataforma que não é suportada atualmente através da política de Acesso Condicional.|
|50006| A verificação da assinatura falhou devido a assinatura inválida. Veja a resolução descrita em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Se o problema persistir, contacte o proprietário da aplicação ou o administrador de aplicação.|
|50007|O certificado de encriptação do parceiro não foi encontrado para esta aplicação. [Abra um bilhete](../fundamentals/active-directory-troubleshooting-support-howto.md) de suporte com a Microsoft para obter isto arranjado.|
|50008|A asserção SAML está em falta ou não está devidamente configurada no token. Contacte o seu fornecedor de federação.|
|50010|A validação do URI de audiência da aplicação falhou, pois não foram configuradas audiências de token. Contacte o proprietário do pedido para resolução.|
|50011|O endereço de resposta está em falta, está mal configurado ou não corresponde aos endereços de resposta configurados para a aplicação. Experimente a resolução enumerada em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Se o problema persistir, contacte o proprietário da aplicação ou o administrador de aplicação.|
|50012| Esta é uma mensagem de erro genérica que indica que a autenticação falhou. Isto pode acontecer por razões como credenciais ou reclamações em falta ou inválidas no pedido. Certifique-se de que o pedido é enviado com as credenciais e reclamações corretas. |
|50013|A afirmação é inválida por várias razões. Por exemplo, o emitente simbólico não corresponde à versão api dentro do seu intervalo de tempo válido, o token é expirado ou mal formado, ou o token de atualização na afirmação não é um token de atualização primária.|
|50017|A validação da certificação falhou devido a um dos seguintes motivos:<ul><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>Não foi possível encontrar o CrlSegment esperado</li><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>O ponto de distribuição de Delta CRL está configurado sem um ponto de distribuição de CRL correspondente</li><li>Não é possível obter segmentos de CRL válidos devido a um problema de tempo de ligação excedido</li><li>Não é possível transferir a CRL</li></ul>Contacte o administrador do inquilino.|
|50020|O utilizador não está autorizado por uma das seguintes razões.<ul><li>O utilizador está a tentar iniciar sessão com uma conta MSA com o ponto final v1</li><li>O utilizador não existe no inquilino.</li></ul> Contacte o proprietário da aplicação.|
|50027|Token JWT inválido devido aos seguintes motivos:<ul><li>Não contém a afirmação nonce, subafirmação</li><li>erro de correspondência do identificador de requerente</li><li>afirmação duplicada nas afirmações idToken</li><li>emissor inesperado</li><li>audiência inesperada</li><li>não está dentro do intervalo de tempo válido </li><li>o formato do token não é adequado</li><li>a verificação de assinatura falhou no token de ID externo do emissor</li></ul>Contacte o proprietário da aplicação.|
|50029|URI inválido: o nome de domínio contém carateres inválidos. Contacte o administrador do inquilino.|
|50034|O utilizador não existe no diretório. Contacte o seu administrador inquilino.|
|50042|O sal necessário para gerar um identificador em pares está em falta em princípio. Contacte o administrador do inquilino.|
|50048|O requerente não corresponde à afirmação do Emissor na asserção do cliente. Contacte o administrador do inquilino.|
|50050|O formato do pedido é incorreto. Contacte o proprietário da aplicação.|
|50053|A conta está bloqueada porque o utilizador tentou iniciar o seu insessão demasiadas vezes com um ID ou senha de utilizador incorreto.|
|50055|Palavra-passe inválida; palavra-passe expirada introduzida.|
|50056|Senha inválida ou nula - A palavra-passe não existe na loja para este utilizador.|
|50057|A conta de utilizador está desativada. A conta foi desativada por um administrador.|
|50058|A aplicação tentou executar um início de sessão automático e não foi possível iniciar a sessão do utilizador de forma silenciosa. A aplicação precisa de iniciar um fluxo interativo que dê aos utilizadores a opção de iniciar o seu início. Proprietário de pedido de contato.|
|50059|O utilizador não existe no diretório. Contacte o seu administrador inquilino.|
|50061|O pedido de fim de sessão é inválido. Contacte o proprietário da aplicação.|
|50072|O utilizador precisa de se inscrever para autenticação de dois fatores (interativo).|
|50074|O utilizador não passou na submissão da MFA.|
|50076|O utilizador não passou o desafio mfa (não interativo).|
|50078|A autenticação multifator apresentada expirou, você deve atualizar a autenticação multifator para acessar o.|
|50079|O utilizador precisa de se inscrever para a autenticação de dois fatores (logins não interativos).|
|50085|O token de atualização precisa de início de sessão de IDP social. Peça ao utilizador que tente fazer o sessão novamente com o seu nome de utilizador e senha.|
|50089|Ficha de fluxo expirada - A autenticação falhou. Peça ao utilizador que tente fazer o sessão novamente com o seu nome de utilizador e senha|
|50097|Autenticação do dispositivo necessária. Isto pode ocorrer porque as alegações do DeviceId ou do DeviceAltSecId são **nulas,** ou se não existir nenhum dispositivo correspondente ao identificador do dispositivo.|
|50099|A assinatura JWT é inválida. Contacte o proprietário da aplicação.|
|50105|O utilizador com sessão iniciada não tem uma função atribuída na aplicação em que a sessão está iniciada. Atribuir o utilizador à aplicação. Para obter mais informações: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|O objeto de realm de federação pedido não existe. Contacte o administrador do inquilino.|
|50120|Emitir com o cabeçalho JWT. Contacte o administrador do inquilino.|
|50124|A Transformação de Afirmações contém um parâmetro de entrada inválido. Contacte o administrador do inquilino para atualizar a apólice.|
|50125|O registo foi interrompido devido a um reset de senha ou entrada de inscrição de senha.|
|50126|Nome de utilizador ou palavra-passe inválido, ou nome de utilizador ou palavra-passe inválido no local.|
|50127|O utilizador precisa de instalar uma aplicação de corretor para ter acesso a este conteúdo.|
|50128|Nome de domínio inválido - Nenhuma informação de identificação do inquilino encontrada no pedido ou implícita por quaisquer credenciais fornecidas.|
|50129|O dispositivo não está no local de trabalho unido - A **adesão** ao local de trabalho é necessária para registar o dispositivo.|
|50130|O valor da reclamação não pode ser interpretado como método auth conhecido.|
|50131|Usado em vários erros de Acesso Condicional. Por exemplo, Mau estado do dispositivo Windows, pedido bloqueado devido a atividades suspeitas, decisões de políticas de acesso e de políticas de segurança.|
|50132|As credenciais foram revogadas devido aos seguintes motivos:<ul><li>O artefacto de SSO é inválido ou expirou</li><li>A sessão não é suficientemente nova para a aplicação</li><li>Foi enviado um início de sessão silencioso, mas a sessão do utilizador com o Azure AD é inválida ou expirou.</li></ul>|
|50133|A sessão é inválida devido à palavra-passe ter expirado ou ter sido alterada recentemente.|
|50135|A alteração da palavra-passe é necessária devido ao risco de conta.|
|50136|Redirecione a sessão de MSA para a aplicação - Sessão Única de MSA detetada. |
|50140|Este erro ocorreu devido à interrupção de "Manter sessão iniciada" quando o utilizador estava a iniciar sessão. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o ID de Correlação, o ID do Pedido e o código de Erro para obter mais detalhes. |
|50143|Sessão de sino - A sessão é inválida porque o inquilino utilizador não corresponde à sugestão de domínio devido a diferentes recursos.  [Abra um bilhete](../fundamentals/active-directory-troubleshooting-support-howto.md) de suporte com Id correlation, Id de Pedido e código de erro para obter mais detalhes.|
|50144|A palavra-passe do Active Directory do utilizador expirou. Gere uma nova palavra-passe para o utilizador ou tem o utilizador final a utilizar uma ferramenta de reset de autosserviço.|
|50146|Esta aplicação tem de ser configurada com uma chave de assinatura específica da aplicação. Ou não está configurada com uma chave destas ou a chave expirou ou ainda não é válida. Contacte o proprietário da aplicação.|
|50148|O code_verifier não corresponde ao code_challenge fornecido no pedido de autorização do PKCE. Contacte o desenvolvedor de aplicações. |
|50155|A autenticação do dispositivo falhou para este utilizador.|
|50158|O desafio de segurança externa não foi satisfeito.|
|50161|Os pedidos enviados por fornecedor externo não são suficientes, nem o pedido de reclamação em falta solicitado ao prestador externo.|
|50166|Não enviou pedido ao fornecedor de sinistros.|
|50169|O realm não é um realm configurado do espaço de nomes do serviço atual.|
|50172|O fornecedor de afirmações externo não foi aprovado. Contacte o administrador inquilino|
|50173|É necessário um token de autenticação atualizado. Volte a fazer o sessão do utilizador utilizando novas credenciais.|
|50177|O desafio externo não é suportado para os utilizadores passthrough.|
|50178|O Controlo de Sessão não é suportado para utilizadores de passagem.|
|50180|É necessária a Autenticação Integrada do Windows Ativar o inquilino para SSO Totalmente Integrado.|
|50181|Falha relacionada com ATP durante o inessão. |
|50194|A aplicação '{appId}'({appName}) não está configurada como uma aplicação multi-arrendatária. A utilização do ponto final /ponto final comum não é suportada para tais aplicações criadas após o '{time}'. Utilize um ponto final específico do arrendatário ou configure o pedido para ser multi-inquilino.|
|50201|Esta interrupção imediata da mensagem será mostrada ao utilizador durante o login quando devem ser fornecidas informações adicionais ao utilizador.|
|51001|A Dica de Domínio não está presente no Identificador de Segurança No Local - On-Premises UPN.|
|51004|A conta de utilizador não existe no diretório.|
|51006|É necessária a Autenticação Integrada do Windows O utilizador iniciou a utilização de token de sessão que está em falta por reclamação. Pedir ao utilizador que volte a iniciar sessão.|
|52004|O utilizador não deu consentimento para acesso a recursos do LinkedIn. |
|53000|A política de acesso condicional requer um dispositivo em conformidade e o dispositivo não está em conformidade. O utilizador inscreva o seu dispositivo com um fornecedor de MDM aprovado como o Intune.|
|53001|A política de acesso condicional requer um dispositivo associado a um domínio, o que não é o caso deste dispositivo. Mande o utilizador utilizar um dispositivo de domínio unido.|
|53002|Aplicação utilizada não é um pedido aprovado de Acesso Condicional. Para obter acesso, o utilizador tem de utilizar uma das aplicações na lista de aplicações aprovadas.|
|53003|O acesso foi bloqueado devido às políticas de Acesso Condicional.|
|53004|O utilizador tem de concluir o processo de registo na autenticação multifator antes de aceder a este conteúdo. O utilizador deve registar-se na autenticação multifator.|
|53032|O Acount foi bloqueado devido às políticas de Proteção de Identidade da AD Azure.|
|65001|A aplicação X não tem permissão para aceder à aplicação Y ou a permissão foi revogada. Ou o utilizador ou o administrador não permitiu utilizar a aplicação com o ID X. Envie um pedido de autorização interativo para este utilizador e este recurso. Ou o utilizador ou o administrador não permitiu utilizar a aplicação com o ID X. Envie um pedido de autorização ao administrador do seu inquilino para agir em nome da Aplicação :Y para o Recurso : Z.|
|65004|O utilizador recusou dar autorização para aceder à aplicação. Pedir ao utilizador que repita o início de sessão e que autorize o acesso à aplicação|
|65005|A lista de acesso a recursos necessária para a aplicação não contém aplicações detetáveis pelo recurso ou a aplicação cliente pediu acesso a um recurso que não foi especificado nesta lista de acesso a recursos necessária ou o serviço Graph devolveu um pedido inválido ou o recurso não foi encontrado. Se a aplicação suportar SAML, poderá ter configurado a aplicação com o identificador (Entidade) incorreto. Experimente a resolução indicada para SAML através da ligação abaixo: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|Concessão inválida devido aos seguintes motivos:<ul><li>A asserção de SAML 2.0 pedida tem um Método de Confirmação de Requerente inválido</li><li>O fluxo OnBehalfOf da aplicação não é suportado em V2</li><li>O token de atualização principal não está assinado com a chave de sessão</li><li>Token de atualização externo inválido</li><li>A concessão de acesso foi obtida para outro inquilino.</li></ul>|
|70001|A aplicação com o nome X não foi encontrada no inquilino com o nome Y. Este erro pode acontecer se a aplicação com o identificador X não tiver sido instalada pelo administrador do inquilino ou não tiver sido permitida por qualquer utilizador do inquilino. Pode ter configurado mal o valor do Identificador para a aplicação ou enviado o seu pedido de autenticação ao inquilino errado.|
|70002|A aplicação devolveu credenciais de cliente inválidas. Contacte o proprietário da aplicação.|
|70003|A aplicação devolveu um tipo de concessão não suportada. Contacte o proprietário da aplicação.|
|70004|A aplicação devolveu um URI de redirecionamento inválido. O endereço de redirecionamento especificado pelo cliente não corresponde a nenhum dos endereços configurados ou a nenhum dos endereços na lista de aprovações OIDC. Contacte o proprietário da aplicação.|
|70005|A aplicação devolveu um tipo de resposta não suportada devido aos seguintes motivos:<ul><li>o tipo de resposta “token” não está ativado na aplicação</li><li>o tipo de resposta “id_token” requer o âmbito “OpenID”; contém um valor de parâmetro de OAuth não suportado no wctx codificado</li></ul>Contacte o proprietário da aplicação.|
|70007|A aplicação devolveu um valor não suportado de “response_mode” durante um pedido de token. Contacte o proprietário da aplicação.|
|70008|O código de autorização ou token de atualização fornecido expirou ou foi revogado. Peça ao utilizador que se retente a fazer sessão.|
|70011|O âmbito que a aplicação pediu é inválido. Contacte o proprietário da aplicação.|
|70012|Ocorreu um erro de servidor ao autenticar um utilizador MSA (consumidor). Tente novamente o sign-in, e se a questão persistir, [abra um bilhete](../fundamentals/active-directory-troubleshooting-support-howto.md) de apoio |
|70018|Código de verificação inválido devido ao Utilizador ter introduzido o código de utilizador errado no fluxo de códigos do dispositivo. A autorização não está aprovada.|
|70019|O código de verificação expirou. Peça ao utilizador que tente novamente o sessão.|
|70037|Foi fornecida uma resposta errada para o desafio. A sessão de autenticação remota foi recusada.|
|70043|Gestão da sessão de acesso condicional do Azure obriga à sessão a expirar|
|70044|Gestão da sessão de acesso condicional do Azure obriga à sessão a expirar|
|75001|Ocorreu um erro durante o enlace de mensagem SAML.|
|75003|A aplicação devolveu um erro relacionado com o Enlace não suportado (a resposta do protocolo SAML não pode ser enviada através de enlaces que não HTTP POST). Contacte o proprietário da aplicação.|
|75005|O Azure AD não suporta o Pedido SAML enviado pela aplicação para Início de Sessão Único. Contacte o proprietário da aplicação.|
|75008|O pedido da aplicação foi recusado porque o pedido SAML tinha um destino inesperado. Contacte o proprietário da aplicação.|
|75011|O método de autenticação através do qual o utilizador se autenticou no serviço não corresponde ao método de autenticação pedido. Contacte o proprietário da aplicação.|
|75016|O Pedido de Autenticação SAML2 tem uma NameIdPolicy inválida. Contacte o proprietário da aplicação.|
|80001|O Agente de Autenticação não se consegue ligar ao Active Directory. Confirme que o agente de autenticação está instalado num computador associado a um domínio que tenha linha de visão para um controlador de domínio que possa servir o pedido de início de sessão do utilizador.|
|80002|Erro interno. Pedido de validação de palavra-passe cronometrado. Não conseguimos enviar o pedido de autenticação para o Serviço De Identidade Híbrida Interna. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80003|O Agente de Autenticação recebeu uma resposta inválida. Ocorreu um erro desconhecido ao tentar autenticar no Active Directory no local. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80005|Agente de Autenticação: ocorreu um erro desconhecido ao processar a resposta do Agente de Autenticação. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80007|O Agente de Autenticação não conseguiu validar a palavra-passe do utilizador.|
|80010|O Agente de Autenticação não conseguiu desencriptar a palavra-passe. |
|80011|O Agente de Autenticação não conseguiu obter a chave de desencriptação.|
|80012|Os utilizadores tentaram iniciar sessão fora das horas permitidas (isto é especificado em AD).|
|80013|Não foi possível concluir a tentativa de autenticação devido a um desfasamento de horas entre o computador que está a executar o agente de autenticação e o AD. Corrija os problemas de sincronização de hora.|
|80014|Agente de autenticação cronometrado. [Abra um bilhete](../fundamentals/active-directory-troubleshooting-support-howto.md) de suporte com o código de erro, id de correlação e hora de data para obter mais detalhes sobre este erro.|
|81001|A permissão do Kerberos do utilizador é demasiado grande. Isto pode acontecer se o utilizador estiver em demasiados grupos e, por conseguinte, a permissão do Kerberos contém demasiadas adesões a grupos. Reduza as adesões a grupos do utilizador e tente novamente.|
|81005|Pacote de autenticação não suportado.|
|81007|O inquilino não está habilitado para SSO Sem Emenda.|
|81012|Isto não é uma condição de erro. Indica que o utilizador que tenta iniciar sessão no Azure AD é diferente do utilizador assinado no dispositivo. Pode ignorar com segurança este código nos registos.|
|90010|O pedido não é apoiado por várias razões. Por exemplo, o pedido é feito usando um método de pedido não suportado (apenas o método POST é suportado) ou o algoritmo de assinatura simbólica que foi solicitado não é suportado. Contacte o programador da aplicação.|
|90014| Faltava um campo necessário para uma mensagem protocole, contacte o proprietário da aplicação. Se for o proprietário da aplicação, certifique-se de que tem todos os parâmetros necessários para o pedido de login. |
|90051| Inválido Delegado Token. É especificado o ID nacional de nuvem inválido ({cloudId}).|
|90072| É necessário adicionar primeiro a conta como um utilizador externo no inquilino. Inscreva-se e inscreva-se novamente com uma conta Azure AD diferente.|
|90094| O aplicativo solicitou permissões às quais o usuário conectado não tem permissão para consentir e o usuário foi bloqueado. |
|90095| A aplicação solicitou permissões às quais o utilizador inscrito não está autorizado a consentir, e foi mostrado ao utilizador o formulário de pedido de consentimento do [administrador.](../manage-apps/configure-admin-consent-workflow.md) |
|130500|O registo telefónico foi bloqueado devido à Política Credencial do Utilizador.|
|500011| O diretor de recursos chamado <site address> não foi encontrado no inquilino chamado <tenant ID>. Isto pode acontecer se o pedido não tiver sido instalado pelo administrador do inquilino ou consentir com qualquer utilizador do arrendatário. Poderá ter enviado o pedido de autenticação para o inquilino errado.|
|500014|O recurso '{identifier}' está desativado.|
|500021| O inquilino é restringido por procuração da empresa. Negando o acesso aos recursos.|
|500121| A autenticação falhou durante o pedido de autenticação forte.|
|500133| A afirmação não está dentro do seu intervalo de tempo válido. Certifique-se de que o token de acesso não está expirado antes de o utilizar para a afirmação do utilizador, ou solicite um novo token.|
|500172|O certificado '{name}' emitido por '{emitente}' não é válido. Hora atual: '{curTime}'. Certificado Não Antes: '{startTime}'. Certificado NotAfter: '{endTime}'.|
|501291|A aplicação do cliente é uma aplicação Mam, o dispositivo não está registado e o pedido é enviado usando um corretor. A adesão ao local de trabalho tem de ser feita para registar o dispositivo antes de a aplicação poder ser acedida.|
|530003|O seu dispositivo é obrigado a ser gerido para aceder a este recurso.|
|530021|A aplicação não satisfaz os requisitos da aplicação aprovada pelo Acesso Condicional.|
|530032|Bloqueado pela política de segurança.| 
|700016|A aplicação com o identificador '{appIdentifier}' não foi encontrada no diretório '{tenantName}'. Isto pode acontecer se o pedido não tiver sido instalado pelo administrador do inquilino ou consentir com qualquer utilizador do arrendatário. Pode ter enviado o seu pedido de autenticação ao inquilino errado.|
|700051|Response_type 'ficha' não está habilitado para o pedido. O pedido solicitou um tipo de resposta não suportado devido aos seguintes motivos: response_type 'ficha' não está habilitado para o pedido. O proprietário da aplicação deve ir ao portal Azure ou ligar para o MS Graph para permitir a concessão implícita de acesso.|
|900432|O Cliente Confidencial não é suportado no pedido da Cross Cloud.|
|5000811|Não é possível verificar a assinatura de token SAML. O identificador de chave de assinatura não corresponde a nenhuma chave registrada válida.|
|7000215|Foi fornecido um segredo do cliente inválido.|
|7000218|O organismo de pedido deve conter o seguinte parâmetro: «client_assertion» ou "client_secret".|


## <a name="next-steps"></a>Passos Seguintes

* [Relatórios de inscrição na visão geral](concept-sign-ins.md)
* [Acesso programático aos relatórios da AD Azure](concept-reporting-api.md)
