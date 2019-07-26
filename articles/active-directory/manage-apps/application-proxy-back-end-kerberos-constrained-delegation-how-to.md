---
title: Solucionar problemas de configurações de delegação restrita de Kerberos para o proxy de aplicativo | Microsoft Docs
description: Resolver problemas de configurações de delegação restrita de Kerberos para o Proxy de aplicações
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
ms.openlocfilehash: 3ca50cfb8697fdbb8c71054c5a6b4d5e23792eb5
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381517"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Resolver problemas de configurações de delegação restringida de Kerberos para o Proxy de aplicações

Os métodos disponíveis para alcançar o SSO a aplicações publicadas podem variar de uma aplicação para outra. Uma opção que o Proxy de aplicações do Azure Active Directory (Azure AD) oferece por predefinição é a delegação restringida de Kerberos (KCD). Pode configurar um conector, para os seus utilizadores, para executar restrita de Kerberos de autenticação para aplicações de back-end.

O procedimento para ativar o KCD é simples. Ela exige não mais do que uma compreensão geral dos vários componentes e fluxo de autenticação que suportam o SSO. Mas, às vezes, o KCD SSO não funcionar conforme esperado. Precisa boas fontes de informações para resolver estes cenários.

Este artigo fornece um único ponto de referência que ajuda a resolver problemas e Self-retificar alguns dos problemas mais comuns. Também aborda de diagnóstico de problemas de implementação mais complexos.

Este artigo faz as seguintes suposições:

