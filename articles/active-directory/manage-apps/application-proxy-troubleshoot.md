---
title: Resolver problemas de Proxy de aplicações | Documentos da Microsoft
description: Descreve como resolver erros no Proxy de aplicações do Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017; it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7be9a17bed2a39d16f813332c2d6effc03393264
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244228"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Resolver problemas do Proxy de aplicações e mensagens de erro

Ao resolver problemas de procuração de aplicações, recomendamos que comece a rever o fluxo de resolução de [problemas, problemas de Conector proxy de aplicação de aplicação dedebug,](application-proxy-debug-connectors.md)para determinar se os conectores proxy de aplicação estão configurados corretamente. Se ainda tiver problemas em ligar-se à aplicação, siga o fluxo de resolução de problemas nos [problemas de aplicação de Proxy de Aplicação de Aplicação de Debug](application-proxy-debug-apps.md).

Se ocorrerem erros na Acessando um aplicativo publicado ou na publicação de aplicações, verifique as seguintes opções para ver se o Proxy de aplicações do Microsoft Azure AD está a funcionar corretamente:

* Abra a consola Windows Services. Verifique se o serviço de **conector proxy proxy da aplicação Microsoft AAD** está ativado e em funcionamento. Também poderá ver a página de propriedades do serviço de Proxy de aplicações, conforme mostrado na imagem seguinte:  
  ![Microsoft AAD Application Proxy Connector Properties](./media/application-proxy-troubleshoot/connectorproperties.png)
