---
title: Problemática supor a delegação restrita de Kerberos - App Proxy
description: Resolução de problemas Kerberos Configurações restritas de delegação para procuração de aplicação
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5e866f61409960447e17ecb50b035eabd53dc38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275686"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Resolução de problemas Kerberos limitou configurações de delegação para procuração de aplicação

Os métodos disponíveis para alcançar o SSO a aplicações publicadas podem variar de uma aplicação para outra. Uma opção que o Azure Ative Directory (Azure AD) Application Proxy oferece por defeito é a delegação limitada kerberos (KCD). Pode configurar um conector, para os seus utilizadores, para executar a autenticação limitada de Kerberos para aplicações de back-end.

O procedimento para ativar a KCD é simples. Não requer mais do que uma compreensão geral dos vários componentes e fluxo de autenticação que suportam o SSO. Mas, às vezes, o KCD SSO não funciona como esperado. Precisa de boas fontes de informação para resolver estes cenários.

Este artigo fornece um único ponto de referência que ajuda a resolver problemas e a auto-remediar algumas das questões mais comuns. Abrange também o diagnóstico de problemas de implementação mais complexos.

Este artigo faz as seguintes suposições:

- A implementação do Proxy de Aplicação AD Azure por [Get começou com o Proxy de Aplicação](application-proxy-add-on-premises-application.md) e o acesso geral a aplicações não KCD funcionam como esperado.
- A aplicação-alvo publicada baseia-se nos Serviços de Informação da Internet (IIS) e na implementação da Microsoft da Kerberos.
- Os anfitriões do servidor e da aplicação residem num único domínio de Diretório Ativo Azure. Para obter informações detalhadas sobre cenários de domínio cruzado e floresta, consulte o [livro branco KCD](https://aka.ms/KCDPaper).
- O pedido de assunto é publicado num inquilino azure com pré-autenticação habilitada. Espera-se que os utilizadores autentiquem ao Azure através da autenticação baseada em formulários. Cenários ricos de autenticação de clientes não estão cobertos por este artigo. Podem ser adicionados em algum momento no futuro.

## <a name="prerequisites"></a>Pré-requisitos

A Procuração de Aplicações AD Azure pode ser implantada em muitos tipos de infraestruturas ou ambientes. As arquiteturas variam de organização para organização. As causas mais comuns de questões relacionadas com a KCD não são os ambientes. Simples configurações erradas ou erros gerais causam a maioria dos problemas.

Por esta razão, o melhor é certificar-se de que cumpriu todos os pré-requisitos na utilização do [KCD SSO com o Proxy](application-proxy-configure-single-sign-on-with-kcd.md) de Aplicação antes de começar a resolver problemas. Note a secção sobre a configuração da delegação limitada kerberos em 2012R2. Este processo emprega uma abordagem diferente para configurar o KCD em versões anteriores do Windows. Além disso, esteja atento a estas considerações:

- Não é incomum um servidor membro do domínio abrir um diálogo de canal seguro com um controlador de domínio específico (DC). Em seguida, o servidor pode passar para outro diálogo a qualquer momento. Assim, os anfitriões de conector não estão restritos à comunicação com apenas dCs locais específicos.
- Os cenários de domínio transversal baseiam-se em referências que direcionam um hospedeiro de conector para DCs que podem estar fora do perímetro da rede local. Nestes casos, é igualmente importante também enviar tráfego para os DCs que representam outros domínios respetivos. Se não, a delegação falha.
- Sempre que possível, evite colocar quaisquer dispositivos IPS ou IDS ativos entre os anfitriões dos conectores e os DCs. Estes dispositivos são, por vezes, demasiado intrusivos e interferem com o tráfego rpc central.

Delegação de teste em cenários simples. Quanto mais variáveis introduzires, mais terás de enfrentar. Para poupar tempo, limite os seus testes a um único conector. Adicione conectores adicionais depois de resolvido o problema.

Alguns fatores ambientais podem também contribuir para uma questão. Para evitar estes fatores, minimize a arquitetura o máximo possível durante os testes. Por exemplo, os ACLs internos de firewall mal configurados são comuns. Se possível, envie todo o tráfego de um conector diretamente para os DCs e aplicação back-end.

O melhor lugar para posicionar conectores é o mais próximo possível dos seus alvos. Uma firewall que fica na linha quando os testes adiciona complexidade desnecessária e pode prolongar as suas investigações.

O que mostra um problema com a KCD? Existem várias indicações comuns de que o KCD SSO está a falhar. Os primeiros sinais de um problema aparecem no navegador.

![Exemplo: Erro de configuração KCD incorreto](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Exemplo: Autorização falhou devido a falta de permissões](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Ambas as imagens mostram o mesmo sintoma: Falha sso. O acesso do utilizador à aplicação é negado.

## <a name="troubleshooting"></a>Resolução de problemas

A forma como se resoluo os problemas depende do problema e dos sintomas que observa. Antes de ir mais longe, explore os seguintes artigos. Fornecem informações úteis de resolução de problemas:

- [Problemas de procuração de aplicação de problemas e mensagens de erro](application-proxy-troubleshoot.md)
- [Erros e sintomas de Kerberos](application-proxy-troubleshoot.md#kerberos-errors)
- [Trabalhar com sSO quando no local e identidades na nuvem não são idênticas](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Se chegou a este ponto, então o seu principal problema existe. Para começar, separe o fluxo nas três fases seguintes que pode resolver.

### <a name="client-pre-authentication"></a>Pré-autenticação do cliente

O utilizador externo autenticando o Azure através de um browser. A capacidade de autenticação prévia ao Azure é necessária para que o SSO da KCD funcione. Teste e aborde esta capacidade se houver algum problema. A fase de pré-autenticação não está relacionada com a KCD ou com a aplicação publicada. É fácil corrigir quaisquer discrepâncias verificando que a conta do assunto existe em Azure. Verifique também se não está desativado ou bloqueado. A resposta de erro no navegador é descritiva o suficiente para explicar a causa. Se estiver incerta, verifique outros artigos de resolução de problemas da Microsoft para verificar.

### <a name="delegation-service"></a>Serviço de delegação

O conector Azure Proxy que recebe um bilhete de serviço Kerberos para os utilizadores de um Centro de Distribuição de Chaves Kerberos (KCD).

As comunicações externas entre o cliente e a frente Azure não têm qualquer influência sobre a KCD. Estas comunicações só certificam-se de que a KCD funciona. O serviço Azure Proxy é fornecido um ID de utilizador válido que é usado para obter um bilhete Kerberos. Sem esta identificação, a KCD não é possível e falha.

Como mencionado anteriormente, as mensagens de erro do navegador fornecem algumas boas pistas sobre o porquê das coisas falharem. Certifique-se de que anota o ID de atividade e a marca de tempo na resposta. Esta informação ajuda-o a correlacionar o comportamento com eventos reais no registo de eventos do Azure Proxy.

![Exemplo: Erro de configuração KCD incorreto](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

As entradas correspondentes vistas no registo do evento mostram como eventos 13019 ou 12027. Encontre os registos do evento do conector em **Registos de Sistemas** &gt; de Registos **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector** &gt; **Administrador**.

![Evento 13019 do registo de eventos da Application Proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Evento 12027 do registo de eventos do Proxy de Aplicação](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Utilize um registo **A** no seu DNS interno para o endereço da aplicação, e não um **CName**.
1. Confirme que o anfitrião do conector foi concedido o direito de delegar no SPN da conta-alvo designado. Reconfirme que Utilize qualquer protocolo de **autenticação** selecionado. Para mais informações, consulte o artigo de [configuração SSO](application-proxy-configure-single-sign-on-with-kcd.md).
1. Verifique se só existe um caso da SPN em Azure AD. Emissão `setspn -x` de um pedido de comando em qualquer anfitrião do membro do domínio.
1. Verifique se é aplicada uma política de domínio que limita o [tamanho máximo dos tokens Kerberos emitidos.](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/) Esta política impede o conector de obter um símbolo se for considerado excessivo.

Um traço de rede que captura as trocas entre o anfitrião do conector e um KDC de domínio é o próximo melhor passo para obter mais detalhes de baixo nível sobre os problemas. Para mais informações, consulte o [papel de mergulho profundo Troubleshoot](https://aka.ms/proxytshootpaper).

Se a bilhética parecer boa, vê-se um evento nos registos a indicar que a autenticação falhou porque a aplicação devolveu um 401. Este evento indica que o pedido de destino rejeitou o seu bilhete. Vai para a próxima fase.

### <a name="target-application"></a>Aplicação-alvo

O consumidor do bilhete Kerberos fornecido pelo conector. Nesta fase, espere que o conector tenha enviado um bilhete de serviço Kerberos para a parte de trás. Este bilhete é um cabeçalho no primeiro pedido de inscrição.

1. Ao utilizar o URL interno da aplicação definido no portal, valide que a aplicação esteja acessível diretamente a partir do navegador no anfitrião do conector. Então pode assinar com sucesso. Os detalhes podem ser encontrados na página do conector **Troubleshoot.**
1. Ainda no anfitrião do conector, confirme que a autenticação entre o navegador e a aplicação utiliza a Kerberos. Efetue uma das seguintes ações:
1. Executar DevTools **(F12)** no Internet Explorer ou utilizar [o Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) do anfitrião do conector. Vá à aplicação utilizando o URL interno. Inspecione os cabeçalhos de autorização de WWW devolvidos na resposta do pedido para se certificar de que ou negoceia ou kerberos está presente.

   - A próxima bolha kerberos que é devolvida na resposta do navegador à aplicação começa com **YII**. Estas cartas dizem-te que o Kerberos está a fugir. Microsoft NT LAN Manager (NTLM), por outro lado, começa sempre com **TlRMTVNTUAAB,** que lê ntLM Security Support Provider (NTLMSSP) quando descodificado a partir do Base64. Se vir **TlRMTVNTUAAB** no início da bolha, kerberos não está disponível. Se não vir **TlRMTVNTUAAB,** a Kerberos provavelmente está disponível.

      > [!NOTE]
      > Se utilizar o Fiddler, este método requer que desative temporariamente uma proteção alargada na configuração da aplicação no IIS.

      ![Janela de inspeção de rede de navegador](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - A bolha neste valor não começa com **tIRMTVNTUAAB**. Portanto, neste exemplo, Kerberos está disponível, e a bolha Kerberos não começa com **yII**.

1. Retire temporariamente a NTLM da lista de fornecedores no site do IIS. Aceda à aplicação diretamente do Internet Explorer no anfitrião do conector. A NTLM já não está na lista de fornecedores. Pode aceder à aplicação usando apenas kerberos. Se o acesso falhar, pode haver um problema com a configuração da aplicação. A autenticação kerberos não está a funcionar.

   - Se a Kerberos não estiver disponível, verifique as definições de autenticação da aplicação no IIS. Certifique-se de que **o Negotiate** está no topo, com a NTLM logo abaixo. Se vir **Não Negociar**, **Kerberos ou Negociar**, ou **PKU2U,** só continue se Kerberos estiver funcional.

     ![Fornecedores de autenticação windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Com kerberos e NTLM no lugar, desative temporariamente a pré-autenticação para a aplicação no portal. Tente acessá-lo a partir da internet utilizando o URL externo. Foi-lhe pedido que autenticasse. Pode fazê-lo com a mesma conta usada no passo anterior. Se não, há um problema com a aplicação de back-end, não com a KCD.
   - Reativar a pré-autenticação no portal. Autenticar através do Azure tentando ligar-se à aplicação através do seu URL externo. Se o SSO falhar, vê uma mensagem de erro proibida no navegador e no evento 13022 no registo:

     *O Conector proxy da aplicação Microsoft AAD não pode autenticar o utilizador porque o servidor backend responde às tentativas de autenticação da Kerberos com um erro HTTP 401.*

      ![Mostra erro proibido hTTTP 401](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Verifique a aplicação IIS. Certifique-se de que o conjunto de aplicações configurado e o SPN estão configurados para utilizar a mesma conta em Azure AD. Navegue no IIS como mostrado na seguinte ilustração:

      ![Janela de configuração de aplicação IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Depois de conhecer a identidade, certifique-se de que esta conta está configurada com o SPN em questão. Um exemplo é `setspn –q http/spn.wacketywack.com`. Introduza o seguinte texto num pedido de comando:

      ![Mostra a janela de comando SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Verifique o SPN definido com base nas definições da aplicação no portal. Certifique-se de que o mesmo SPN configurado contra a conta AD-alvo Azure é utilizado pelo conjunto de aplicações da aplicação.

      ![Configuração SPN no portal Azure](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Entre no IIS e selecione a opção 'Editor de **Configuração'** para a aplicação. Navegar para **system.webServer/security/autenticação/windowsAuthentication**. Certifique-se de que o valor **UseAppPoolCredentials** é **verdadeiro**.

      ![Aplicativo de configuração IIS pools opção credencial](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Mude este valor para **True**. Remova todos os bilhetes Kerberos em cache do servidor back-end executando o seguinte comando:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Para mais informações, consulte Purpur o cache de bilhete cliente [Kerberos para todas as sessões](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Se deixar o modo Kernel ativado, melhora o desempenho das operações da Kerberos. Mas também faz com que o bilhete para o serviço solicitado seja desencriptado utilizando a conta da máquina. Esta conta também é chamada de sistema local. Detete este valor para **True** para quebrar o KCD quando a aplicação estiver hospedada em mais de um servidor numa fazenda.

- Como verificação adicional, desative a proteção **alargada** também. Em alguns cenários, a proteção **estendida** quebrou o KCD quando foi ativada em configurações específicas. Nesses casos, uma aplicação foi publicada como subpasta do website predefinido. Esta aplicação está configurada apenas para autenticação anónima. Todos os diálogos estão acinzentados, o que sugere que os objetos infantis não herdam nenhuma definição ativa. Recomendamos que teste, mas não se esqueça de restaurar este valor para **ativado,** sempre que possível.

  Este cheque adicional coloca-o no caminho certo para usar a sua aplicação publicada. Pode girar conectores adicionais que também estão configurados para delegar. Para mais informações, leia a análise técnica mais aprofundada, [resolução de problemas com o Procurador de Aplicação AD Azure](https://aka.ms/proxytshootpaper).

Se ainda não conseguir fazer progressos, o suporte da Microsoft pode ajudá-lo. Crie um bilhete de apoio diretamente dentro do portal. Um engenheiro vai contactá-lo.

## <a name="other-scenarios"></a>Outros cenários

- A Procuração de Aplicações Azure solicita um bilhete Kerberos antes de enviar o seu pedido para um pedido. Algumas aplicações de terceiros não gostam deste método de autenticação. Estas candidaturas esperam que se realizem negociações mais convencionais. O primeiro pedido é anónimo, o que permite que a aplicação responda com os tipos de autenticação que suporta através de um 401.
- A autenticação multi-hop é comumente usada em cenários em que uma aplicação é nivida, com extremidade traseira e frente, onde ambos requerem autenticação, como serviços de reporte de servidores SQL. Para configurar o cenário multi-hop, consulte o artigo de apoio [Kerberos Constranged Delegação May Require A Transição Protocolar em Cenários Multi-hop](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Passos seguintes

[Configure o KCD num domínio gerido](../../active-directory-domain-services/deploy-kcd.md).