- Implementação do Proxy de aplicações do Azure AD por [introdução ao Proxy de aplicações](application-proxy-add-on-premises-application.md) e acesso geral para aplicativos não KCD funcionar conforme esperado.
- O aplicativo de destino publicados baseia-se nos serviços de informação Internet (IIS) e a implementação Microsoft do Kerberos.
- Os anfitriões de servidor e aplicação residirem num único domínio do Azure Active Directory. Para obter informações detalhadas sobre cenários entre domínios e florestas, consulte a [KCD white paper](https://aka.ms/KCDPaper).
- A aplicação de assunto é publicada no Azure inquilino com a pré-autenticação ativada. Os utilizadores devem autenticar para o Azure através da autenticação baseada em formulários. Cenários de autenticação de cliente rico não são abordados neste artigo. Podem ser adicionados em algum momento no futuro.

## <a name="prerequisites"></a>Pré-requisitos

Proxy de aplicações do Azure AD podem ser implementado em vários tipos de infraestruturas ou ambientes. As arquiteturas variam de organização para organização. As causas mais comuns dos problemas relacionados com o KCD não são os ambientes. Erros de configuração simples ou erros gerais com que a maioria dos problemas.

Por esse motivo, é melhor para se certificar de que cumpriu todos os pré-requisitos no [usando o KCD SSO com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md) antes de iniciar a resolução de problemas. Tenha em atenção a secção sobre como configurar a delegação restringida de Kerberos no 2012R2. Este processo utiliza uma abordagem diferente para configurar o KCD em versões anteriores do Windows. Além disso, ficar atentos a essas considerações:

- Não é incomum para um servidor de membro de domínio abrir uma caixa de diálogo de canal seguro com um controlador de domínio específico (DC). Em seguida, o servidor pode ser movida para outra caixa de diálogo em qualquer momento. Então, anfitriões de conector não são restritos a comunicação com o site local específico apenas controladores de domínio.
- Cenários entre domínios contam com referências que direcionam um anfitrião do conector para controladores de domínio que podem estar fora do perímetro de rede local. Nestes casos, é igualmente importante para também enviar tráfego ou superior para DCs que representam outros respectivos domínios. Caso contrário, ocorre uma falha de delegação.
- Sempre que possível, evite colocar quaisquer dispositivos ativos de IPS ou IDS entre anfitriões de conector e controladores de domínio. Esses dispositivos são, às vezes, muito invasivos e interferem no tráfego principal de RPC.

Delegação de teste em cenários simples. As variáveis mais introduz, quanto mais talvez tenha que ser seguidas. Para economizar tempo, limite o teste com um único conector. Adicione conectores adicionais depois do problema foi resolvido.

Alguns fatores ambientais também podem contribuir para um problema. Para evitar esses fatores, minimize tanto quanto possível durante o teste de arquitetura. Por exemplo, o firewall interno mal configurado ACLs são comuns. Se possível, envie todo o tráfego de um conector direta para os controladores de domínio e a aplicação de back-end.

O melhor local para posicionar conectores é o mais próximo possível para seus destinos. Uma firewall que lhes inline durante o teste adiciona complexidade desnecessária e pode prolongar a suas investigações.

O que mostra um problema KCD? Existem vários indicações comuns KCD SSO com falhas. Os primeira sinais de um problema são apresentados no browser.

![Exemplo: Erro de configuração de KCD incorreto](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Exemplo: Autorização falhou devido a permissões em falta](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Essas duas imagens mostram o mesmo sintoma: Falha de SSO. É negado o acesso de utilizador à aplicação.

## <a name="troubleshooting"></a>Resolução de problemas

Como resolver depende do problema e os sintomas que observar. Antes de vai qualquer ainda mais longe, explore os artigos seguintes. Eles fornecem informações de resolução de problemas úteis:

- [Resolver problemas do Proxy de aplicações e mensagens de erro](application-proxy-troubleshoot.md)
- [Erros de Kerberos e os sintomas](application-proxy-troubleshoot.md#kerberos-errors)
- [Trabalhar com o SSO no local e na cloud identidades não são idênticas](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Se tem a este ponto, o principal problema existe. Para começar, separe o fluxo para as três fases seguintes que pode resolver.

### <a name="client-pre-authentication"></a>Pré-autenticação de cliente

O utilizador externo a autenticação no Azure através de um browser. A capacidade de autenticar previamente para o Azure é necessária para KCD SSO função. Testar e resolver esta capacidade, se existirem quaisquer problemas. A fase de pré-autenticação não está relacionado com KCD ou a aplicação publicada. É fácil corrigir qualquer discrepância sanidade verificando que a conta de assunto existe no Azure. Também verificar se não está desativada ou bloqueada. A resposta de erro no browser é descritiva o bastante para explicar a causa. Se estiver incerto, consulte outros artigos de resolução de problemas da Microsoft para verificar.

### <a name="delegation-service"></a>Serviço de delegação

O conector de Proxy do Azure que obtém uma permissão de serviço do Kerberos para os utilizadores a partir de um centro de distribuição de chave de Kerberos (KCD).

As comunicações externas entre o cliente e o front-end do Azure não tem nenhuma relevância na KCD. Estas comunicações apenas Certifique-se de que o KCD funciona. O serviço de Proxy do Azure é fornecido um ID de utilizador válido, que é utilizado para obter um tíquete Kerberos. Sem essa ID, o KCD não é possível e falha.

Como mencionado anteriormente, as mensagens de erro do navegador fornece algumas boas dicas sobre por que as coisas falharem. Certifique-se de tomar nota do ID de atividade e timestamp na resposta. Estas informações ajudam-lhe correlacionar o comportamento real eventos no registo de eventos de Proxy do Azure.

![Exemplo: Erro de configuração de KCD incorreto](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Mostram entradas correspondentes no registo de eventos de ver como eventos 13019 ou 12027. Localizar os registos de eventos do conector no **Applications and Services Logs** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt;  **Conector** &gt; **administrador**.

![Evento 13019 de registo de eventos do Proxy de aplicações](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![12027 de eventos de registo de eventos do Proxy de aplicações](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Utilize um **uma** gravar no seu DNS interno para o endereço do aplicativo, não um **CName**.
1. Voltem a que o anfitrião do conector foi concedido o direito de delegar para SPN a conta de destino designado. Voltem que **utilizar qualquer protocolo de autenticação** está selecionada. Para obter mais informações, consulte a [artigo de configuração de SSO](application-proxy-configure-single-sign-on-with-kcd.md).
1. Certifique-se de que existe apenas uma instância do SPN existe no Azure AD. Problema `setspn -x` na linha de comandos em qualquer host de membro de domínio.
1. Verifique o que é aplicada uma política de domínio que limita a [tamanho máximo de tokens do Kerberos emitidos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Esta política será interrompido o conector de obter um token, se ele é considerado excessiva.

Um rastreio de rede que captura as trocas entre o anfitrião de conector e um domínio KDC é a próxima etapa melhor para obter mais detalhes de baixo nível sobre os problemas. Para obter mais informações, consulte a [aprofunde-se o documento de resolução de problemas](https://aka.ms/proxytshootpaper).

Se estiver de emissão de permissões correto, verá um evento nos registos que diz que a autenticação falhou porque o aplicativo devolveu um erro 401. Este evento indica que o aplicativo de destino rejeitou o pedido de suporte. Vá para o próximo estágio.

### <a name="target-application"></a>Aplicação de destino

O consumidor do tíquete Kerberos fornecido pelo conector. Nesta fase, esperar que o conector para enviou um pedido de serviço do Kerberos para o back-end. Este pedido de suporte é um cabeçalho no primeiro pedido de aplicação.

1. Ao utilizar o URL interno do aplicativo definido no portal, valide se a aplicação está acessível diretamente a partir do browser no anfitrião do conector. Em seguida, pode iniciar sessão com êxito. Pode encontrar detalhes sobre o conector **resolução de problemas** página.
1. Ainda no anfitrião do conector, certifique-se que a autenticação entre o browser e o aplicativo usa Kerberos. Efetuar uma das seguintes ações:
1. Executar DevTools (**F12**) no Internet Explorer ou utilizar [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) partir do anfitrião do conector. Vá para a aplicação utilizando o URL interno. Inspecionar os cabeçalhos de autorização de WWW oferecidos devolvidos na resposta da aplicação para se certificar de que qualquer um negociar ou Kerberos está presente.

   - O blob de Kerberos seguinte, que é devolvido na resposta do navegador para o aplicativo começa com **YII**. Estas letras indicam que o Kerberos está em execução. Microsoft NT LAN Manager (NTLM), por outro lado, sempre começa com **TlRMTVNTUAAB**, que lê o fornecedor de suporte de segurança da NTLM (NTLMSSP) ao descodificar a partir de Base64. Se vir **TlRMTVNTUAAB** no início do blob, Kerberos não estiver disponível. Se não vir **TlRMTVNTUAAB**, Kerberos é provável que disponíveis.

      > [!NOTE]
      > Se utilizar o Fiddler, este método requer que desabilitar temporariamente a proteção expandida na configuração da aplicação no IIS.

      ![Janela de inspeção de rede do browser](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - O blob nesta figura não começa com **TIRMTVNTUAAB**. Neste exemplo, Kerberos estiver disponível, e o blob de Kerberos não começa com **YII**.

1. Remova temporariamente NTLM a partir da lista de fornecedores no site do IIS. Aceda à aplicação diretamente a partir do Internet Explorer no anfitrião do conector. NTLM não se encontra na lista de fornecedores. Pode aceder à aplicação através de Kerberos apenas. Se o acesso falhar, poderá haver um problema com a configuração da aplicação. A autenticação Kerberos não está a funcionar.

   - Se o Kerberos não estiver disponível, verifique as definições de autenticação da aplicação no IIS. Certifique-se **negociar** está listada na parte superior, com o NTLM apenas sob ela. Se vir **negociar**, **Kerberos ou negociar**, ou **PKU2U**, continue apenas se o Kerberos é funcional.

     ![Provedores de autenticação do Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Com o Kerberos e NTLM no local, desative temporariamente a pré-autenticação para a aplicação no portal. Tente aceder a partir da internet ao utilizar o URL externo. Lhe for pedido para se autenticar. Pode fazê-lo com a mesma conta utilizada no passo anterior. Caso contrário, existe um problema com a aplicação de back-end, não KCD.
   - Volte a ativar a pré-autenticação no portal. Autenticar-se através do Azure ao tentar ligar-se para a aplicação através do respetivo URL externo. Se falhar de SSO, verá uma mensagem de erro proibido no browser e o evento 13022 no registo de:

     *Conector do Proxy de aplicações do Microsoft AAD não é possível autenticar o utilizador porque o servidor de back-end reage a tentativas de autenticação de Kerberos com um erro de HTTP 401.*

      ![Mostra o erro proibido do HTTTP 401](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Verifique o aplicativo do IIS. Certifique-se de que o conjunto aplicacional configurado e o SPN estão configurados para utilizar a mesma conta no Azure AD. Navegue no IIS, conforme mostrado na ilustração seguinte:

      ![Janela de configuração de aplicativo do IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Depois de saber a identidade, certifique-se de que esta conta está configurada com o SPN em questão. Um exemplo é `setspn –q http/spn.wacketywack.com`. Introduza o seguinte texto num prompt de comando:

      ![Mostra a janela de comando SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Verifique o SPN definido contra as configurações do aplicativo no portal. Certifique-se de que o mesmo SPN configurado em relação a conta do Azure AD de destino é utilizado pelo conjunto de aplicações do aplicativo.

      ![Configuração do SPN no portal do Azure](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Vá para o IIS e selecione o **Editor de configuração** opção para a aplicação. Navegue para **system.webServer/security/authentication/windowsAuthentication**. Certificar-se de que o valor **UseAppPoolCredentials** é **verdadeiro**.

      ![Opção de credenciais de pools de aplicativos de configuração do IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Alterar este valor para **True**. Remova todas as permissões de Kerberos em cache do servidor de back-end, executando o seguinte comando:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Para obter mais informações, consulte [limpar a cache de pedido de suporte do cliente de Kerberos para todas as sessões](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Se deixar o modo de Kernel ativado, melhora o desempenho de operações do Kerberos. Mas ele também faz com que o pedido de suporte para o serviço de pedido ser desencriptada com a conta de computador. Esta conta também é denominada Sistema Local. Definir este valor como **True** para dividir o KCD quando o aplicativo é hospedado em mais de um servidor num farm.

- Como uma verificação adicional, desative **Extended** proteção demasiado. Em alguns cenários, **Extended** proteção interrompida KCD quando ele foi ativado em configurações específicas. Nesses casos, uma aplicação foi publicada como uma subpasta do Web site predefinido. Esta aplicação está configurada para apenas a autenticação anónima. Todas as caixas de diálogo estão desativadas, que sugere que objetos filho não herdam as definições do Active Directory. Recomendamos que teste, mas não se esqueça de restaurar este valor para **ativada**, sempre que possível.

  Esta verificação adicional coloca-o no caminho certo para utilizar o seu aplicativo publicado. Pode acelerar conectores adicionais que também estejam configurados para delegar. Para obter mais informações, leia a apresentação técnica mais aprofundada, [resolução de problemas do Proxy de aplicações do Azure AD](https://aka.ms/proxytshootpaper).

Se ainda não o conseguir progresso, o suporte da Microsoft pode ajudá-lo. Crie um pedido de suporte diretamente no portal. Um engenheiro irá contactá-lo.

## <a name="other-scenarios"></a>Outros cenários

- Proxy de aplicações do Azure solicita um tíquete Kerberos antes de enviar sua solicitação para uma aplicação. Alguns aplicativos de terceiros não gostam desse método de autenticação. Esses aplicativos esperam que a negociações mais convencionais para ter lugar. A primeira solicitação é anônima, o que permite que o aplicativo responder com os tipos de autenticação que ele oferece suporte por meio de um erro 401.
- Multi-HOP autenticação é frequentemente utilizada em cenários em que um aplicativo está em camadas, com um back-end e front-end, em que ambos requerem a autenticação, como o SQL Server Reporting Services. Para configurar o cenário de salto múltiplo, consulte o artigo de suporte a [delegação restrita de Kerberos pode exigir a transição de protocolo em cenários de multi-hop](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Passos Seguintes

[Configurar o KCD num domínio gerido](../../active-directory-domain-services/deploy-kcd.md).
