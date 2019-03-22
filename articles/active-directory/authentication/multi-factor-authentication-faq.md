---
title: Autenticação Multifator do Azure FAQ - o Azure Active Directory
description: Perguntas mais frequentes e respostas relacionadas ao Azure multi-factor Authentication.
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
ms.openlocfilehash: 707e0aecdaf2a579d086e012519182078c1a6303
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313231"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Perguntas mais frequentes sobre o Azure multi-factor Authentication

Encontre respostas para perguntas comuns sobre o multi-factor Authentication do Azure e utilizar o serviço de multi-factor Authentication. Ele é dividido em perguntas sobre o serviço em geral, modelos, experiências do usuário, de faturação e resolução de problemas.

## <a name="general"></a>Geral

**P: Como é que o servidor do Azure multi-factor Authentication com dados de utilizador?**

Com o servidor do multi-factor Authentication, os dados de utilizador são armazenados apenas nos servidores no local. Nenhuns dados de utilizador persistentes são armazenados na nuvem. Quando o utilizador efetua a verificação de dois passos, o servidor multi-factor Authentication envia dados para o serviço de nuvem do Azure multi-factor Authentication para autenticação. A comunicação entre o servidor multi-factor Authentication e o serviço de nuvem do multi-factor Authentication utiliza o Secure Sockets Layer (SSL) ou Transport Layer Security (TLS) através da porta 443 de saída.

Quando os pedidos de autenticação são enviados para o serviço em nuvem, os dados são recolhidos para a autenticação e a utilização de relatórios. Campos de dados incluídos nos logs de verificação de dois passos são os seguintes:

* **ID exclusivo** (de qualquer nome ou no local multi-factor Authentication Server ID de utilizador)
* **Primeiro e último nome** (opcional)
* **Endereço de e-mail** (opcional)
* **Número de telefone** (ao utilizar uma chamada de voz ou autenticação por SMS)
* **Token de dispositivo** (ao utilizar a autenticação da aplicação móvel)
* **Modo de autenticação**
* **Resultado da autenticação**
* **Nome do servidor multi-factor Authentication**
* **Servidor multi-factor Authentication IP**
* **Cliente IP** (se disponível)

Os campos opcionais podem ser configurados no servidor multi-factor Authentication.

O resultado da verificação (êxito ou recusa) e o motivo pelo qual se foi negado, são armazenados com os dados de autenticação. Estes dados estão disponíveis em relatórios de utilização e de autenticação.

**P: Quais códigos de curtos de SMS são utilizados para enviar mensagens SMS para os meus utilizadores?**

No Microsoft dos Estados Unidos utiliza os seguintes códigos de curtos de SMS:

   * 97671
   * 69829
   * 51789
   * 99399

No Canadá Microsoft utiliza os seguintes códigos de curtos de SMS:

   * 759731 
   * 673801

A Microsoft não garante consistente SMS ou voz com base no multi-factor Authentication prompt entrega pelo mesmo número. Com o intuito de nossos usuários, a Microsoft poderá adicionar ou remover curtos códigos em qualquer altura, à medida que faz ajustes de rota para melhorar a capacidade de entrega SMS. A Microsoft não suporta códigos curtos para países além dos Estados Unidos e Canadá

## <a name="billing"></a>Faturação

