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
ms.openlocfilehash: 6cf5b6d4cadf4592fbaa19c89a969b20e9eb69b6
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424271"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Resolver problemas do Proxy de aplicações e mensagens de erro

Ao solucionar problemas de proxy de aplicativo, recomendamos que você comece com a revisão do fluxo de solução de problemas, [depurar problemas do conector de proxy de aplicativo](application-proxy-debug-connectors.md), para determinar se os conectores de proxy de aplicativo estão configurados corretamente. Se você ainda estiver tendo problemas para se conectar ao aplicativo, siga o fluxo de solução de problemas em depurar aplicativos de [proxy de aplicativo](application-proxy-debug-apps.md).

Se ocorrerem erros na Acessando um aplicativo publicado ou na publicação de aplicações, verifique as seguintes opções para ver se o Proxy de aplicações do Microsoft Azure AD está a funcionar corretamente:

* Abra o console de serviços do Windows. Verifique se o serviço **conector de proxy de aplicativo do Microsoft AAD** está habilitado e em execução. Também poderá ver a página de propriedades do serviço de Proxy de aplicações, conforme mostrado na imagem seguinte:  
  ![Captura de ecrã de janela de propriedades de conector do Proxy de aplicação do Microsoft AAD](./media/application-proxy-troubleshoot/connectorproperties.png)
