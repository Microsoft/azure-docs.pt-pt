---
title: Aplicação de resolução de problemas Proxy | Microsoft Docs
description: Cobre como resolver erros no Azure AD Application Proxy.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 06/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: cb8fb0e194b4c43b5e247f2ea5d1e38d924591db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257968"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Resolver problemas do Proxy de Aplicações e as mensagens de erro

Ao resolver problemas problemas de aplicação Proxy, recomendamos que comece a rever o fluxo de resolução de [problemas, problemas de Conector de Procuração de Aplicações de Debug](application-proxy-debug-connectors.md), para determinar se os conectores de procuração de aplicação estão configurados corretamente. Se ainda tiver problemas em ligar-se à aplicação, siga o fluxo de resolução de problemas em problemas de [aplicação de aplicação de depuração](application-proxy-debug-apps.md).

Se ocorrerem erros no acesso a uma aplicação publicada ou nas aplicações de publicação, verifique as seguintes opções para ver se o Microsoft Azure AD Application Proxy está a funcionar corretamente:

* Abra a consola Windows Services. Verifique se o serviço **de conector de aplicação proxy da Aplicação Microsoft AAD** está ativado e em execução. Também pode querer olhar para a página de propriedades do serviço Application Proxy, como mostra a seguinte imagem:  
  ![Microsoft AAD Application Proxy Connector Properties screenshot](./media/application-proxy-troubleshoot/connectorproperties.png)
