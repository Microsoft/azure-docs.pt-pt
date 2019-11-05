---
title: Perguntas frequentes sobre a autenticação multifator do Azure-Azure Active Directory
description: Perguntas frequentes e respostas relacionadas à autenticação multifator do Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea174de04d0e75bc996c6f692edd474a9396346a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474266"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Perguntas frequentes sobre a autenticação multifator do Azure

Estas perguntas FREQUENTEs respondem a perguntas comuns sobre a autenticação multifator do Azure e usando o serviço de autenticação multifator. Ele é dividido em perguntas sobre o serviço em geral, os modelos de cobrança, as experiências do usuário e a solução de problemas.

## <a name="general"></a>Geral

**P: como o Azure Servidor de Autenticação Multifator lida com os dados do usuário?**

Com Servidor de Autenticação Multifator, os dados do usuário são armazenados somente nos servidores locais. Nenhuns dados de utilizador persistentes são armazenados na nuvem. Quando o usuário executa a verificação em duas etapas, Servidor de Autenticação Multifator envia dados para o serviço de nuvem da autenticação multifator do Azure para autenticação. A comunicação entre Servidor de Autenticação Multifator e o serviço de nuvem da autenticação multifator usa protocolo SSL (SSL) ou TLS (segurança de camada de transporte) pela porta 443 de saída.

Quando as solicitações de autenticação são enviadas para o serviço de nuvem, os dados são coletados para relatórios de autenticação e uso. Os campos de dados incluídos em logs de verificação em duas etapas são os seguintes:

* **ID exclusiva** (nome de usuário ou ID de servidor de autenticação multifator local)
* **Nome e sobrenome** (opcional)
* **Endereço de email** (opcional)
* **Número de telefone** (ao usar uma chamada de voz ou autenticação SMS)
* **Token de dispositivo** (ao usar a autenticação de aplicativo móvel)
* **Modo de autenticação**
* **Resultado da autenticação**
* **Nome do Servidor de Autenticação Multifator**
* **Servidor de Autenticação Multifator IP**
* **IP do cliente** (se disponível)

Os campos opcionais podem ser configurados no Servidor de Autenticação Multifator.

O resultado da verificação (êxito ou negação) e o motivo se ele foi negado, são armazenados com os dados de autenticação. Esses dados estão disponíveis em relatórios de uso e de autenticação.

**P: quais códigos curtos do SMS são usados para enviar mensagens SMS aos meus usuários?**

No Estados Unidos Microsoft usa os seguintes códigos curtos do SMS:

   * 97671
   * 69829
   * 51789
   * 99399

No Canadá, a Microsoft usa os seguintes códigos curtos do SMS:

   * 759731 
   * 673801

A Microsoft não garante a entrega de prompt de autenticação multifator baseada em voz ou SMS consistente pelo mesmo número. Ao interesse de nossos usuários, a Microsoft pode adicionar ou remover códigos curtos a qualquer momento à medida que fizermos ajustes de rota para melhorar a entrega do SMS. A Microsoft não oferece suporte a códigos curtos para países/regiões além do Estados Unidos e do Canadá.

## <a name="billing"></a>Faturação

