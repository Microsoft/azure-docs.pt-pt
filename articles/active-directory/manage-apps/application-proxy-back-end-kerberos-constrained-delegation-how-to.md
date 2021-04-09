---
title: Resolução de problemas Kerberos restrita delegação - App Proxy
description: Resolução de problemas Configurações da delegação restrita kerberos para procuração de aplicação
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/23/2019
ms.author: kenwith
ms.reviewer: asteen, japere
ms.openlocfilehash: b8562f3bdd82b5b0c2c1340f511f87ad90dfbe3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102487955"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Resolver problemas de configurações de delegação restrita do Kerberos para o Proxy de Aplicações

Os métodos disponíveis para a obtenção de SSO para aplicações publicadas podem variar de uma aplicação para outra. Uma opção que o Azure Ative Directory (Azure AD) Application Proxy oferece por padrão é a delegação restrita kerberos (KCD). Pode configurar um conector, para os seus utilizadores, para executar a autenticação Kerberos restrita para aplicações de back-end.

O procedimento de habilitação do KCD é simples. Não requer mais do que uma compreensão geral dos vários componentes e fluxo de autenticação que suportam sSO. Mas, às vezes, o KCD SSO não funciona como esperado. Precisa de boas fontes de informação para resolver estes cenários.

Este artigo fornece um único ponto de referência que ajuda a resolver problemas e a auto-remediar algumas das questões mais comuns. Abrange igualmente o diagnóstico de problemas de implementação mais complexos.

Este artigo faz os seguintes pressupostos:

