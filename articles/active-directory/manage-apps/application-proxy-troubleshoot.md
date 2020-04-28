---
title: Procuração de aplicação de troubleshoot [ Procuração de problemas ] Microsoft Docs
description: Cobre como resolver erros em Procuração de Aplicação AD Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244228"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Problemas de procuração de aplicação de problemas e mensagens de erro

Ao resolver problemas de procuração de aplicações, recomendamos que comece a rever o fluxo de resolução de [problemas, problemas de Conector proxy de aplicação de aplicação dedebug,](application-proxy-debug-connectors.md)para determinar se os conectores proxy de aplicação estão configurados corretamente. Se ainda tiver problemas em ligar-se à aplicação, siga o fluxo de resolução de problemas nos [problemas de aplicação de Proxy de Aplicação de Aplicação de Debug](application-proxy-debug-apps.md).

Se ocorrerem erros no acesso a uma aplicação publicada ou em aplicações editoriais, verifique as seguintes opções para ver se o Microsoft Azure AD Application Proxy está a funcionar corretamente:

* Abra a consola Windows Services. Verifique se o serviço de **conector proxy proxy da aplicação Microsoft AAD** está ativado e em funcionamento. Também pode querer ver a página de propriedades do serviço Proxy de Aplicação, como mostra a seguinte imagem:  
  ![Microsoft AAD Application Proxy Connector Properties screenshot de janela](./media/application-proxy-troubleshoot/connectorproperties.png)