* Abra o Espectador de Eventos e procure eventos de conector Proxy de aplicação em registos de **aplicações e serviços** > **Microsoft** > **AadApplicationProxy** > **Connector** > **Admin**.
* Se necessário, estão disponíveis registos mais [detalhados ligando os registos de sessão do conector Proxy](application-proxy-connectors.md#under-the-hood)da aplicação .

## <a name="the-page-is-not-rendered-correctly"></a>A página não é processada corretamente
Pode ter problemas com a sua aplicação de processamento ou está a funcionar incorretamente sem receber mensagens de erro específicas. Isto pode ocorrer se publicou o caminho do artigo, mas a aplicação requer o conteúdo existente fora desse caminho.

Por exemplo, se publicar o caminho `https://yourapp/app` mas a aplicação chama imagens em `https://yourapp/media`, elas não serão prestadas. Certifique-se de que pode publicar a aplicação através do caminho de nível mais alto, que precisa incluir todos os conteúdos relevantes. Neste exemplo, seria `http://yourapp/`.

Se alterar o seu caminho para incluir conteúdo referenciado, mas ainda precisar que os utilizadores aterrem num link mais profundo no caminho, consulte a publicação de blog [Definindo o link certo para aplicações proxy de aplicação no painel de acesso a AD Azure e no launcher de aplicações office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Erros de conector

Se o registo falhar durante a instalação do Assistente de conector, existem duas formas de ver o motivo da falha. Ou procure no registo de eventos em Registos de **Aplicações e Serviços\Microsoft\AadApplicationProxy\Connector\Admin,** ou execute o seguinte comando Windows PowerShell:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

Depois de localizar o erro do conector de registo de eventos, utilize esta tabela dos erros comuns para resolver o problema:

| Erro | Passos recomendados |
| ----- | ----------------- |
| Falha no registo do conector: Certifique-se de que ativou o Proxy de aplicações no Portal de gestão do Azure e que introduziu corretamente o nome de utilizador do Active Directory e a palavra-passe. Erro: "um ou mais erros ocorreram." | Se fechou a janela de registo sem iniciar sessão no Azure AD, execute novamente o Assistente do conector e registar o conector. <br><br> Se a janela de registo abrir e fechar imediatamente sem permitir que faça login, provavelmente terá este erro. Este erro ocorre quando existe um erro de sistema de rede no seu sistema. Certifique-se de que é possível ligar de um navegador a um site público e que as portas estão abertas conforme especificado nos [pré-requisitos do Proxy de Aplicação.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) |
| Limpar erro é apresentado na janela de registo. Não é possível continuar | Se vir este erro e, em seguida, fecha a janela, que introduziu o nome de utilizador errado ou a palavra-passe. Tente novamente. |
| Falha no registo do conector: Certifique-se de que ativou o Proxy de aplicações no Portal de gestão do Azure e que introduziu corretamente o nome de utilizador do Active Directory e a palavra-passe. Erro: "AADSTS50059: não existem informações de identificação de inquilino encontrado na solicitação explícita ou implícita por qualquer, desde que as credenciais e procurar por serviço de principal URI falhou. | Está a tentar assinar usando uma Conta Microsoft e não um domínio que faz parte da identificação da organização do diretório a que está a tentar aceder. Certifique-se de que o administrador faz parte do mesmo nome de domínio que o domínio do arrendatário, por exemplo, se o domínio AD Azure for contoso.com, o administrador deve ser admin@contoso.com. |
| Falha ao obter a política de execução atual para executar scripts do PowerShell. | Se a instalação connector falhar, verifique se a política de execução powerShell não está desativada. <br><br>1. Abra o Editor de Política do Grupo.<br>2. Vá à **configuração do computador** > **modelos administrativos** > **componentes do Windows** > Windows **PowerShell** e clique duas vezes **na execução**do script .<br>3. A política de execução pode ser definida para **não configurada** ou **ativada**. Se estiver programado para **Ativar,** certifique-se de que em Opções, a Política de Execução está definida para **permitir scripts locais e scripts assinados remotamente** ou **para permitir todos os scripts**. |
| Não foi possível transferir a configuração do conector. | Certificado de cliente do conector, o que é utilizado para autenticação, expirou. Também pode ocorrer se tiver o conector instalado atrás de um proxy. Neste caso, o conector não é possível aceder à Internet e não será capaz de fornecer aplicativos a usuários remotos. Renovar a confiança manualmente utilizando o cmdlet `Register-AppProxyConnector` no Windows PowerShell. Se o seu conector for atrás de um proxy, é necessário conceder acesso à Internet para as contas de conector "serviços de rede" e "local system". Isso pode ser feito por conceder-lhes acesso ao Proxy ou configurando-os para ignorar o proxy. |
| Registo do conector falhou: Certifique-se de que é administrador de aplicação do seu Diretório Ativo para registar o Conector. Erro: "o pedido de registo foi negado." | O alias que está a tentar iniciar a sessão com não é um administrador deste domínio. O conector é sempre instalado para o diretório que detém o domínio do utilizador. Certifique-se de que a conta de administração com a qual está a tentar assinar tem pelo menos permissões de administrador de candidatura para o inquilino da AD Azure. |
| O Connector não conseguiu ligar-se ao serviço devido a problemas de networking. O Conector tentou aceder ao seguinte URL. | O conector não consegue ligar-se ao serviço de nuvem Proxy application Proxy. Isto pode acontecer se tiver uma regra de firewall bloqueando a ligação. Certifique-se de que permitiu o acesso às portas e URLS corretos listados nos [pré-requisitos](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)do Proxy de Aplicação . |

## <a name="kerberos-errors"></a>Erros de Kerberos

Esta tabela abrange os erros mais comuns que vêm da configuração de Kerberos e a configuração e inclui sugestões para resolução.

| Erro | Passos recomendados |
| ----- | ----------------- |
| Falha ao obter a política de execução atual para executar scripts do PowerShell. | Se a instalação do conector falhar, verifique para se certificar de que a política de execução do PowerShell não está desativada.<br><br>1. Abra o Editor de Política do Grupo.<br>2. Vá à **configuração do computador** > **modelos administrativos** > **componentes do Windows** > Windows **PowerShell** e clique duas vezes **na execução**do script .<br>3. A política de execução pode ser definida para **não configurada** ou **ativada**. Se estiver programado para **Ativar,** certifique-se de que em Opções, a Política de Execução está definida para **permitir scripts locais e scripts assinados remotamente** ou **para permitir todos os scripts**. |
| 12008 - as do azure AD foi excedido o número máximo de tentativas de autenticação do Kerberos permitidos para o servidor de back-end. | Este erro poderá indicar uma configuração incorreta entre o Azure AD e o servidor de aplicações de back-end ou um problema na configuração de data e hora em ambas as máquinas. O servidor de back-end recusou o pedido de Kerberos criado pelo Azure AD. Certifique-se de que o Azure AD e o servidor de back-end de aplicações estão configuradas corretamente. Certifique-se de que a configuração de data e hora no Azure AD e o servidor de aplicações de back-end são sincronizadas. |
| 13016 - as do azure AD não é possível obter um tíquete Kerberos em nome do utilizador, porque não existe nenhum UPN no token de limite ou o cookie de acesso. | Existe um problema com a configuração de STS. Corrija a configuração de afirmação UPN no STS. |
| 13019 - as do azure AD não é possível obter um tíquete Kerberos em nome do utilizador devido ao erro seguinte de API geral. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor de controlador de domínio ou um problema na configuração de data e hora em ambas as máquinas. O controlador de domínio recusou o pedido de Kerberos criado pelo Azure AD. Certifique-se de que o Azure AD e o servidor de aplicações de back-end estão configurados corretamente, especialmente a configuração do SPN. Certificar-se de que o Azure AD está associado ao mesmo domínio que o controlador de domínio para se certificar de que o controlador de domínio estabelece a fidedignidade com o Azure AD. Certifique-se de que a configuração de data e hora no Azure AD e o controlador de domínio são sincronizadas. |
| 13020 - as do azure AD não é possível obter um tíquete Kerberos em nome do utilizador, porque o SPN do servidor de back-end não está definido. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor de controlador de domínio ou um problema na configuração de data e hora em ambas as máquinas. O controlador de domínio recusou o pedido de Kerberos criado pelo Azure AD. Certifique-se de que o Azure AD e o servidor de aplicações de back-end estão configurados corretamente, especialmente a configuração do SPN. Certificar-se de que o Azure AD está associado ao mesmo domínio que o controlador de domínio para se certificar de que o controlador de domínio estabelece a fidedignidade com o Azure AD. Certifique-se de que a configuração de data e hora no Azure AD e o controlador de domínio são sincronizadas. |
| 13022 - as do azure AD não é possível autenticar o utilizador porque o servidor de back-end reage a tentativas de autenticação de Kerberos com um erro de HTTP 401. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor de aplicações de back-end ou um problema na configuração de data e hora em ambas as máquinas. O servidor de back-end recusou o pedido de Kerberos criado pelo Azure AD. Certifique-se de que o Azure AD e o servidor de back-end de aplicações estão configuradas corretamente. Certifique-se de que a configuração de data e hora no Azure AD e o servidor de aplicações de back-end são sincronizadas. Para mais informações, consulte [Troubleshoot Kerberos Configurações restritas de delegação para procuração](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)de aplicação .  |

## <a name="end-user-errors"></a>Erros de utilizador final

Esta lista inclui os erros que os utilizadores finais poderão surgir quando tentarem aceder à aplicação e efetuar a ativação. 

| Erro | Passos recomendados |
| ----- | ----------------- |
| O Web site não é possível apresentar a página. | O utilizador pode obter este erro ao tentar aceder à aplicação que publicou se o aplicativo é um aplicativo de IWA. O SPN definido para esta aplicação pode estar incorreto. Para aplicações IWA, certifique-se de que o SPN configurado para esta aplicação está correto. |
| O Web site não é possível apresentar a página. | O utilizador pode obter este erro ao tentar aceder à aplicação que publicou se o aplicativo é um aplicativo do OWA. Isto pode ser causado por um dos seguintes:<br><li>O SPN definido para esta aplicação está incorreto. Certifique-se de que o SPN configurado para esta aplicação está correto.</li><li>O utilizador que tentou aceder à aplicação está a utilizar uma conta Microsoft, em vez da conta empresarial adequada para iniciar sessão ou o utilizador é um utilizador convidado. Certifique-se de que o utilizador inicia sessão com a respetiva conta empresarial que corresponda ao domínio do aplicativo publicado. Os utilizadores da Microsoft Account e convidado não é possível aceder a aplicações de IWA.</li><li>O utilizador que tentou aceder à aplicação não está devidamente definido para esta aplicação no lado do local. Certifique-se de que este utilizador dispõe das permissões adequadas, tal como definidas para esta aplicação de backend na máquina de instalações. |
| Não é possível aceder esta aplicação empresarial. Não está autorizado a aceder a esta aplicação. Falha na autorização. Certifique-se de atribuir o utilizador com acesso a esta aplicação. | O utilizador poderá ter este erro ao tentar aceder à aplicação que publicou se utilizar as contas da Microsoft em vez da sua conta corporativa para iniciar sessão. Os utilizadores convidados também podem obter este erro. Os utilizadores da Microsoft Account e convidados não é possível aceder a aplicações de IWA. Certifique-se de que o utilizador inicia sessão com a respetiva conta empresarial que corresponda ao domínio do aplicativo publicado.<br><br>Pode não ter atribuída ao utilizador para esta aplicação. Vá ao separador **Aplicação** e, em **utilizadores e grupos,** atribua este utilizador ou grupo de utilizadores a esta aplicação. |
| Esta aplicação empresarial não é possível aceder neste momento. Tente novamente mais tarde... O conector excedeu o tempo limite. | O utilizador poderá ter este erro ao tentar aceder à aplicação que publicou se não estiver devidamente definida para esta aplicação no lado do local. Certifique-se de que os seus utilizadores dispõem das permissões adequadas, tal como definidas para esta aplicação de backend na máquina de instalações. |
| Não é possível aceder esta aplicação empresarial. Não está autorizado a aceder a esta aplicação. Falha na autorização. Certifique-se de que o utilizador tem uma licença para o Azure Ative Directory Premium. | O utilizador poderá ter este erro ao tentar aceder à aplicação que publicou se não tiver sido explicitamente atribuída com uma licença Premium pelo administrador do assinante. Vá ao separador De **Licenças** de Diretório Ativo do assinante e certifique-se de que este utilizador ou grupo de utilizadores é atribuído a uma licença Premium. |
| Não foi possível encontrar um servidor com o nome de anfitrião especificado. | O utilizador poderá ter este erro ao tentar aceder à aplicação que publicou se o domínio personalizado da aplicação não estiver configurado corretamente. Certifique-se de que fez o upload de um certificado para o domínio e configurou corretamente o registo DNS seguindo os passos de [Trabalho com domínios personalizados em Procuração de Aplicação AD Azure](application-proxy-configure-custom-domain.md) |

## <a name="my-error-wasnt-listed-here"></a>Meu erro não listado aqui

Se ocorrer um erro ou problema com o Proxy da aplicação AD Azure que não está listado neste guia de resolução de problemas, gostaríamos de ouvir sobre ele. Envie um e-mail para a nossa equipa de [feedback](mailto:aadapfeedback@microsoft.com) com os detalhes do erro que encontrou.

## <a name="see-also"></a>Consulte também
* [Ativar procuração de aplicação para diretório ativo Azure](application-proxy-add-on-premises-application.md)
* [Publicar aplicações com procuração de aplicação](application-proxy-add-on-premises-application.md)
* [Ativar um único sinal](application-proxy-configure-single-sign-on-with-kcd.md)
* [Ativar o acesso condicional](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