A maioria das questões de faturas podem ser respondidas ao referir-se a qualquer uma de [página de preços do multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ou a documentação sobre [como obter o Azure multi-factor Authentication](concept-mfa-licensing.md).

**P: É cobrada a minha organização para enviar as chamadas telefónicas e mensagens de texto que são utilizadas para autenticação?**

Não, não é cobrada por telefonemas individuais ou texto as mensagens enviadas aos utilizadores através do multi-factor Authentication. Se utilizar um fornecedor MFA por autenticação, é-lhe cobrada para cada autenticação, mas não para o método utilizado.

Os utilizadores poderão ser cobrados para chamadas telefónicas ou mensagens de texto que recebem, de acordo com o serviço de telefone pessoal.

**P: O modelo de faturação por utilizador cobra-me para utilizadores ativados tudo ou apenas aqueles que efetuar a verificação de dois passos?**

A faturação baseia-se no número de utilizadores configurado para utilizar o multi-factor Authentication, independentemente se foi executada a verificação de dois passos nesse mês.

**P: Como funciona a faturação do multi-factor Authentication?**

Ao criar um fornecedor MFA por utilizador ou por autenticação, a subscrição do Azure da sua organização é faturada mensalmente com base na utilização. Este modelo de faturação é semelhante a como o Azure cobra para utilização de máquinas virtuais e Web sites.

Ao adquirir uma subscrição do Azure multi-factor Authentication, sua organização paga apenas o valor da licença anual para cada utilizador. Licenças MFA e do Office 365, Azure AD Premium ou Enterprise Mobility + os pacotes de segurança são faturados dessa maneira. 

Saiba mais sobre as suas opções no [como obter o Azure multi-factor Authentication](concept-mfa-licensing.md).

**P: Existe uma versão gratuita do multi-factor Authentication do Azure?**

Em alguns casos, Sim.

Multi-factor Authentication para administradores do Azure oferece um subconjunto das funcionalidades de MFA do Azure sem custos para o acesso ao Microsoft online services, incluindo o [portal do Azure](https://portal.azure.com) e [Centro de administração do Microsoft 365](https://admin.microsoft.com). Esta oferta aplica-se apenas para os administradores globais em instâncias do Azure Active Directory que não têm a versão completa do MFA do Azure através de uma licença do MFA, um pacote ou um fornecedor de autónomo baseado no consumo. Se os administradores de utilizam a versão gratuita e, em seguida, adquirir uma versão completa do MFA do Azure, em seguida, todos os administradores globais são elevados para a versão paga automaticamente.

Autenticação Multifator para utilizadores do Office 365 oferece um subconjunto das funcionalidades de MFA do Azure sem custos para o acesso a serviços do Office 365, incluindo o Exchange Online e SharePoint Online. Esta oferta aplica-se aos utilizadores que têm uma licença do Office 365 atribuída, quando a instância correspondente do Azure Active Directory não tem a versão completa do MFA do Azure através de uma licença do MFA, um pacote ou um fornecedor de autónomo baseado no consumo.

**P: A minha organização pode alternar entre por utilizador e por autenticação modelos de faturação consumo em qualquer altura?**

Se a sua organização comprar MFA como um serviço autónomo com a faturação com base no consumo, escolha um modelo de faturação ao criar um fornecedor de MFA. Não é possível alterar o modelo de faturação após a criação de um fornecedor de MFA. No entanto, pode eliminar o fornecedor MFA e, em seguida, criar um com um modelo de faturação diferente.

Quando é criado um fornecedor de MFA, podem ser associado a um Azure Active Directory (também conhecido como "inquilino do Azure AD"). Se o fornecedor de MFA atual estiver associado a um inquilino do Azure AD, pode eliminar o fornecedor MFA com segurança e criar um que esteja associado ao mesmo inquilino do Azure AD. Em alternativa, se comprou licenças suficientes do MFA, Azure AD Premium ou Enterprise Mobility + Security (EMS) para abranger todos os utilizadores ativados para o MFA, pode eliminar totalmente o fornecedor do MFA.

Se for o fornecedor de MFA *não* ligado a um inquilino do Azure AD ou associar o novo fornecedor do MFA com um Azure AD diferentes inquilinos, as definições de utilizador e opções de configuração não são transferidas. Além disso, os Servidores MFA do Azure existentes têm de ser reativados com as credenciais de ativação geradas através do novo Fornecedor do MFA. Reativar os Servidores MFA para associá-los ao novo Fornecedor do MFA não afeta a autenticação por chamada telefónica e mensagem de texto, mas as notificações da aplicação móvel deixarão de funcionar para todos os utilizadores até que reativem a aplicação móvel.

Saiba mais sobre os fornecedores de MFA na [introdução ao fornecedor do Azure multi-factor Auth](concept-mfa-authprovider.md).

**P: Pode a minha organização alternar entre baseado no consumo faturação e subscrições (um modelo baseado em licença) em qualquer altura?**

Em alguns casos, Sim.

Se o seu diretório tem um *por utilizador* fornecedor de multi-factor Authentication do Azure, pode adicionar licenças MFA. Os utilizadores com licenças não são contabilizadas na faturação baseado no consumo por utilizador. Os utilizadores sem licenças ainda podem ser ativados para a MFA através do fornecedor de MFA. Se adquirir e atribuir licenças para todos os seus utilizadores configurados para utilizar o multi-factor Authentication, pode eliminar o fornecedor de multi-factor Authentication. Pode sempre criar outro fornecedor MFA por utilizador, se tiver mais utilizadores a licenças no futuro.

Se o seu diretório tem um *por autenticação* fornecedor de multi-factor Authentication do Azure, é sempre faturado por cada autenticação, desde que o fornecedor MFA está ligado à sua subscrição. Pode atribuir licenças MFA para utilizadores, mas ainda é cobrado por cada pedido de verificação de dois passos, se trata de alguém com uma licença do MFA atribuída ou não.

**P: Minha organização tem de utilizar e sincronizar identidades para utilizar o Azure multi-factor Authentication?**

Se a sua organização utilizar um modelo de faturação baseado no consumo, o Azure Active Directory é opcional, mas não necessária. Se o fornecedor de MFA não estiver associado a um inquilino do Azure AD, só pode implementar Azure multi-factor Authentication Server no local.

O Azure Active Directory é necessário para o modelo de licença porque licenças adicionadas ao inquilino do Azure AD ao adquirir e atribuí-las aos utilizadores no diretório.

## <a name="manage-and-support-user-accounts"></a>Gerir e suportar contas de utilizador

**P: O que devo dizer que os meus utilizadores se eles não recebem uma resposta no seu telefone?**

Ter os seus utilizadores a tentar até 5 vezes em 5 minutos para obter uma chamada telefónica ou SMS para autenticação. A Microsoft usa vários fornecedores para entrega de chamadas e mensagens SMS. Se não funcionar, abra um incidente de suporte com a Microsoft para resolver.

Se os passos acima não funcionam com sorte, todos os seus utilizadores configurado mais do que um método de verificação. Diga-lhes que selecionem um método de verificação diferente na página de início de sessão e que tentem iniciar sessão novamente.

Pode indicar a seus usuários para o [guia de resolução de problemas do utilizador final](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

**P: O que devo fazer se um dos meus utilizadores não é possível obter sua conta?**

Pode repor a conta de utilizador, tornando-os para percorrer o processo de registo novamente. Saiba mais sobre [gerir definições de utilizador e dispositivo com multi-factor Authentication na cloud](howto-mfa-userdevicesettings.md).

**P: O que devo fazer se perder um dos meus utilizadores um telefone que está a utilizar as palavras-passe de aplicação?**

Para evitar acessos não autorizados, elimine palavras-passe de aplicação do utilizador. Depois do utilizador tiver um dispositivo de substituição, eles podem recriar as palavras-passe. Saiba mais sobre [gerir definições de utilizador e dispositivo com multi-factor Authentication na cloud](howto-mfa-userdevicesettings.md).

**P: E se um utilizador não é possível iniciar sessão em aplicações não baseadas no browser?**

Se a organização ainda utilizar clientes legados e [permitida a utilização de palavras-passe de aplicação](howto-mfa-mfasettings.md#app-passwords), em seguida, os utilizadores não podem iniciar sessão para estes clientes legados com o respetivo nome de utilizador e palavra-passe. Em vez disso, eles precisam [configurar palavras-passe de aplicação](../user-help/multi-factor-authentication-end-user-app-passwords.md). Os utilizadores tem de limpar (eliminar) as informações de início de sessão, reiniciar a aplicação e, em seguida, inicie sessão com o respetivo nome de utilizador e *palavra-passe de aplicação* em vez da palavra-passe regular.

Se sua organização não tiver clientes legados, não deve permitir que os utilizadores criem palavras-passe de aplicação.

> [!NOTE]
> Autenticação moderna para clientes do Office 2013
>
> As palavras-passe de aplicação são necessárias apenas para aplicações que não suportam a autenticação moderna. Os clientes do Office 2013 suportam protocolos de autenticação moderna, mas têm de ser configurados. Clientes mais recentes do Office suportam automaticamente os protocolos de autenticação moderna. Para obter mais informações, consulte a [anúncio de pré-visualização pública de autenticação moderna do Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**P: Os meus utilizadores dizem que às vezes, não receber a mensagem de texto, ou eles respondem às mensagens de texto bidirecional, mas a verificação de exceder o tempo limite.**

Entrega de mensagens de texto e de receção de respostas no SMS bidirecional não são garantidos porque há fatores incontroláveis para que possam afetar a confiabilidade do serviço. Estes fatores incluem o país de destino, a operadora de celular e a intensidade do sinal.

Se os utilizadores têm problemas frequentes com a receção fiável de mensagens de texto, peça-lhes para usar o método de chamada telefónica ou aplicação móvel em vez disso. A aplicação móvel pode receber notificações, tanto pela rede móvel e ligações Wi-Fi. Além disso, a aplicação móvel pode gerar códigos de verificação, mesmo quando o dispositivo não tem nenhum sinal de todo. A aplicação Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [IOS](https://go.microsoft.com/fwlink/?Linkid=825073), e [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

Se tiver de utilizar mensagens de texto, recomendamos que utilize SMS unidirecional, em vez de SMS bidirecional, sempre que possível. SMS unidirecional é mais fiável e evita que os utilizadores a incorrer em custos globais de SMS de responder a uma mensagem de texto que foi enviada a partir de outro país.

**P: Pode alterar a quantidade de meus usuários tenham de introduzir o código de verificação de uma mensagem de texto, antes do sistema exceder o tempo limite de tempo?**

Em alguns casos, Sim. 

Para SMS unidirecional com o servidor MFA do Azure v7.0 ou superior, pode configurar o tempo limite de definição por definição de uma chave de registo. Depois do serviço de nuvem do MFA envia a mensagem de texto, é devolvido o código de verificação (ou código de acesso único) para o servidor MFA. O servidor MFA armazena o código na memória para 300 segundos por padrão. Se o utilizador não introduzir o código antes de passaram os 300 segundos, a autenticação é negada. Utilize estes passos para alterar a configuração de tempo limite padrão:

1. Vá para HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Criar uma chave de registo DWORD denominada **pfsvc_pendingSmsTimeoutSeconds** e definir o tempo em segundos que pretende que o servidor de MFA do Azure para armazenar os códigos de acesso de uso individual.

>[!TIP] 
>Se tiver vários servidores MFA, apenas aquele que processou o pedido de autenticação original sabe o código de verificação que foi enviado para o utilizador. Quando o usuário insere o código, o pedido de autenticação para validar têm de ser enviado para o mesmo servidor. Se a validação do código é enviada para um servidor diferente, a autenticação é negada. 

Para obter SMS bidirecional com o servidor MFA do Azure, pode configurar a definição de tempo limite no Portal de gestão do MFA. Se os utilizadores não responder a do SMS dentro do período de tempo limite definido, a autenticação é negada. 

Para obter SMS unidirecional com a Azure MFA na cloud (incluindo o adaptador AD FS ou a extensão de servidor de políticas de rede), não é possível configurar a definição de tempo limite. Azure AD armazena o código de verificação para 180 segundos. 

**P: Pode utilizar tokens de hardware com o servidor do Azure multi-factor Authentication?**

Se estiver a utilizar o servidor do Azure multi-factor Authentication, pode importar tokens de baseados no tempo e única palavra-passe (TOTP) de autenticação aberta (OATH) de terceiros e, em seguida, utilizá-los para verificação de dois passos.

Pode utilizar tokens de ActiveIdentity que são tokens OATH TOTP se colocar a chave secreta num arquivo CSV e importar para o servidor do Azure multi-factor Authentication. Pode utilizar OATH tokens com serviços de Federação do Active Directory (ADFS), autenticação baseada em formulários do servidor de informação Internet (IIS) e Remote Authentication Dial-In User Service (RADIUS), desde que o sistema de cliente pode aceitar a entrada do usuário.

Pode importar tokens OATH TOTP de terceiros com os seguintes formatos:  

- Contentor da chave simétrica (PSKC) portátil  
- Se o ficheiro contém um número de série, uma chave secreta no formato de Base 32 e um intervalo de tempo CSV  

**P: Posso utilizar o servidor do Azure multi-factor Authentication para proteger os serviços de Terminal?**

Sim, mas se estiver a utilizar o Windows Server 2012 R2 ou posterior só pode proteger os serviços de Terminal com o Gateway de ambiente de trabalho remoto (Gateway de RD).

Alterações de segurança no Windows Server 2012 R2 alterado como o Azure multi-factor Authentication Server se conecta ao pacote de segurança de autoridade de segurança Local (LSA) no Windows Server 2012 e versões anteriores. Para obter versões de serviços de Terminal no Windows Server 2012 ou anterior, pode [proteger uma aplicação com a autenticação do Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se estiver a utilizar o Windows Server 2012 R2, tem de Gateway de RD.

**P: Configurei o ID do autor da chamada no servidor MFA, mas os meus utilizadores recebem chamadas de multi-factor Authentication de um chamador anônimo.**

Quando as chamadas de multi-factor Authentication forem colocadas através da rede telefônica pública, às vezes, eles são encaminhados através de uma operadora que não suporta o ID do autor da chamada. Por este motivo, ID do autor da chamada não é garantido, mesmo que o sistema de multi-factor Authentication sempre envia-os.

**P: Por que meus usuários que está a ser-lhe pedidos para registar as suas informações de segurança?**
Há vários motivos que poderiam ser pedido aos utilizadores para registar as suas informações de segurança:

- O utilizador foi ativado para a MFA pelo seu administrador no Azure AD, mas não tem informações de segurança registadas na sua conta ainda.
- O utilizador tenha sido ativado para o self-service reposição palavra-passe no Azure AD. As informações de segurança irão ajudá-los a repor a palavra-passe no futuro, se esquecerem nunca.
- O utilizador aceder a uma aplicação que tenha uma política de acesso condicional para exigir a MFA e ainda não registou anteriormente para a MFA.
- O utilizador está a registar um dispositivo com o Azure AD (incluindo associação do Azure AD) e sua organização requer MFA para registo de dispositivos, mas o utilizador não registou anteriormente para a MFA.
- O utilizador está a gerar Windows Hello para empresas no Windows 10 (que requer MFA) e ainda não registou anteriormente para a MFA.
- A organização tem criado e ativado uma política de registo na MFA que foi aplicada ao usuário.
- O utilizador registado para MFA anteriormente, mas escolhi um método de verificação que uma vez que um administrador desativou. O utilizador, por conseguinte, deve passar por registo na MFA novamente para selecionar um novo método de verificação padrão.

## <a name="errors"></a>Erros

**P: O que devem fazer os utilizadores se veem uma mensagem de erro "o pedido de autenticação não é para uma conta ativada" ao utilizar notificações da aplicação móvel?**

Peça-lhes para seguir este procedimento para remover a conta a partir da aplicação móvel, em seguida, adicioná-lo novamente:

1. Aceda a [seu perfil de portal do Azure](https://account.activedirectory.windowsazure.com/profile/) e inicie sessão com a sua conta organizacional.
2. Selecione **verificação adicional de segurança**.
3. Remova a conta existente a partir da aplicação móvel.
4. Clique em **configurar**e, em seguida, siga as instruções para reconfigurar a aplicação móvel.

**P: O que devem fazer os utilizadores se é apresentada uma mensagem de erro de 0x800434D4L quando o início de sessão aplicação não baseada no browser?**

O erro 0x800434D4L ocorre quando tenta iniciar sessão para um aplicativo fora do browser, instalado num computador local, que não funciona com contas que necessitam de verificação de dois passos.

Uma solução alternativa para este erro é fazer com que o utilizador em separado totaliza relacionados com a administração e operações de não-administrador. Mais tarde, pode ligar as caixas de correio entre a sua conta de administrador e a conta não administrativa, de modo a que pode iniciar sessão Outlook ao utilizar a sua conta não administrativa. Para obter mais detalhes sobre esta solução, saiba como [dar um administrador a possibilidade de abrir e ver o conteúdo da caixa de correio de um utilizador](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Passos Seguintes

Se a sua pergunta não respondida aqui,. Deixe-nos comentários na parte inferior da página. Em alternativa, aqui estão algumas opções adicionais para obter ajuda:

* Pesquisa o [Base de dados de conhecimento de suporte Microsoft](https://www.microsoft.com/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) para soluções para problemas técnicos comuns.
* Procurar e navegue perguntas técnicas e respostas da Comunidade ou fazer sua própria pergunta no [fóruns do Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Se for um cliente herdado do PhoneFactor e tiver alguma dúvida ou precisa de ajuda para repor uma palavra-passe, utilize o [reposição de palavra-passe](mailto:phonefactorsupport@microsoft.com) link para abrir um incidente de suporte.
* Entre em contato com um analista de suporte por meio [suporte do Azure multi-factor Authentication Server (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Quando contactar-nos, é útil se pode incluir o máximo de informações sobre o seu problema quanto possível. Pode fornecer as informações incluem a página em que viu o erro, o código de erro específico, o ID de sessão específico e o ID do utilizador que viu o erro.