- A implementação do Azure AD Application Proxy per [Get iniciou-se com o Application Proxy](application-proxy-add-on-premises-application.md) e o acesso geral a aplicações não-KCD funcionam como esperado.
- A aplicação-alvo publicada baseia-se nos Serviços de Informação da Internet (IIS) e na implementação da Microsoft de Kerberos.
- Os anfitriões do servidor e da aplicação residem num único domínio do Azure Ative Directory. Para obter informações detalhadas sobre cenários de domínio transversal e floresta, consulte o [livro branco do KCD](https://aka.ms/KCDPaper).
- O pedido de objeto é publicado num inquilino da Azure com pré-autenticação habilitada. Espera-se que os utilizadores autentem a autenticação para a Azure através da autenticação baseada em formulários. Os cenários de autenticação de clientes ricos não são abrangidos por este artigo. Podem ser adicionados em algum momento no futuro.

## <a name="prerequisites"></a>Pré-requisitos

O Azure AD Application Proxy pode ser implantado em vários tipos de infraestruturas ou ambientes. As arquiteturas variam de organização para organização. As causas mais comuns das questões relacionadas com o KCD não são os ambientes. Simples erros de configuração ou erros gerais causam a maioria dos problemas.

Por esta razão, o melhor é certificar-se de que encontrou todos os pré-requisitos na [utilização do KCD SSO com o Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md) antes de começar a resolver problemas. Note a secção sobre a configuração da delegação de Kerberos constrangida em 2012R2. Este processo emprega uma abordagem diferente para configurar o KCD em versões anteriores do Windows. Além disso, esteja atento a estas considerações:

- Não é incomum um servidor de membro de domínio abrir um diálogo de canal seguro com um controlador de domínio específico (DC). Então o servidor pode mover-se para outro diálogo a qualquer momento. Assim, os anfitriões do conector não se restringem à comunicação com apenas dCs locais específicos.
- Os cenários de domínio cruzado dependem de referências que direcionam um anfitrião de conector para DCs que podem estar fora do perímetro da rede local. Nestes casos, é igualmente importante enviar também tráfego para DCs que representam outros domínios respetivos. Se não, a delegação falha.
- Sempre que possível, evite colocar quaisquer dispositivos IPS ou IDS ativos entre os anfitriões do conector e os DCs. Estes dispositivos são por vezes demasiado intrusivos e interferem com o tráfego RPC central.

Delegação de testes em cenários simples. Quanto mais variáveis introduzires, mais terás de enfrentar. Para poupar tempo, limite o seu teste a um único conector. Adicione conectores adicionais depois de resolvido o problema.

Alguns fatores ambientais podem também contribuir para um problema. Para evitar estes fatores, minimize a arquitetura o máximo possível durante os testes. Por exemplo, os ACLs de firewall internos mal configurados são comuns. Se possível, envie todo o tráfego de um conector diretamente para os DCs e aplicação de back-end.

O melhor local para posicionar os conectores é o mais próximo possível dos seus alvos. Uma firewall que fica em linha quando os testes adiciona complexidade desnecessária e pode prolongar as suas investigações.

O que mostra um problema com o KCD? Há várias indicações comuns de que o KCD SSO está a falhar. Os primeiros sinais de um problema aparecem no navegador.

![Screenshot que mostra um exemplo de um erro de configuração K C D incorreto, com o erro "Incorreta Kerberos contou a delegação..." destacado.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Exemplo: Autorização falhou por falta de permissões](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Ambas as imagens mostram o mesmo sintoma: falha SSO. O acesso do utilizador à aplicação é negado.

## <a name="troubleshooting"></a>Resolução de problemas

A forma como se lida com problemas depende do problema e dos sintomas que observa. Antes de ir mais longe, explore os seguintes artigos. Fornecem informações úteis sobre a resolução de problemas:

- [Resolver problemas do Proxy de Aplicações e as mensagens de erro](application-proxy-troubleshoot.md)
- [Erros e sintomas de Kerberos](application-proxy-troubleshoot.md#kerberos-errors)
- [Trabalhar com sSO quando no local e identidades em nuvem não são idênticos](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Se chegaste a este ponto, então o teu principal problema existe. Para começar, separe o fluxo para as três fases seguintes que pode resolver problemas.

### <a name="client-pre-authentication"></a>Pré-autenticação do cliente

O utilizador externo autenticando a Azure através de um browser. A capacidade de pré-autenticação para Azure é necessária para que o KCD SSO funcione. Teste e aborde esta capacidade se houver algum problema. A fase de pré-autenticação não está relacionada com o KCD ou com a aplicação publicada. É fácil corrigir quaisquer discrepâncias verificando se a conta do assunto existe em Azure. Verifique também se não está desativado ou bloqueado. A resposta de erro no navegador é descritiva o suficiente para explicar a causa. Se tiver dúvidas, verifique outros artigos de resolução de problemas da Microsoft para verificar.

### <a name="delegation-service"></a>Serviço de delegação

O conector Azure Proxy que recebe um bilhete de serviço Kerberos para utilizadores de um Centro de Distribuição de Chaves Kerberos (KCD).

As comunicações externas entre o cliente e a frente Azure não têm qualquer influência no KCD. Estas comunicações só se certificam de que o KCD funciona. O serviço Azure Proxy é fornecido um ID de utilizador válido que é usado para obter um bilhete Kerberos. Sem esta identificação, o KCD não é possível e falha.

Como mencionado anteriormente, as mensagens de erro do navegador fornecem algumas boas pistas sobre o porquê das coisas falharem. Certifique-se de que regista o ID da atividade e o calendário na resposta. Esta informação ajuda-o a correlacionar o comportamento com eventos reais no registo de eventos Azure Proxy.

![Exemplo: Erro de configuração do KCD incorreto](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

As entradas correspondentes vistas no registo de eventos mostram como eventos 13019 ou 12027. Encontre os registos de eventos de conector em **Aplicações e Serviços** &gt; **Regista o Microsoft** &gt; **AadApplicationProxy** &gt; **Connector** &gt; **Admin**.

![Evento 13019 do registo de eventos application Proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Evento 12027 do registo de eventos application Proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Utilize um registo **A** no seu DNS interno para o endereço da aplicação, não um **CName**.
1. Reconfirm que o anfitrião do conector tem o direito de delegar na SPN da conta-alvo designada. É selecionado o reconhecimento de que é selecionado **qualquer protocolo de autenticação.** Para obter mais informações, consulte o [artigo de configuração SSO](application-proxy-configure-single-sign-on-with-kcd.md).
1. Verifique se existe apenas uma instância da SPN em Azure AD. Emitir `setspn -x` a partir de um pedido de comando em qualquer anfitrião de membro de domínio.
1. Verifique se é aplicada uma política de domínio que limita o [tamanho máximo das fichas Kerberos emitidas.](/archive/blogs/askds/maxtokensize-and-windows-8-and-windows-server-2012) Esta apólice impede o conector de obter um símbolo se for considerado excessivo.

Um traço de rede que captura as trocas entre o anfitrião do conector e um KDC de domínio é o próximo melhor passo para obter mais detalhes de baixo nível sobre os problemas. Para obter mais informações, consulte o [papel de resolução de problemas de mergulho profundo](https://aka.ms/proxytshootpaper).

Se a bilhética parecer boa, você vê um evento nos registos indicando que a autenticação falhou porque a aplicação devolveu um 401. Este evento indica que o pedido de destino rejeitou o seu bilhete. Vai para a próxima fase.

### <a name="target-application"></a>Aplicação-alvo

O consumidor do bilhete Kerberos fornecido pelo conector. Nesta fase, espere que o conector tenha enviado um bilhete de serviço Kerberos para a parte de trás. Este bilhete é um cabeçalho no primeiro pedido de inscrição.

1. Ao utilizar o URL interno da aplicação definido no portal, valide que a aplicação está acessível diretamente a partir do navegador no anfitrião do conector. Então pode assinar com sucesso. Os detalhes podem ser encontrados na página **de resolução de problemas** do conector.
1. Ainda no anfitrião do conector, confirme que a autenticação entre o navegador e a aplicação utiliza Kerberos. Efetue uma das seguintes ações:
1. Executar DevTools **(F12)** no Internet Explorer ou utilizar [o Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) do anfitrião do conector. Aceda à aplicação utilizando o URL interno. Inspecione os cabeçalhos de autorização www oferecidos devolvidos na resposta do pedido para garantir a presença de ou Kerberos.

   - A próxima bolha Kerberos que é devolvida na resposta do navegador à aplicação começa com **YII.** Estas cartas dizem-te que o Kerberos está a fugir. O Microsoft NT LAN Manager (NTLM), por outro lado, começa sempre com **a TlRMTVNTUAAB,** que lê o NTLM Security Support Provider (NTLMSSP) quando descodificado a partir da Base64. Se vir **a TLRMTVNTUAAB** no início da bolha, a Kerberos não está disponível. Se não vir **a TlRMTVNTUAAB,** a Kerberos está provavelmente disponível.

      > [!NOTE]
      > Se utilizar o Fiddler, este método requer que desative temporariamente a proteção alargada na configuração da aplicação no IIS.

      ![Janela de inspeção da rede de navegador](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - A bolha neste número não começa com **TIRMTVNTUAAB**. Assim, neste exemplo, Kerberos está disponível, e a bolha Kerberos não começa com **YII**.

1. Remova temporariamente a NTLM da lista de fornecedores no site do IIS. Aceda diretamente à aplicação do Internet Explorer no anfitrião do conector. A NTLM já não está na lista de fornecedores. Pode aceder à aplicação usando apenas Kerberos. Se o acesso falhar, pode haver um problema com a configuração da aplicação. A autenticação de Kerberos não está a funcionar.

   - Se kerberos não estiver disponível, verifique as definições de autenticação da aplicação no IIS. Certifique-se de que **o Negotiate** está listado no topo, com a NTLM logo abaixo. Se vir **Não Negociar**, **Kerberos ou Negociar**, ou **PKU2U,** continue apenas se Kerberos estiver funcional.

     ![Fornecedores de autenticação windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Com Kerberos e NTLM no lugar, desative temporariamente a pré-autenticação para a aplicação no portal. Tente aceder-lhe a partir da internet utilizando o URL externo. É solicitado a autenticar. Pode fazê-lo com a mesma conta usada no passo anterior. Se não, há um problema com a aplicação de back-end, não com o KCD.
   - Reativar a pré-autenticação no portal. Autenticar através do Azure tentando ligar-se à aplicação através do seu URL externo. Se o SSO falhar, vê uma mensagem de erro proibida no navegador e no evento 13022 no registo:

     *O Conector Proxy da aplicação do Microsoft AAD não pode autenticar o utilizador porque o servidor backend responde às tentativas de autenticação de Kerberos com um erro HTTP 401.*

      ![Mostra erro proibido HTTTP 401](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Verifique a aplicação IIS. Certifique-se de que o conjunto de aplicações configurado e o SPN estão configurados para utilizar a mesma conta em Azure AD. Navegue no IIS como mostrado na seguinte ilustração:

      ![Janela de configuração de aplicação IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Depois de conhecer a identidade, certifique-se de que esta conta está configurada com a SPN em questão. Um exemplo é `setspn –q http/spn.wacketywack.com`. Introduza o seguinte texto num pedido de comando:

      ![Mostra a janela de comando SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Verifique o SPN definido contra as definições da aplicação no portal. Certifique-se de que a mesma SPN configurada contra a conta Azure AD alvo é utilizada pelo conjunto de aplicações da aplicação.

      ![Configuração SPN no portal Azure](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Entre no IIS e selecione a opção **Editor de Configuração** para a aplicação. Navegue para **system.webServer/security/authentication/windowsAuthentication**. Certifique-se de que o valor **UseAppPoolCredentials** é **verdadeiro**.

      ![Opção credencial de conjuntos de aplicativos de configuração IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Altere este valor para **Verdadeiro**. Remova todos os bilhetes Kerberos em cache do servidor back-end executando o seguinte comando:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Para mais informações, consulte [a cache de bilhete cliente Kerberos para todas as sessões.](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf)

Se deixar o modo Kernel ativado, melhora o desempenho das operações da Kerberos. Mas também faz com que o bilhete para o serviço solicitado seja desencriptado através da conta da máquina. Esta conta também é chamada de sistema Local. Desaje este valor ao **True** para quebrar o KCD quando a aplicação é hospedada em mais de um servidor numa fazenda.

- Como verificação adicional, desative a proteção **alargada** também. Em alguns cenários, a proteção **alargada** quebrou o KCD quando foi ativada em configurações específicas. Nesses casos, foi publicada uma aplicação como sub-dobra do website padrão. Esta aplicação está configurada apenas para autenticação anónima. Todos os diálogos estão acinzentados, o que sugere que objetos infantis não herdam nenhuma configuração ativa. Recomendamos que teste, mas não se esqueça de restaurar este valor para **ativar,** sempre que possível.

  Este cheque adicional coloca-o no caminho certo para utilizar a sua aplicação publicada. Pode girar conectores adicionais que também estão configurados para delegar. Para mais informações, leia o mais aprofundado passeio técnico, resolução de [problemas da Aplicação AD AD Azure.](https://aka.ms/proxytshootpaper)

Se ainda não conseguir fazer progressos, o suporte da Microsoft pode ajudá-lo. Crie um bilhete de apoio diretamente dentro do portal. Um engenheiro irá contactá-lo.

## <a name="other-scenarios"></a>Outros cenários

- A Azure Application Proxy solicita um bilhete Kerberos antes de enviar o seu pedido para um pedido. Algumas aplicações de terceiros não gostam deste método de autenticação. Estas aplicações esperam que as negociações mais convencionais se realizem. O primeiro pedido é anónimo, o que permite que a aplicação responda com os tipos de autenticação que suporta através de um 401. Este tipo de negociação kerberos pode ser habilitado utilizando as etapas descritas neste documento: [Kerberos Delegação Restrita para uma única assinatura](application-proxy-configure-single-sign-on-with-kcd.md).
- A autenticação multi-hop é comumente usada em cenários onde uma aplicação é hierárquica, com uma extremidade traseira e extremidade frontal, onde ambos requerem autenticação, como os SqL Server Reporting Services. Para mais detalhes, consulte [Como configurar a delegação restrita de Kerberos para as páginas de procuração de inscrição na Web](/troubleshoot/windows-server/identity/configure-kerberos-constrained-delegation).

## <a name="next-steps"></a>Passos seguintes

[Configure o KCD num domínio gerido](../../active-directory-domain-services/deploy-kcd.md).