* Abra o Visualizador de eventos e procure eventos de conector de Proxy de aplicações no **Applications and Services Logs** > **Microsoft** > **AadApplicationProxy**  >  **Conector** > **administrador**.
* Se for necessário, os registos mais detalhados são disponíveis por [ativar os registos de sessão do conector de Proxy de aplicações](application-proxy-connectors.md#under-the-hood).

## <a name="the-page-is-not-rendered-correctly"></a>A página não é processada corretamente
Pode ter problemas com a sua aplicação de processamento ou está a funcionar incorretamente sem receber mensagens de erro específicas. Isto pode ocorrer se publicou o caminho do artigo, mas a aplicação requer o conteúdo existente fora desse caminho.

Por exemplo, se publicar o caminho `https://yourapp/app` mas o aplicativo chama imagens `https://yourapp/media`, eles não serão compostos. Certifique-se de que pode publicar a aplicação através do caminho de nível mais alto, que precisa incluir todos os conteúdos relevantes. Neste exemplo, seria `http://yourapp/`.

Se alterar o caminho para incluir conteúdo referenciado, mas ainda precisar que os utilizadores aterrar num link-se no caminho, consulte a mensagem de blogue [definir a ligação correta para aceder a aplicações de Proxy de aplicações no Azure AD painel e iniciador de aplicações do Office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Erros de conector

Se o registo falhar durante a instalação do Assistente de conector, existem duas formas de ver o motivo da falha. No registo de eventos em qualquer um ter um aspeto **aplicativos e serviços Logs\Microsoft\AadApplicationProxy\Connector\Admin**, ou execute o seguinte comando do Windows PowerShell:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

Depois de localizar o erro do conector de registo de eventos, utilize esta tabela dos erros comuns para resolver o problema:

| Erro | Passos recomendados |
| ----- | ----------------- |
| Falha no registro do conector: Certifique-se de que você habilitou o proxy de aplicativo no Portal de Gerenciamento do Azure e que inseriu seu nome de usuário e senha do Active Directory corretamente. Erro: ' Ocorreu um ou mais erros. ' | Se fechou a janela de registo sem iniciar sessão no Azure AD, execute novamente o Assistente do conector e registar o conector. <br><br> Se a janela de registro abrir e fechar imediatamente sem permitir que você faça logon, você provavelmente receberá esse erro. Este erro ocorre quando existe um erro de sistema de rede no seu sistema. Certifique-se de que é possível conectar-se de um navegador a um site público e que as portas estejam abertas conforme especificado nos [pré-requisitos do proxy de aplicativo](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |
| Limpar erro é apresentado na janela de registo. Não é possível continuar | Se vir este erro e, em seguida, fecha a janela, que introduziu o nome de utilizador errado ou a palavra-passe. Tente novamente. |
| Falha no registro do conector: Certifique-se de que você habilitou o proxy de aplicativo no Portal de Gerenciamento do Azure e que inseriu seu nome de usuário e senha do Active Directory corretamente. Erro: 'AADSTS50059: Nenhuma informação de identificação de locatário encontrada na solicitação ou implícita por qualquer credencial fornecida e a pesquisa pelo URI da entidade de serviço falhou. | Você está tentando entrar usando uma conta da Microsoft e não um domínio que faz parte da ID da organização do diretório que você está tentando acessar. Certifique-se de que o administrador faz parte do mesmo nome de domínio como o domínio de inquilino, por exemplo, se o domínio do Azure AD for contoso.com, o administrador deve ser admin@contoso.com. |
| Falha ao obter a política de execução atual para executar scripts do PowerShell. | Se a instalação do conector falhar, verifique se a política de execução do PowerShell não está desabilitada. <br><br>1. Abra o Editor de diretiva de grupo.<br>2. Aceda a **configuração do computador** > **modelos administrativos** > **componentes do Windows**  >   **Windows PowerShell** e faça duplo clique **ativar a execução do Script**.<br>3. A política de execução pode ser definida para o **não configurado** ou **ativado**. Se definido como **Enabled**, certifique-se de que em Opções, a diretiva de execução está definida como **permitir que scripts locais e remotos scripts assinados** ou a **permitir que todos os scripts**. |
| Não foi possível transferir a configuração do conector. | Certificado de cliente do conector, o que é utilizado para autenticação, expirou. Também pode ocorrer se tiver o conector instalado atrás de um proxy. Neste caso, o conector não é possível aceder à Internet e não será capaz de fornecer aplicativos a usuários remotos. Renovar a confiança manualmente usando o `Register-AppProxyConnector` cmdlet no Windows PowerShell. Se o seu conector for atrás de um proxy, é necessário conceder acesso à Internet para as contas de conector "serviços de rede" e "local system". Isso pode ser feito por conceder-lhes acesso ao Proxy ou configurando-os para ignorar o proxy. |
| Falha no registro do conector: Verifique se você é um administrador de aplicativos de seu Active Directory para registrar o conector. Erro: ' A solicitação de registro foi negada. ' | O alias que está a tentar iniciar a sessão com não é um administrador deste domínio. O conector é sempre instalado para o diretório que detém o domínio do utilizador. Certifique-se de que a conta de administrador com a qual você está tentando entrar tenha pelo menos permissões de administrador do aplicativo para o locatário do Azure AD. |
| O conector não pôde se conectar ao serviço devido a problemas de rede. O conector tentou acessar a URL a seguir. | O conector não pode se conectar ao serviço de nuvem do proxy de aplicativo. Isso pode acontecer se você tiver uma regra de firewall bloqueando a conexão. Certifique-se de que você tenha permitido acesso às portas e às URLS corretas listadas em [pré-requisitos de proxy de aplicativo](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

## <a name="kerberos-errors"></a>Erros de Kerberos

Esta tabela abrange os erros mais comuns que vêm da configuração de Kerberos e a configuração e inclui sugestões para resolução.

| Erro | Passos recomendados |
| ----- | ----------------- |
| Falha ao obter a política de execução atual para executar scripts do PowerShell. | Se a instalação do conector falhar, verifique para se certificar de que a política de execução do PowerShell não está desativada.<br><br>1. Abra o Editor de diretiva de grupo.<br>2. Aceda a **configuração do computador** > **modelos administrativos** > **componentes do Windows**  >   **Windows PowerShell** e faça duplo clique **ativar a execução do Script**.<br>3. A política de execução pode ser definida para o **não configurado** ou **ativado**. Se definido como **Enabled**, certifique-se de que em Opções, a diretiva de execução está definida como **permitir que scripts locais e remotos scripts assinados** ou a **permitir que todos os scripts**. |
| 12008 - as do azure AD foi excedido o número máximo de tentativas de autenticação do Kerberos permitidos para o servidor de back-end. | Este erro poderá indicar uma configuração incorreta entre o Azure AD e o servidor de aplicações de back-end ou um problema na configuração de data e hora em ambas as máquinas. O servidor de back-end recusou o pedido de Kerberos criado pelo Azure AD. Certifique-se de que o Azure AD e o servidor de back-end de aplicações estão configuradas corretamente. Certifique-se de que a configuração de data e hora no Azure AD e o servidor de aplicações de back-end são sincronizadas. |
| 13016 - as do azure AD não é possível obter um tíquete Kerberos em nome do utilizador, porque não existe nenhum UPN no token de limite ou o cookie de acesso. | Existe um problema com a configuração de STS. Corrija a configuração de afirmação UPN no STS. |
| 13019 - as do azure AD não é possível obter um tíquete Kerberos em nome do utilizador devido ao erro seguinte de API geral. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor de controlador de domínio ou um problema na configuração de data e hora em ambas as máquinas. O controlador de domínio recusou o pedido de Kerberos criado pelo Azure AD. Certifique-se de que o Azure AD e o servidor de aplicações de back-end estão configurados corretamente, especialmente a configuração do SPN. Certificar-se de que o Azure AD está associado ao mesmo domínio que o controlador de domínio para se certificar de que o controlador de domínio estabelece a fidedignidade com o Azure AD. Certifique-se de que a configuração de data e hora no Azure AD e o controlador de domínio são sincronizadas. |
| 13020 - as do azure AD não é possível obter um tíquete Kerberos em nome do utilizador, porque o SPN do servidor de back-end não está definido. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor de controlador de domínio ou um problema na configuração de data e hora em ambas as máquinas. O controlador de domínio recusou o pedido de Kerberos criado pelo Azure AD. Certifique-se de que o Azure AD e o servidor de aplicações de back-end estão configurados corretamente, especialmente a configuração do SPN. Certificar-se de que o Azure AD está associado ao mesmo domínio que o controlador de domínio para se certificar de que o controlador de domínio estabelece a fidedignidade com o Azure AD. Certifique-se de que a configuração de data e hora no Azure AD e o controlador de domínio são sincronizadas. |
| 13022 - as do azure AD não é possível autenticar o utilizador porque o servidor de back-end reage a tentativas de autenticação de Kerberos com um erro de HTTP 401. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor de aplicações de back-end ou um problema na configuração de data e hora em ambas as máquinas. O servidor de back-end recusou o pedido de Kerberos criado pelo Azure AD. Certifique-se de que o Azure AD e o servidor de back-end de aplicações estão configuradas corretamente. Certifique-se de que a configuração de data e hora no Azure AD e o servidor de aplicações de back-end são sincronizadas. Para obter mais informações, consulte [resolver problemas de Kerberos restrita delegação configurações de Proxy de aplicações](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Erros de utilizador final

Esta lista inclui os erros que os utilizadores finais poderão surgir quando tentarem aceder à aplicação e efetuar a ativação. 

| Erro | Passos recomendados |
| ----- | ----------------- |
| O Web site não é possível apresentar a página. | O utilizador pode obter este erro ao tentar aceder à aplicação que publicou se o aplicativo é um aplicativo de IWA. O SPN definido para esta aplicação pode estar incorreto. Para aplicações IWA, certifique-se de que o SPN configurado para esta aplicação está correto. |
| O Web site não é possível apresentar a página. | O utilizador pode obter este erro ao tentar aceder à aplicação que publicou se o aplicativo é um aplicativo do OWA. Isto pode ser causado por um dos seguintes:<br><li>O SPN definido para esta aplicação está incorreto. Certifique-se de que o SPN configurado para esta aplicação está correto.</li><li>O utilizador que tentou aceder à aplicação está a utilizar uma conta Microsoft, em vez da conta empresarial adequada para iniciar sessão ou o utilizador é um utilizador convidado. Certifique-se de que o utilizador inicia sessão com a respetiva conta empresarial que corresponda ao domínio do aplicativo publicado. Os utilizadores da Microsoft Account e convidado não é possível aceder a aplicações de IWA.</li><li>O usuário que tentou acessar o aplicativo não está definido corretamente para este aplicativo no lado local. Verifique se esse usuário tem as permissões apropriadas, conforme definido para esse aplicativo de back-end no computador local. |
| Não é possível aceder esta aplicação empresarial. Não está autorizado a aceder a esta aplicação. Falha na autorização. Certifique-se de atribuir o utilizador com acesso a esta aplicação. | O usuário pode receber esse erro ao tentar acessar o aplicativo publicado se ele usar contas da Microsoft em vez de sua conta corporativa para entrar. Os utilizadores convidados também podem obter este erro. Os utilizadores da Microsoft Account e convidados não é possível aceder a aplicações de IWA. Certifique-se de que o utilizador inicia sessão com a respetiva conta empresarial que corresponda ao domínio do aplicativo publicado.<br><br>Pode não ter atribuída ao utilizador para esta aplicação. Vá para o **aplicativo** separador e, em **utilizadores e grupos**, atribuir este utilizador ou grupo de utilizadores a esta aplicação. |
| Esta aplicação empresarial não é possível aceder neste momento. Tente novamente mais tarde... O conector excedeu o tempo limite. | O usuário pode receber esse erro ao tentar acessar o aplicativo que você publicou se eles não estiverem definidos corretamente para esse aplicativo no lado local. Certifique-se de que os usuários tenham as permissões apropriadas, conforme definido para esse aplicativo de back-end no computador local. |
| Não é possível aceder esta aplicação empresarial. Não está autorizado a aceder a esta aplicação. Falha na autorização. Certifique-se de que o utilizador tem uma licença do Azure Active Directory Premium ou Basic. | O usuário pode receber esse erro ao tentar acessar o aplicativo que você publicou se eles não tiverem sido explicitamente atribuídos com uma licença Premium/Basic pelo administrador do Assinante. Vá para Active Directory o subscritor **licenças** separador e certifique-se de que este utilizador ou grupo de utilizadores é atribuído uma licença Premium ou Basic. |
| Não foi possível encontrar um servidor com o nome de host especificado. | O usuário pode receber esse erro ao tentar acessar o aplicativo que você publicou se o domínio personalizado do aplicativo não estiver configurado corretamente. Verifique se você carregou um certificado para o domínio e configurou o registro DNS corretamente seguindo as etapas em [trabalhando com domínios personalizados no Azure proxy de aplicativo do AD](application-proxy-configure-custom-domain.md) |

## <a name="my-error-wasnt-listed-here"></a>Meu erro não listado aqui

Se ocorrer um erro ou problema com o Proxy da aplicação AD Azure que não está listado neste guia de resolução de problemas, gostaríamos de ouvir sobre ele. Envie um e-mail para nosso [equipa de comentários](mailto:aadapfeedback@microsoft.com) com os detalhes do erro tiver encontrado.

## <a name="see-also"></a>Consulte também
* [Ativar o Proxy da aplicação para o Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Publicar aplicações com o Proxy de aplicações](application-proxy-add-on-premises-application.md)
* [Ativar o início de sessão único](application-proxy-configure-single-sign-on-with-kcd.md)
* [Habilitar acesso condicional](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