A maioria das perguntas de cobrança pode ser respondida referindo-se à [página de preços da autenticação multifator](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ou à documentação sobre [como obter a autenticação multifator do Azure](concept-mfa-licensing.md).

**P: minha organização é cobrada pelo envio de chamadas telefônicas e mensagens de texto que são usadas para autenticação?**

Não, você não é cobrado por chamadas telefônicas individuais colocadas ou mensagens de texto enviadas aos usuários por meio da autenticação multifator do Azure. Se você usar um provedor de MFA por autenticação, será cobrado para cada autenticação, mas não para o método usado.

Os usuários podem ser cobrados por chamadas telefônicas ou mensagens de texto recebidas, de acordo com seu serviço de telefone pessoal.

**P: o modelo de cobrança por usuário cobra para todos os usuários habilitados ou apenas aqueles que executaram a verificação em duas etapas?**

A cobrança baseia-se no número de usuários configurados para usar a autenticação multifator, independentemente de executarem a verificação em duas etapas no mês.

**P: como funciona a cobrança da autenticação multifator?**

Quando você cria um provedor de MFA por usuário ou por autenticação, a assinatura do Azure da sua organização é cobrada mensalmente com base no uso. Esse modelo de cobrança é semelhante a como o Azure cobra pelo uso de máquinas virtuais e sites.

Quando você adquire uma assinatura para a autenticação multifator do Azure, sua organização paga apenas a taxa de licença anual para cada usuário. As licenças MFA e os pacotes do Office 365, Azure AD Premium ou Enterprise Mobility + Security são cobrados dessa maneira. 

Saiba mais sobre suas opções em [como obter a autenticação multifator do Azure](concept-mfa-licensing.md).

**P: existe uma versão gratuita da autenticação multifator do Azure?**

Em alguns casos, sim.

A autenticação multifator para administradores do Azure oferece um subconjunto de recursos do Azure MFA sem custo adicional para acesso ao Microsoft serviços online, incluindo o [portal do Azure](https://portal.azure.com) e o [Microsoft 365 Admin Center](https://admin.microsoft.com). Essa oferta só se aplica a administradores globais em Azure Active Directory instâncias que não têm a versão completa do Azure MFA por meio de uma licença do MFA, um pacote ou um provedor baseado em consumo autônomo. Se seus administradores usarem a versão gratuita e, em seguida, você comprar uma versão completa do Azure MFA, todos os administradores globais serão elevados para a versão paga automaticamente.

A autenticação multifator para usuários do Office 365 oferece um subconjunto de recursos do Azure MFA sem custo adicional para acesso aos serviços do Office 365, incluindo o Exchange Online e o SharePoint Online. Esta oferta se aplica a usuários que têm uma licença do Office 365 atribuída, quando a instância correspondente do Azure Active Directory não tem a versão completa do Azure MFA por meio de uma licença do MFA, um pacote ou um provedor baseado em consumo autônomo.

**P: minha organização pode alternar entre modelos de cobrança de consumo por usuário e por autenticação a qualquer momento?**

Se sua organização comprar MFA como um serviço autônomo com cobrança baseada em consumo, você escolherá um modelo de cobrança ao criar um provedor de MFA. Você não pode alterar o modelo de cobrança depois que um provedor de MFA é criado. 

Se o seu provedor de MFA *não* estiver vinculado a um locatário do Azure ad ou você vincular o novo provedor de MFA a um locatário diferente do Azure AD, as configurações de usuário e as opções de configuração não serão transferidas. Além disso, os Servidores MFA do Azure existentes têm de ser reativados com as credenciais de ativação geradas através do novo Fornecedor do MFA. Reativar os Servidores MFA para associá-los ao novo Fornecedor do MFA não afeta a autenticação por chamada telefónica e mensagem de texto, mas as notificações da aplicação móvel deixarão de funcionar para todos os utilizadores até que reativem a aplicação móvel.

Saiba mais sobre provedores de MFA em [introdução a um provedor de autenticação multifator do Azure](concept-mfa-authprovider.md).

**P: minha organização pode alternar entre cobrança e assinaturas baseadas em consumo (um modelo baseado em licença) a qualquer momento?**

Em alguns casos, sim.

Se o diretório tiver um provedor de autenticação multifator do Azure *por usuário* , você poderá adicionar licenças MFA. Os usuários com licenças não são contados na cobrança baseada em consumo por usuário. Os usuários sem licenças ainda podem ser habilitados para MFA por meio do provedor de MFA. Se você comprar e atribuir licenças para todos os usuários configurados para usar a autenticação multifator, poderá excluir o provedor de autenticação multifator do Azure. Você sempre poderá criar outro provedor de MFA por usuário se tiver mais usuários do que licenças no futuro.

Se o diretório tiver um provedor de autenticação multifator do Azure *por autenticação* , você será sempre cobrado por cada autenticação, desde que o provedor de MFA esteja vinculado à sua assinatura. Você pode atribuir licenças MFA a usuários, mas ainda será cobrado por cada solicitação de verificação em duas etapas, seja ela proveniente de alguém com uma licença MFA atribuída ou não.

**P: minha organização precisa usar e sincronizar identidades para usar a autenticação multifator do Azure?**

Se sua organização usa um modelo de cobrança baseado em consumo, Azure Active Directory é opcional, mas não é necessário. Se seu provedor de MFA não estiver vinculado a um locatário do Azure AD, você só poderá implantar o Azure Servidor de Autenticação Multifator local.

Azure Active Directory é necessário para o modelo de licença porque as licenças são adicionadas ao locatário do Azure AD quando você compra e atribui-as aos usuários no diretório.

## <a name="manage-and-support-user-accounts"></a>Gerenciar e dar suporte a contas de usuário

**P: o que devo dizer aos meus usuários para fazer se não receberem uma resposta em seu telefone?**

Faça com que os usuários tentem até cinco vezes em 5 minutos para obter uma chamada telefônica ou SMS para autenticação. A Microsoft usa vários provedores para entregar chamadas e mensagens SMS. Se isso não funcionar, abra um caso de suporte com a Microsoft para solucionar mais problemas.

Se as etapas acima não funcionarem, espero que todos os usuários tenham configurado mais de um método de verificação. Diga-lhes que selecionem um método de verificação diferente na página de início de sessão e que tentem iniciar sessão novamente.

Você pode apontar seus usuários para o [Guia de solução de problemas do usuário final](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

**P: o que devo fazer se um dos meus usuários não conseguir entrar em sua conta?**

Você pode redefinir a conta do usuário fazendo com que ele passe pelo processo de registro novamente. Saiba mais sobre como [gerenciar configurações de usuário e dispositivo com a autenticação multifator do Azure na nuvem](howto-mfa-userdevicesettings.md).

**P: o que devo fazer se um dos meus usuários perder um telefone que está usando senhas de aplicativo?**

Para impedir o acesso não autorizado, exclua todas as senhas de aplicativo do usuário. Depois que o usuário tiver um dispositivo de substituição, ele poderá recriar as senhas. Saiba mais sobre como [gerenciar configurações de usuário e dispositivo com a autenticação multifator do Azure na nuvem](howto-mfa-userdevicesettings.md).

**P: e se um usuário não conseguir entrar em aplicativos sem navegador?**

Se sua organização ainda usar clientes herdados e você tiver [permitido o uso de senhas de aplicativo](howto-mfa-mfasettings.md#app-passwords), os usuários não poderão entrar nesses clientes herdados com seu nome de usuário e senha. Em vez disso, eles precisam [Configurar senhas de aplicativo](../user-help/multi-factor-authentication-end-user-app-passwords.md). Os usuários devem limpar (excluir) suas informações de entrada, reiniciar o aplicativo e, em seguida, entrar com seu nome de usuário e *senha de aplicativo* em vez de sua senha regular.

Se sua organização não tiver clientes herdados, você não deve permitir que os usuários criem senhas de aplicativo.

> [!NOTE]
> Autenticação moderna para clientes do Office 2013
>
> As senhas de aplicativo só são necessárias para aplicativos que não dão suporte à autenticação moderna. Os clientes do Office 2013 dão suporte a protocolos de autenticação modernos, mas precisam ser configurados. Agora, a autenticação moderna está disponível para qualquer cliente que execute a atualização de março de 2015 ou posterior para o Office 2013. Para obter mais informações, consulte a postagem no blog [atualização da autenticação moderna do Office 365](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

**P: meus usuários dizem que às vezes eles não recebem a mensagem de texto ou respondem a mensagens de texto bidirecionais, mas a verificação atinge o tempo limite.**

A entrega de mensagens de texto e o recebimento de respostas em SMS bidirecional não são garantidos porque há fatores não controláveis que podem afetar a confiabilidade do serviço. Esses fatores incluem o país/região de destino, a operadora de celular e a intensidade do sinal.

Se os usuários costumam ter problemas com o recebimento confiável de mensagens de texto, informe-os sobre o uso do aplicativo móvel ou do método de chamada telefônica. O aplicativo móvel pode receber notificações em conexões celulares e Wi-Fi. Além disso, o aplicativo móvel pode gerar códigos de verificação mesmo quando o dispositivo não tem nenhum sinal. O aplicativo Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [Ios](https://go.microsoft.com/fwlink/?Linkid=825073)e [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

Se você precisar usar mensagens de texto, recomendamos o uso do SMS unidirecional em vez do SMS bidirecional, quando possível. O SMS unidirecional é mais confiável e impede que os usuários incorrerem em encargos de SMS globais de responder a uma mensagem de texto que foi enviada de outro país/região.

**P: posso alterar a quantidade de tempo que meus usuários têm para inserir o código de verificação de uma mensagem de texto antes que o sistema expire?**

Em alguns casos, sim. 

Para um SMS unidirecional com o Azure MFA Server v 7.0 ou superior, você pode definir a configuração de tempo limite definindo uma chave do registro. Depois que o serviço de nuvem do MFA envia a mensagem de texto, o código de verificação (ou a senha de uso único) é retornado para o servidor MFA. Por padrão, o servidor MFA armazena o código na memória por 300 segundos. Se o usuário não inserir o código antes que os 300 segundos tenham passado, sua autenticação será negada. Use estas etapas para alterar a configuração de tempo limite padrão:

1. Vá para HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Crie uma chave de Registro DWORD chamada **pfsvc_pendingSmsTimeoutSeconds** e defina o tempo em segundos que você deseja que o servidor do Azure MFA armazene senhas de uso único.

>[!TIP] 
>Se você tiver vários servidores MFA, apenas aquele que processou a solicitação de autenticação original saberá o código de verificação que foi enviado ao usuário. Quando o usuário insere o código, a solicitação de autenticação para validá-lo deve ser enviada para o mesmo servidor. Se a validação de código for enviada para um servidor diferente, a autenticação será negada. 

Para o SMS bidirecional com o servidor MFA do Azure, você pode definir a configuração de tempo limite no Portal de Gerenciamento de MFA. Se os usuários não responderem ao SMS dentro do período de tempo limite definido, sua autenticação será negada. 

Para o SMS unidirecional com o Azure MFA na nuvem (incluindo o adaptador AD FS ou a extensão do servidor de políticas de rede), você não pode definir a configuração de tempo limite. O Azure AD armazena o código de verificação por 180 segundos. 

**P: posso usar tokens de hardware com o Azure Servidor de Autenticação Multifator?**

Se estiver usando o Azure Servidor de Autenticação Multifator, você poderá importar tokens de TOTP (senha de uso único) com base em tempo de autenticação aberta (OATH), e depois usá-los para a verificação em duas etapas.

Você pode usar tokens ActiveIdentity que são tokens TOTP OATH se você colocar a chave secreta em um arquivo CSV e importar para o Azure Servidor de Autenticação Multifator. Você pode usar tokens OATH com Serviços de Federação do Active Directory (AD FS) (ADFS), autenticação baseada em formulários do IIS (servidor de informações da Internet) e serviço RADIUS (RADIUS), desde que o sistema cliente possa aceitar a entrada do usuário.

Você pode importar tokens de TOTP OATH de terceiros com os seguintes formatos:  

- Contêiner de chave simétrica portátil (PSKC)  
- CSV se o arquivo contiver um número de série, uma chave secreta no formato base 32 e um intervalo de tempo  

**P: posso usar o Azure Servidor de Autenticação Multifator para proteger os serviços de terminal?**

Sim, mas se você estiver usando o Windows Server 2012 R2 ou posterior, só poderá proteger os serviços de terminal usando o gateway de Área de Trabalho Remota (gateway de área de trabalho remota).

As alterações de segurança no Windows Server 2012 R2 mudaram como o Azure Servidor de Autenticação Multifator se conecta ao pacote de segurança da autoridade de segurança local (LSA) no Windows Server 2012 e em versões anteriores. Para versões dos serviços de terminal no Windows Server 2012 ou anteriores, você pode [proteger um aplicativo com a autenticação do Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se você estiver usando o Windows Server 2012 R2, precisará do gateway de área de trabalho remota.

**P: configurei a ID do chamador no servidor MFA, mas meus usuários ainda recebem chamadas de autenticação multifator de um chamador anônimo.**

Quando as chamadas da autenticação multifator são colocadas por meio da rede telefônica pública, às vezes, elas são roteadas por uma operadora que não dá suporte à ID do chamador. Por isso, a ID do chamador não é garantida, embora o sistema de autenticação multifator sempre a envie.

**P: por que meus usuários estão sendo solicitados a registrar suas informações de segurança?**
Há vários motivos pelos quais os usuários podem ser solicitados a registrar suas informações de segurança:

- O usuário foi habilitado para MFA por seu administrador no Azure AD, mas ainda não tem informações de segurança registradas para sua conta.
- O usuário foi habilitado para redefinição de senha de autoatendimento no Azure AD. As informações de segurança ajudarão a redefinir sua senha no futuro, se elas já esquecerem.
- O usuário acessou um aplicativo que tem uma política de acesso condicional para exigir MFA e não foi registrado anteriormente para MFA.
- O usuário está registrando um dispositivo com o Azure AD (incluindo o ingresso no Azure AD) e sua organização requer MFA para registro de dispositivo, mas o usuário não foi registrado anteriormente para MFA.
- O usuário está gerando o Windows Hello para empresas no Windows 10 (que requer MFA) e não foi registrado anteriormente para MFA.
- A organização criou e habilitou uma política de registro de MFA que foi aplicada ao usuário.
- O usuário registrou anteriormente para MFA, mas escolheu um método de verificação que um administrador já desabilitou. O usuário deve, portanto, passar pelo registro de MFA novamente para selecionar um novo método de verificação padrão.

## <a name="errors"></a>Erros

**P: o que os usuários devem fazer se receberem uma mensagem de erro "a solicitação de autenticação não é para uma conta ativada" ao usar notificações de aplicativo móvel?**

Peça que eles sigam este procedimento para remover sua conta do aplicativo móvel e, em seguida, adicioná-lo novamente:

1. Acesse [seu perfil de portal do Azure](https://account.activedirectory.windowsazure.com/profile/) e entre com sua conta institucional.
2. Selecione **verificação de segurança adicional**.
3. Remova a conta existente do aplicativo móvel.
4. Clique em **Configurar**e siga as instruções para reconfigurar o aplicativo móvel.

**P: o que os usuários devem fazer se receberem uma mensagem de erro 0x800434D4L ao entrar em um aplicativo sem navegador?**

O erro 0x800434D4L ocorre quando você tenta entrar em um aplicativo sem navegador, instalado em um computador local, que não funciona com contas que exigem a verificação em duas etapas.

Uma solução alternativa para esse erro é ter contas de usuário separadas para operações não administrativas e relacionadas ao administrador. Posteriormente, você pode vincular caixas de correio entre sua conta de administrador e uma conta de não-administrador para que você possa entrar no Outlook usando sua conta de não administrador. Para obter mais detalhes sobre essa solução, saiba como [dar a um administrador a capacidade de abrir e exibir o conteúdo da caixa de correio de um usuário](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Passos seguintes

Se sua pergunta não for respondida aqui, deixe-a nos comentários na parte inferior da página. Ou, aqui estão algumas opções adicionais para obter ajuda:

* Pesquise na [base de dados de conhecimento suporte da Microsoft](https://www.microsoft.com/en-us/search?form=mssupport&q=phonefactor&rtc=1) para obter soluções para problemas técnicos comuns.
* Pesquise e procure perguntas e respostas técnicas da Comunidade ou faça sua própria pergunta nos [fóruns de Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Se você for um cliente herdado do PhoneFactor e tiver dúvidas ou precisar de ajuda para redefinir uma senha, use o link [redefinição de senha](mailto:phonefactorsupport@microsoft.com) para abrir um caso de suporte.
* Contate um profissional de suporte por meio [do suporte do Azure servidor de autenticação multifator (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Ao entrar em contato conosco, será útil se você puder incluir o máximo possível de informações sobre o seu problema. As informações que você pode fornecer incluem a página em que você viu o erro, o código de erro específico, a ID de sessão específica e a ID do usuário que viu o erro.