* Abra o Espectador de Eventos e procure eventos de conector Proxy de aplicação em **Registos de Aplicações e Serviços** > **Microsoft** > **AadApplicationProxy** > **Conector** > **Admin**.
* Se necessário, estão disponíveis registos mais [detalhados ligando os registos de sessão do conector Proxy](application-proxy-connectors.md#under-the-hood)da aplicação .

## <a name="the-page-is-not-rendered-correctly"></a>A página não é prestada corretamente
Pode ter problemas com a renderização da sua aplicação ou funcionamento incorretamente sem receber mensagens de erro específicas. Isto pode ocorrer se publicar o caminho do artigo, mas a aplicação requer conteúdo que exista fora desse caminho.

Por exemplo, se publicar `https://yourapp/app` o caminho mas `https://yourapp/media`a aplicação chama imagens, não serão renderizadas. Certifique-se de que publica a aplicação utilizando o caminho de nível mais alto necessário para incluir todos os conteúdos relevantes. Neste exemplo, seria. `http://yourapp/`

Se alterar o seu caminho para incluir conteúdo referenciado, mas ainda precisar que os utilizadores aterrem num link mais profundo no caminho, consulte a publicação de blog [Definindo o link certo para aplicações proxy de aplicação no painel de acesso a AD Azure e no launcher de aplicações office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Erros do conector

Se o registo falhar durante a instalação do assistente do Conector, existem duas formas de ver o motivo da falha. Ou procure no registo de eventos em Registos de **Aplicações e Serviços\Microsoft\AadApplicationProxy\Connector\Admin,** ou execute o seguinte comando Windows PowerShell:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

Assim que encontrar o erro do Conector a partir do registo do evento, utilize esta tabela de erros comuns para resolver o problema:

| Erro | Passos recomendados |
| ----- | ----------------- |
| Registo do conector falhou: Certifique-se de que ativou o Proxy de Aplicação no Portal de Gestão Do Azure e que introduziu corretamente o nome de utilizador e a palavra-passe do Diretório Ativo. Erro: "Ocorreram um ou mais erros." | Se fechar a janela de registo sem iniciar sessão no Azure AD, volte a executar o assistente do Conector e registe o Conector. <br><br> Se a janela de registo abrir e fechar imediatamente sem permitir que faça login, provavelmente terá este erro. Este erro ocorre quando há um erro de rede no seu sistema. Certifique-se de que é possível ligar de um navegador a um site público e que as portas estão abertas conforme especificado nos [pré-requisitos do Proxy de Aplicação.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) |
| É apresentado um erro claro na janela de registo. Não pode prosseguir | Se vir este erro e a janela fechar, introduziu o nome de utilizador errado ou a palavra-passe. Tentar novamente. |
| Registo do conector falhou: Certifique-se de que ativou o Proxy de Aplicação no Portal de Gestão Do Azure e que introduziu corretamente o nome de utilizador e a palavra-passe do Diretório Ativo. Erro: 'AADSTS50059: Nenhuma informação de identificação de inquilinos encontrada no pedido ou implícita por quaisquer credenciais fornecidas e pesquisa pelo diretor de serviço URI falhou. | Está a tentar assinar usando uma Conta Microsoft e não um domínio que faz parte da identificação da organização do diretório a que está a tentar aceder. Certifique-se de que o administrador faz parte do mesmo nome de domínio que o domínio do arrendatário, por exemplo, se o domínio AD Azure for contoso.com, o administrador deve ser admin@contoso.com. |
| Falhou em recuperar a atual política de execução para executar scripts PowerShell. | Se a instalação connector falhar, verifique se a política de execução powerShell não está desativada. <br><br>1. Abra o Editor de Política do Grupo.<br>2. Vá para a **configuração** > do computador**Modelos Administrativos** > **Windows Components** > **Windows PowerShell** e clique duas vezes **na execução**do script .<br>3. A política de execução pode ser definida para **não configurada** ou **ativada**. Se estiver programado para **Ativar,** certifique-se de que em Opções, a Política de Execução está definida para **permitir scripts locais e scripts assinados remotamente** ou **para permitir todos os scripts**. |
| O conector não descarregou a configuração. | O certificado de cliente do Conector, que é utilizado para autenticação, expirou. Isto também pode ocorrer se tiver o Conector instalado atrás de um proxy. Neste caso, o Connector não pode aceder à Internet e não poderá fornecer aplicações a utilizadores remotos. Renovar a confiança `Register-AppProxyConnector` manualmente utilizando o cmdlet no Windows PowerShell. Se o seu Conector estiver por detrás de um proxy, é necessário conceder acesso à Internet às contas connector "serviços de rede" e "sistema local". Isto pode ser realizado, quer concedendo-lhes acesso ao Proxy, quer colocando-os a contornar o proxy. |
| Registo do conector falhou: Certifique-se de que é administrador de aplicação do seu Diretório Ativo para registar o Conector. Erro: "O pedido de registo foi negado." | O pseudónimo com que está saindo não é um administrador neste domínio. O seu Conector está sempre instalado para o diretório que detém o domínio do utilizador. Certifique-se de que a conta de administração com a qual está a tentar assinar tem pelo menos permissões de administrador de candidatura para o inquilino da AD Azure. |
| O Connector não conseguiu ligar-se ao serviço devido a problemas de networking. O Conector tentou aceder ao seguinte URL. | O conector não consegue ligar-se ao serviço de nuvem Proxy application Proxy. Isto pode acontecer se tiver uma regra de firewall bloqueando a ligação. Certifique-se de que permitiu o acesso às portas e URLS corretos listados nos [pré-requisitos](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)do Proxy de Aplicação . |

## <a name="kerberos-errors"></a>Erros de Kerberos

Esta tabela cobre os erros mais comuns que vêm da configuração e configuração da Kerberos, e inclui sugestões de resolução.

| Erro | Passos recomendados |
| ----- | ----------------- |
| Falhou em recuperar a atual política de execução para executar scripts PowerShell. | Se a instalação connector falhar, verifique se a política de execução powerShell não está desativada.<br><br>1. Abra o Editor de Política do Grupo.<br>2. Vá para a **configuração** > do computador**Modelos Administrativos** > **Windows Components** > **Windows PowerShell** e clique duas vezes **na execução**do script .<br>3. A política de execução pode ser definida para **não configurada** ou **ativada**. Se estiver programado para **Ativar,** certifique-se de que em Opções, a Política de Execução está definida para **permitir scripts locais e scripts assinados remotamente** ou **para permitir todos os scripts**. |
| 12008 - A AD Azure excedeu o número máximo de tentativas de autenticação kerberos permitidas para o servidor backend. | Este erro pode indicar uma configuração incorreta entre o Azure AD e o servidor de aplicação backend, ou um problema na configuração de tempo e data em ambas as máquinas. O servidor de backend recusou o bilhete Kerberos criado pela Azure AD. Verifique se o Azure AD e o servidor de aplicação backend estão configurados corretamente. Certifique-se de que a configuração da hora e da data no AD Azure e no servidor de aplicação backend estão sincronizadas. |
| 13016 - A Azure AD não pode recuperar um bilhete Kerberos em nome do utilizador porque não existe UPN no token ou no cookie de acesso. | Há um problema com a configuração STS. Fixe a configuração de reclamação UPN no STS. |
| 13019 - A Azure AD não pode recuperar um bilhete Kerberos em nome do utilizador devido ao seguinte erro geral da API. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor do controlador de domínio, ou um problema na configuração de tempo e data em ambas as máquinas. O controlador de domínio recusou o bilhete Kerberos criado pela Azure AD. Verifique se o Azure AD e o servidor de aplicação backend estão configurados corretamente, especialmente a configuração SPN. Certifique-se de que o Azure AD é domínio unido ao mesmo domínio que o controlador de domínio para garantir que o controlador de domínio estabelece a confiança com a AD Azure. Certifique-se de que a configuração da hora e da data no AD Azure e no controlador de domínio estão sincronizadas. |
| 13020 - A Azure AD não pode recuperar um bilhete Kerberos em nome do utilizador porque o servidor de backend SPN não está definido. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor do controlador de domínio, ou um problema na configuração de tempo e data em ambas as máquinas. O controlador de domínio recusou o bilhete Kerberos criado pela Azure AD. Verifique se o Azure AD e o servidor de aplicação backend estão configurados corretamente, especialmente a configuração SPN. Certifique-se de que o Azure AD é domínio unido ao mesmo domínio que o controlador de domínio para garantir que o controlador de domínio estabelece a confiança com a AD Azure. Certifique-se de que a configuração da hora e da data no AD Azure e no controlador de domínio estão sincronizadas. |
| 13022 - A AD Azure não pode autenticar o utilizador porque o servidor backend responde às tentativas de autenticação kerberos com um erro HTTP 401. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor de aplicação backend, ou um problema na configuração de tempo e data em ambas as máquinas. O servidor de backend recusou o bilhete Kerberos criado pela Azure AD. Verifique se o Azure AD e o servidor de aplicação backend estão configurados corretamente. Certifique-se de que a configuração da hora e da data no AD Azure e no servidor de aplicação backend estão sincronizadas. Para mais informações, consulte [Troubleshoot Kerberos Configurações restritas de delegação para procuração](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)de aplicação .  |

## <a name="end-user-errors"></a>Erros do utilizador final

Esta lista cobre erros que os utilizadores finais podem encontrar quando tentam aceder à aplicação e falhar. 

| Erro | Passos recomendados |
| ----- | ----------------- |
| O site não pode exibir a página. | O utilizador poderá ter este erro ao tentar aceder à aplicação que publicou se a aplicação for uma aplicação IWA. O SPN definido para esta aplicação pode estar incorreto. Para aplicações IWA, certifique-se de que o SPN configurado para esta aplicação está correto. |
| O site não pode exibir a página. | O utilizador poderá ter este erro ao tentar aceder à aplicação que publicou se a aplicação for uma aplicação OWA. Isto pode ser causado por um dos seguintes:<br><li>O SPN definido para esta aplicação é incorreto. Certifique-se de que o SPN configurado para esta aplicação está correto.</li><li>O utilizador que tentou aceder à aplicação está a utilizar uma conta Microsoft em vez da conta corporativa adequada para iniciar sessão, ou o utilizador é um utilizador convidado. Certifique-se de que o utilizador assina na utilização da sua conta corporativa que corresponda ao domínio da aplicação publicada. Os utilizadores da Conta Microsoft e o hóspede não podem aceder às aplicações IWA.</li><li>O utilizador que tentou aceder à aplicação não está devidamente definido para esta aplicação no lado do local. Certifique-se de que este utilizador dispõe das permissões adequadas, tal como definidas para esta aplicação de backend na máquina de instalações. |
| Esta aplicação corporativa não pode ser acedida. Não está autorizado a aceder a este pedido. A autorização falhou. Certifique-se de atribuir ao utilizador acesso a esta aplicação. | O utilizador poderá ter este erro ao tentar aceder à aplicação que publicou se utilizar as contas da Microsoft em vez da sua conta corporativa para iniciar sessão. Os utilizadores convidados também podem ter este erro. Os utilizadores e hóspedes da Conta Microsoft não podem aceder às aplicações IWA. Certifique-se de que o utilizador assina na utilização da sua conta corporativa que corresponda ao domínio da aplicação publicada.<br><br>Pode não ter atribuído o utilizador a esta aplicação. Vá ao separador **Aplicação** e, em **utilizadores e grupos,** atribua este utilizador ou grupo de utilizadores a esta aplicação. |
| Esta aplicação corporativa não pode ser acedida agora. Por favor, tente de novo mais tarde... O conector tem um tempo. | O utilizador poderá ter este erro ao tentar aceder à aplicação que publicou se não estiver devidamente definida para esta aplicação no lado do local. Certifique-se de que os seus utilizadores dispõem das permissões adequadas, tal como definidas para esta aplicação de backend na máquina de instalações. |
| Esta aplicação corporativa não pode ser acedida. Não está autorizado a aceder a este pedido. A autorização falhou. Certifique-se de que o utilizador tem uma licença para o Azure Ative Directory Premium. | O utilizador poderá ter este erro ao tentar aceder à aplicação que publicou se não tiver sido explicitamente atribuída com uma licença Premium pelo administrador do assinante. Vá ao separador De **Licenças** de Diretório Ativo do assinante e certifique-se de que este utilizador ou grupo de utilizadores é atribuído a uma licença Premium. |
| Não foi possível encontrar um servidor com o nome de anfitrião especificado. | O utilizador poderá ter este erro ao tentar aceder à aplicação que publicou se o domínio personalizado da aplicação não estiver configurado corretamente. Certifique-se de que fez o upload de um certificado para o domínio e configurou corretamente o registo DNS seguindo os passos de [Trabalho com domínios personalizados em Procuração de Aplicação AD Azure](application-proxy-configure-custom-domain.md) |

## <a name="my-error-wasnt-listed-here"></a>O meu erro não foi listado aqui.

Se encontrar um erro ou problema com o Procurador de Aplicação AD Azure que não esteja listado neste guia de resolução de problemas, gostaríamos de saber. Envie um e-mail para a nossa equipa de [feedback](mailto:aadapfeedback@microsoft.com) com os detalhes do erro que encontrou.

## <a name="see-also"></a>Consulte também
* [Ativar procuração de aplicação para diretório ativo Azure](application-proxy-add-on-premises-application.md)
* [Publicar aplicações com o Proxy da Aplicação](application-proxy-add-on-premises-application.md)
* [Ativar um único sinal](application-proxy-configure-single-sign-on-with-kcd.md)
* [Ativar o acesso condicional](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