* Abrir o espectador de eventos e procurar eventos de conector de aplicação proxy em **aplicações e serviços**  >  **Microsoft**  >  **AadApplicationProxy**  >  **Connector**  >  **Admin**.
* Se necessário, estão disponíveis registos mais [detalhados, ligando os registos de sessão de conector de aplicação proxy](application-proxy-connectors.md#under-the-hood).

## <a name="the-page-is-not-rendered-correctly"></a>A página não é renderizada corretamente
Pode ter problemas com a sua aplicação a renderizar ou a funcionar incorretamente sem receber mensagens de erro específicas. Isto pode ocorrer se publicar o caminho do artigo, mas a aplicação requer conteúdo que exista fora desse caminho.

Por exemplo, se publicar o caminho `https://yourapp/app` mas a aplicação ligar as imagens, `https://yourapp/media` elas não serão renderizadas. Certifique-se de que publica a aplicação utilizando o caminho mais alto que necessita para incluir todos os conteúdos relevantes. Neste exemplo, `http://yourapp/` seria.

## <a name="connector-errors"></a>Erros do conector

Se o registo falhar durante a instalação do assistente do Conector, existem duas formas de ver o motivo da avaria. Ou procure no registo de eventos sob **Aplicações e Registos de Serviços\Microsoft\AadApplicationProxy\Connector\Admin**, ou executar o seguinte comando Windows PowerShell:

```powershell
Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1
```

Assim que encontrar o erro do Conector a partir do registo de eventos, utilize esta tabela de erros comuns para resolver o problema:

| Erro | Passos recomendados |
| ----- | ----------------- |
| O registo do conector falhou: Certifique-se de que ativou o Proxy da aplicação no Portal de Gestão do Azure e que inseriu corretamente o nome de utilizador e palavra-passe do seu Diretório Ativo. Erro: "Ocorreram um ou mais erros." | Se fechou a janela de registo sem iniciar sessão no Azure AD, volte a executar o assistente de conector e registe o Conector. <br><br> Se a janela de registo abrir e fechar imediatamente sem permitir o seu login, provavelmente terá este erro. Este erro ocorre quando existe um erro de ligação em rede no seu sistema. Certifique-se de que é possível ligar de um navegador a um site público e de que as portas estão abertas conforme especificado nos [pré-requisitos do Application Proxy.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) |
| O erro claro é apresentado na janela de registo. Não pode prosseguir | Se vir este erro e a janela fechar, introduziu o nome de utilizador ou palavra-passe errado. Tente novamente. |
| O registo do conector falhou: Certifique-se de que ativou o Proxy da aplicação no Portal de Gestão do Azure e que inseriu corretamente o nome de utilizador e palavra-passe do seu Diretório Ativo. Erro: 'AADSTS50059: Nenhuma informação de identificação de inquilinos encontrada no pedido ou implícita por quaisquer credenciais fornecidas e pesquisa pelo principal serviço URI falhou. | Está a tentar iniciar saúde usando uma Conta Microsoft e não um domínio que faz parte da identificação da organização do diretório a que está a tentar aceder. Certifique-se de que o administrador faz parte do mesmo nome de domínio que o domínio do inquilino, por exemplo, se o domínio AD AZure for contoso.com, o administrador deve ser admin@contoso.com . |
| Falhou em recuperar a atual política de execução para executar scripts PowerShell. | Se a instalação do Conector falhar, verifique se a política de execução powerShell não está desativada. <br><br>1. Abrir o Editor de Política do Grupo.<br>2. Aceda aos modelos administrativos de **configuração do**  >  **computador,**  >  **os componentes do Windows**  >  **PowerShell** e o duplo clique Ligue a **execução do script**.<br>3. A política de execução pode ser definida para **não configurar** ou **ativar**. Se definido para **Ativado**, certifique-se de que, em Opções, a Política de Execução está definida para **permitir scripts locais e scripts assinados remotamente** ou para **permitir todos os scripts**. |
| O conector não conseguiu descarregar a configuração. | O certificado de cliente do Conector, que é utilizado para a autenticação, expirou. Isto também pode ocorrer se tiver o Conector instalado atrás de um representante. Neste caso, o Conector não pode aceder à Internet e não poderá fornecer aplicações a utilizadores remotos. Renovar a confiança manualmente utilizando o `Register-AppProxyConnector` cmdlet no Windows PowerShell. Se o seu Conector estiver por detrás de um representante, é necessário conceder acesso à Internet às contas connector "serviços de rede" e "sistema local". Isto pode ser conseguido, concedendo-lhes acesso ao Proxy ou definindo-os para contornar o representante. |
| O registo do conector falhou: Certifique-se de que é administrador de aplicação do seu Diretório Ativo para registar o Conector. Erro: "O pedido de registo foi negado." | O pseudónimo com que está a tentar entrar não é um administrador neste domínio. O seu Conector está sempre instalado para o diretório que detém o domínio do utilizador. Certifique-se de que a conta de administração com a qual está a tentar assinar tem permissões de administrador de aplicação para o inquilino da AD Azure. |
| O Conector não conseguiu ligar-se ao serviço devido a problemas de ligação em rede. O Conector tentou aceder ao seguinte URL. | O conector não consegue ligar-se ao serviço de nuvem Proxy da Aplicação. Isto pode acontecer se tiver uma regra de firewall que bloqueie a ligação. Certifique-se de que permitiu o acesso às portas e URLS corretos listados nos [pré-requisitos de Procuração](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)de Aplicações . |

## <a name="kerberos-errors"></a>Erros de Kerberos

Esta tabela cobre os erros mais comuns que vêm da configuração e configuração de Kerberos, e inclui sugestões de resolução.

| Erro | Passos recomendados |
| ----- | ----------------- |
| Falhou em recuperar a atual política de execução para executar scripts PowerShell. | Se a instalação do Conector falhar, verifique se a política de execução powerShell não está desativada.<br><br>1. Abrir o Editor de Política do Grupo.<br>2. Aceda aos modelos administrativos de **configuração do**  >  **computador,**  >  **os componentes do Windows**  >  **PowerShell** e o duplo clique Ligue a **execução do script**.<br>3. A política de execução pode ser definida para **não configurar** ou **ativar**. Se definido para **Ativado**, certifique-se de que, em Opções, a Política de Execução está definida para **permitir scripts locais e scripts assinados remotamente** ou para **permitir todos os scripts**. |
| 12008 - A AD Azure excedeu o número máximo de tentativas de autenticação de Kerberos permitidas para o servidor backend. | Este erro pode indicar uma configuração incorreta entre o Azure AD e o servidor de aplicação backend, ou um problema na configuração de tempo e data em ambas as máquinas. O servidor backend recusou o bilhete Kerberos criado pela Azure AD. Verifique se o AZure AD e o servidor de aplicação backend estão configurados corretamente. Certifique-se de que a configuração da hora e da data no AD Azure e no servidor de aplicações backend estão sincronizados. |
| 13016 - A Azure AD não pode recuperar um bilhete Kerberos em nome do utilizador porque não existe UPN no token de borda ou no cookie de acesso. | Há um problema com a configuração STS. Fixe a configuração de reclamação UPN na STS. |
| 13019 - A Azure AD não pode recuperar um bilhete Kerberos em nome do utilizador devido ao seguinte erro geral da API. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor do controlador de domínio, ou um problema na configuração de tempo e data em ambas as máquinas. O controlador de domínio recusou o bilhete Kerberos criado pela Azure AD. Verifique se o AZure AD e o servidor de aplicação backend estão configurados corretamente, especialmente a configuração SPN. Certifique-se de que o AD AD é um domínio ligado ao mesmo domínio que o controlador de domínio para garantir que o controlador de domínio estabelece confiança com a Azure AD. Certifique-se de que a configuração da hora e da data no AD AZure e no controlador de domínio estão sincronizadas. |
| 13020 - A Azure AD não pode recuperar um bilhete Kerberos em nome do utilizador porque o servidor de backend SPN não está definido. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor do controlador de domínio, ou um problema na configuração de tempo e data em ambas as máquinas. O controlador de domínio recusou o bilhete Kerberos criado pela Azure AD. Verifique se o AZure AD e o servidor de aplicação backend estão configurados corretamente, especialmente a configuração SPN. Certifique-se de que o AD AD é um domínio ligado ao mesmo domínio que o controlador de domínio para garantir que o controlador de domínio estabelece confiança com a Azure AD. Certifique-se de que a configuração da hora e da data no AD AZure e no controlador de domínio estão sincronizadas. |
| 13022 - A AD Azure não pode autenticar o utilizador porque o servidor backend responde às tentativas de autenticação de Kerberos com um erro HTTP 401. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor de aplicação backend, ou um problema na configuração de tempo e data em ambas as máquinas. O servidor backend recusou o bilhete Kerberos criado pela Azure AD. Verifique se o AZure AD e o servidor de aplicação backend estão configurados corretamente. Certifique-se de que a configuração da hora e da data no AD Azure e no servidor de aplicações backend estão sincronizados. Para obter mais informações, consulte [configurações de delegação restritas de Kerberos para aplicação proxy](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Erros do utilizador final

Esta lista cobre erros que os utilizadores finais podem encontrar quando tentam aceder à app e falhar. 

| Erro | Passos recomendados |
| ----- | ----------------- |
| O site não pode exibir a página. | O seu utilizador pode ter este erro ao tentar aceder à aplicação que publicou se a aplicação for uma aplicação IWA. A SPN definida para esta aplicação pode estar incorreta. Para aplicações IWA, certifique-se de que o SPN configurado para esta aplicação está correta. |
| O site não pode exibir a página. | O seu utilizador pode ter este erro ao tentar aceder à aplicação que publicou se a aplicação for uma aplicação OWA. Isto pode ser causado por uma das seguintes:<br><li>A SPN definida para esta aplicação está incorreta. Certifique-se de que o SPN configurado para esta aplicação está correto.</li><li>O utilizador que tentou aceder à aplicação está a utilizar uma conta Microsoft em vez da conta corporativa adequada para iniciar scontabilidade, ou o utilizador é um utilizador convidado. Certifique-se de que o utilizador assina na utilização da sua conta corporativa que corresponda ao domínio da aplicação publicada. Os utilizadores da Microsoft Account e o hóspede não podem aceder às aplicações IWA.</li><li>O utilizador que tentou aceder à aplicação não está devidamente definido para esta aplicação do lado do local. Certifique-se de que este utilizador dispõe das permissões adequadas, tal como definidas para esta aplicação de backend na máquina de acesso. |
| Esta aplicação corporativa não pode ser acedida. Não está autorizado a aceder a esta aplicação. A autorização falhou. Certifique-se de atribuir ao utilizador acesso a esta aplicação. | O utilizador pode ter este erro ao tentar aceder à aplicação que publicou se utilizar as contas da Microsoft em vez da sua conta corporativa para iniciar scontabilidade. Os utilizadores convidados também podem obter este erro. Os utilizadores e hóspedes da Microsoft Account não podem aceder às aplicações IWA. Certifique-se de que o utilizador assina na utilização da sua conta corporativa que corresponda ao domínio da aplicação publicada.<br><br>Pode não ter atribuído o utilizador para esta aplicação. Aceda ao separador **Aplicação,** e em **Utilizadores e Grupos,** atribua este utilizador ou grupo de utilizadores a esta aplicação. |
| Esta aplicação corporativa não pode ser acedida agora. Por favor, tente de novo mais tarde... O conector está esgotado. | O utilizador poderá ter este erro ao tentar aceder à aplicação que publicou se não estiver devidamente definida para esta aplicação no lado do local. Certifique-se de que os seus utilizadores têm as permissões adequadas, conforme definido para esta aplicação backend na máquina de acesso. |
| Esta aplicação corporativa não pode ser acedida. Não está autorizado a aceder a esta aplicação. A autorização falhou. Certifique-se de que o utilizador tem uma licença para o Azure Ative Directory Premium. | O seu utilizador poderá obter este erro ao tentar aceder à aplicação que publicou se não tiverem sido explicitamente atribuídas a uma licença Premium pelo administrador do assinante. Aceda ao separador Ative Directory **Licenses** do assinante e certifique-se de que este utilizador ou grupo de utilizadores é atribuído a uma licença Premium. |
| Não foi possível encontrar um servidor com o nome de anfitrião especificado. | O seu utilizador poderá obter este erro ao tentar aceder à aplicação que publicou se o domínio personalizado da aplicação não estiver configurado corretamente. Certifique-se de que carregou um certificado para o domínio e configura o registo de DNS corretamente seguindo os passos em [Trabalhar com domínios personalizados no Azure AD Application Proxy](application-proxy-configure-custom-domain.md) |
|Proibido: Esta aplicação corporativa não pode ser acedida ou o utilizador não pôde ser autorizado. Certifique-se de que o utilizador está definido no seu AD no local e que o utilizador tem acesso à aplicação no seu AD no local. | Isto pode ser um problema com o acesso à informação de autorização, ver [Algumas aplicações e APIs requerem acesso à informação de autorização em objetos de conta]( https://support.microsoft.com/help/331951/some-applications-and-apis-require-access-to-authorization-information). Em resumo, adicione a conta da máquina de conector proxy da aplicação ao grupo de domínio "Windows Authorization Access Group" para resolver. |

## <a name="my-error-wasnt-listed-here"></a>O meu erro não foi listado aqui.

Se encontrar um erro ou problema com o Azure AD Application Proxy que não esteja listado neste guia de resolução de problemas, gostaríamos de saber. Envie um e-mail para a nossa equipa de [comentários](mailto:aadapfeedback@microsoft.com) com os detalhes do erro que encontrou.

## <a name="see-also"></a>Ver também
* [Ativar o Proxy de Aplicações para O Diretório Ativo Azure](application-proxy-add-on-premises-application.md)
* [Publicar aplicações com o Proxy da Aplicação](application-proxy-add-on-premises-application.md)
* [Ativar um único sinal](application-proxy-configure-single-sign-on-with-kcd.md)
* [Ativar o acesso condicional](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
