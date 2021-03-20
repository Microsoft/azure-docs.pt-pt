---
title: Personalize uma instalação do Azure Ative Directory Connect
description: Este artigo explica as opções de instalação personalizadas para Azure AD Connect. Utilize estas instruções para instalar o Active Directory através do Azure AD Connect.
services: active-directory
keywords: o que é o Azure AD Connect, instale o Active Directory, componentes precisos para o Azure AD
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afeadff71bd373354b891bd6690d94d28fc0805
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92096356"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Instalação personalizada do Azure Ative Directory Connect
Utilize *definições personalizadas* no Azure Ative Directory (Azure AD) Ligue-se quando quiser mais opções para a instalação. Utilize estas definições, por exemplo, se tiver várias florestas ou se pretender configurar funcionalidades opcionais. Utilize configurações personalizadas em todos os casos em que a [instalação expressa](how-to-connect-install-express.md) não satisfaça as suas necessidades de implantação ou topologia.

Pré-requisitos:
- [Baixar Azure Ad Connect](https://go.microsoft.com/fwlink/?LinkId=615771).
- Complete os passos pré-requisitos em [Azure AD Connect: Hardware e pré-requisitos](how-to-connect-install-prerequisites.md). 
- Certifique-se de que tem as contas descritas nas [contas e permissões do Azure AD Connect](reference-connect-accounts-permissions.md).

## <a name="custom-installation-settings"></a>Configurações de instalação personalizadas 

Para configurar uma instalação personalizada para Azure AD Connect, procure nas páginas de assistente que as seguintes secções descrevem.

### <a name="express-settings"></a>Definições rápidas
Na página **Definições Expressas,** selecione **Personalizar** para iniciar uma instalação de configurações personalizadas.  O resto deste artigo guia-o através do processo de instalação personalizado. Utilize os seguintes links para obter rapidamente a informação para uma determinada página:

- [Componentes necessários](#install-required-components)
- [Inscrição do utilizador](#user-sign-in)
- [Ligue-se ao Azure AD](#connect-to-azure-ad)
- [Sincronização](#sync-pages)

### <a name="install-required-components"></a>Instalar os componentes necessários
Quando instalar os serviços de sincronização, pode deixar a secção de configuração opcional não ser selecionada. O Azure AD Connect configura tudo automaticamente. Configura uma instância SQL Server 2012 Express LocalDB, cria os grupos apropriados e atribui permissões. Se quiser alterar os predefinidos, limpe as caixas apropriadas.  O quadro seguinte resume estas opções e fornece links para informações adicionais. 

![Screenshot mostrando seleções opcionais para os componentes de instalação necessários no Azure AD Connect.](./media/how-to-connect-install-custom/requiredcomponents2.png)

| Configuração opcional | Description |
| --- | --- |
|Especifique um local de instalação personalizado| Permite-lhe alterar o caminho de instalação predefinido para Azure AD Connect.|
| Utilizar um SQL Server existente |Permite especificar o nome e o nome do servidor SQL. Escolha esta opção se já tiver um servidor de base de dados que pretende utilizar. Por **exemplo,** insira o nome da instância, uma vírgula e o número de porta se a sua instância SQL Server não tiver navegação ativada.  Em seguida, especifique o nome da base de dados Azure AD Connect.  Os seus privilégios SQL determinam se uma nova base de dados pode ser criada ou se o seu administrador SQL deve criar a base de dados com antecedência.  Se tiver permissões de administrador do SQL Server (SA), consulte [instalar o Azure AD Connect utilizando uma base de dados existente](how-to-connect-install-existing-database.md).  Se tiver permissões delegadas (DBO), consulte [instalar o Azure AD Connect utilizando permissões de administrador delegadas da SQL](how-to-connect-install-sql-delegation.md). |
| Utilizar uma conta de serviço existente |Por predefinição, o Azure AD Connect fornece uma conta de serviço virtual para os serviços de sincronização. Se utilizar uma instância remota do SQL Server ou utilizar um representante que exija autenticação, pode utilizar uma conta de *serviço gerida* ou uma conta de serviço protegida por palavra-passe no domínio. Nesses casos, insira a conta que pretende utilizar. Para executar a instalação, precisa de ser um SA em SQL para que possa criar credenciais de inscrição para a conta de serviço. Para mais informações, consulte [as contas e permissões do Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>Utilizando a mais recente construção, o administrador sql pode agora providenciar a base de dados fora da banda. Em seguida, o administrador AZURE AD Connect pode instalá-lo com os direitos do proprietário da base de dados.  Para obter mais informações, consulte [instalar o Azure AD Connect utilizando permissões de administrador delegadas sql](how-to-connect-install-sql-delegation.md).|
| Especificar grupos de sincronização personalizados |Por predefinição, quando os serviços de sincronização são instalados, o Azure AD Connect cria quatro grupos que são locais para o servidor. Estes grupos são Administradores, Operadores, Navegação e Redefinição de Password. Pode especificar aqui os seus próprios grupos. Os grupos devem ser locais no servidor. Não podem estar no domínio. |
|Definições de sincronização de importações (pré-visualização)|Permite-lhe importar configurações de outras versões do Azure AD Connect.  Para obter mais informações, consulte [as definições de configuração de Ligação AD Azure Ad importando e exportando](how-to-connect-import-export-config.md).|

### <a name="user-sign-in"></a>Início de sessão do utilizador
Depois de instalar os componentes necessários, selecione o método de inscrição único dos seus utilizadores. A tabela que se segue descreve brevemente as opções disponíveis. Para obter uma descrição completa dos métodos de início de sessão, consulte [Início de sessão do utilizador](plan-connect-user-signin.md).

![Screenshot que mostra a página "User's Entrar". A opção "Sincronização de Hash password" é selecionada.](./media/how-to-connect-install-custom/usersignin4.png)

| Opção de inscrição única | Description |
| --- | --- |
| Sincronização de hash de palavra-passe |Os utilizadores podem iniciar sômposições nos serviços de cloud da Microsoft, como o Microsoft 365, utilizando a mesma palavra-passe que utilizam na sua rede de acesso. As palavras-passe do utilizador são sincronizadas com a Azure AD como um hash de senha. A autenticação ocorre na nuvem. Para obter mais informações, consulte [a sincronização de hash password](how-to-connect-password-hash-synchronization.md). |
|Autenticação pass-through|Os utilizadores podem iniciar sômposições nos serviços de cloud da Microsoft, como o Microsoft 365, utilizando a mesma palavra-passe que utilizam na sua rede de acesso.  As palavras-passe do utilizador são validadas através do controlador de domínio ative diretório no local.
| Federação com o AD FS |Os utilizadores podem iniciar sômposições nos serviços de cloud da Microsoft, como o Microsoft 365, utilizando a mesma palavra-passe que utilizam na sua rede de acesso.  Os utilizadores são redirecionados para o seu local Azure Directory Federation Services (AD FS) para iniciar seduca. A autenticação ocorre no local. |
| Federação com o PingFederate|Os utilizadores podem iniciar sômposições nos serviços de cloud da Microsoft, como o Microsoft 365, utilizando a mesma palavra-passe que utilizam na sua rede de acesso.  Os utilizadores são redirecionados para as suas situações de PingFederate no local para iniciar seduca. A autenticação ocorre no local. |
| Não configurar |Nenhuma função de insição do utilizador está instalada ou configurada. Escolha esta opção se já tiver um servidor de federação de terceiros ou outra solução no lugar. |
|Ativar um único sinal|Esta opção está disponível tanto com a sincronização de haxixe de palavra-passe como com a autenticação pass-through. Proporciona uma única experiência de inscrição para utilizadores de desktop em redes corporativas. Para obter mais informações, consulte ['S'S.'](how-to-connect-sso.md) </br></br>**Nota:** Para os clientes AD FS, esta opção não está disponível. A AD FS já oferece o mesmo nível de inscrição única.</br>

### <a name="connect-to-azure-ad"></a>Ligar ao Azure AD
Na página **Connect to Azure AD,** insira uma conta de administração global e senha. Se selecionou **a Federação com FS AD** na página anterior, não faça sônio com uma conta que esteja num domínio que pretende ativar para a federação. 

Você pode querer usar uma conta no domínio *de onmicrosoft.com* padrão, que vem com o seu inquilino AZURE AD. Esta conta é utilizada apenas para criar uma conta de serviço no Azure AD. Não é usado depois do fim da instalação.
  
![Screenshot mostrando a página "Connect to Azure AD".](./media/how-to-connect-install-custom/connectaad.png)

Se a sua conta de administração global tiver a autenticação multifactor ativada, forneça novamente a palavra-passe na janela de entrada e terá de completar o desafio de autenticação multifactor. O desafio pode ser um código de verificação ou um telefonema.  

![Screenshot mostrando a página "Connect to Azure AD". Um campo de autenticação multifactor solicita ao utilizador um código.](./media/how-to-connect-install-custom/connectaadmfa.png)

A conta global de administração também pode ter [uma gestão de identidade privilegiada](../privileged-identity-management/pim-getting-started.md) habilitada.

Se vir um erro ou tiver problemas de conectividade, consulte [problemas de conectividade de resolução de problemas](tshoot-connect-connectivity.md).

## <a name="sync-pages"></a>Páginas sincronizadas

As secções seguintes descrevem as páginas na secção **Sync.**

### <a name="connect-your-directories"></a>Ligar os diretórios
Para se ligar aos Serviços de Domínio do Diretório Ativo (Azure AD DS), o Azure AD Connect necessita do nome da floresta e credenciais de uma conta que tenha permissões suficientes.

![Screenshot que mostra a página "Conecte os seus diretórios".](./media/how-to-connect-install-custom/connectdir01.png)

Depois de introduzir o nome da floresta e selecionar  **Add Diretório,** aparece uma janela. A tabela a seguir descreve as suas opções.

| Opção | Descrição |
| --- | --- |
| Criar conta nova | Crie a conta Azure AD DS que o Azure AD Connect necessita para se ligar à floresta de Diretório Ativo durante a sincronização do diretório. Depois de selecionar esta opção, insira o nome de utilizador e a palavra-passe para uma conta administrada da empresa.  O Azure AD Connect utiliza a conta de administração da empresa fornecida para criar a conta AZure AD DS necessária. Pode introduzir a parte de domínio no formato NetBIOS ou no formato FQDN. Ou seja, insira *o administrador da FABRIKAM\ou* *fabrikam.com\administrador*. |
| Utilizar conta existente | Forneça uma conta Azure AD DS existente que o Azure AD Connect pode usar para ligar à floresta de Diretório Ativo durante a sincronização do diretório. Pode introduzir a parte de domínio no formato NetBIOS ou no formato FQDN. Ou seja, *insira FABRIKAM\syncuser* ou *fabrikam.com\syncuser*. Esta conta pode ser uma conta de utilizador regular porque necessita apenas das permissões de leitura por defeito. Mas dependendo do seu cenário, pode precisar de mais permissões. Para mais informações, consulte [as contas e permissões do Azure AD Connect](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account). |

![Screenshot mostrando a página "Connect Directory" e a janela da conta florestal A D, onde pode optar por criar uma nova conta ou usar uma conta existente.](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> A partir da construção 1.4.18.0, não é possível utilizar uma conta administrada ou administração de domínio como conta de conector Azure AD DS. Quando seleciona **Utilizar a conta existente**, se tentar introduzir uma conta administrada da empresa ou uma conta de administração de domínio, vê o seguinte erro: "Não é permitida a utilização de uma conta de administrador de Empresa ou de Domínio para a sua conta de floresta de AD. Deixe o Azure AD Connect criar a conta para si ou especificar uma conta de sincronização com as permissões corretas."
>

### <a name="azure-ad-sign-in-configuration"></a>Configuração do início de sessão do Azure AD
Na página **de configuração de inscrição AD Azure,** reveja os domínios do nome principal do utilizador (UPN) nas instalações Azure AD DS. Estes domínios da UPN foram verificados em Azure AD. Nesta página, configura o atributo a utilizar para o nome de utilizadorPrincipal.

![Screenshot mostrando domínios não verificados na página "Azure A D's sign-in".](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

Reveja todos os domínios marcados como **Não Adicionados** ou **Não Verificados**. Certifique-se de que os domínios utilizados foram verificados no Azure AD. Depois de verificar os seus domínios, selecione o ícone de atualização circular. Para obter mais informações, consulte [Adicionar e verificar o domínio](../fundamentals/add-custom-domain.md).

Os utilizadores utilizam o atributo *userPrincipalName* quando fazem sedudas para Azure AD e Microsoft 365. O Azure AD deve verificar os domínios, também conhecidos como sufixo UPN, antes de os utilizadores serem sincronizados. A Microsoft recomenda que mantenha o utilizador predefinido PrencipalName. 

Se o atributo userPrincipalName não for exequível e não puder ser verificado, então pode selecionar outro atributo. Pode, por exemplo, selecionar o e-mail como o atributo que detém o ID de inscrição. Quando utiliza um atributo diferente do nome do utilizadorPrincipalName, é conhecido como um *ID alternativo*. 

O valor do atributo de ID alternativo deve seguir a norma RFC 822. Você pode usar um ID alternativo com sincronização de haxixe de palavra-passe, autenticação de passagem e federação. No Ative Directory, o atributo não pode ser definido como multivalorizado, mesmo que tenha apenas um único valor. Para obter mais informações sobre o ID alternativo, consulte [a autenticação pass-through: Perguntas frequentes](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname).

>[!NOTE]
> Quando ativar a autenticação de passagem, deve ter pelo menos um domínio verificado para continuar através do processo de instalação personalizado.

> [!WARNING]
> Os IDs alternativos não são compatíveis com todas as cargas de trabalho do Microsoft 365. Para obter mais informações, consulte [configurar identificações alternativas de inscrição.](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)
>

### <a name="domain-and-ou-filtering"></a>Filtragem de domínios e de UOs
Por predefinição, todos os domínios e unidades organizacionais (OUs) são sincronizados. Se não quiser sincronizar alguns domínios ou OUs a AD Azure, pode limpar as seleções apropriadas.  

![Screenshot mostrando a página de filtragem Domain e O U.](./media/how-to-connect-install-custom/domainoufiltering.png)  

Esta página configura a filtragem baseada em domínios e baseada em OU. Se planeia fazer alterações, consulte [a filtragem baseada em domínio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) e a [filtragem baseada em OU.](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) Algumas OUs são essenciais para a funcionalidade, por isso deve deixá-las selecionadas.

Se utilizar a filtragem baseada em OU com uma versão Azure AD Connect com mais de 1.1.524.0, as novas OUs são sincronizadas por padrão. Se não quiser que as novas OUs sejam sincronizadas, então pode ajustar o comportamento padrão após o passo [de filtragem baseado em U.](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) Para a Azure AD Connect 1.1.524.0 ou mais tarde, pode indicar se pretende que as novas OUs sejam sincronizadas.

Se pretender utilizar [a filtragem baseada em grupo, certifique-se](#sync-filtering-based-on-groups)de que o OU com o grupo está incluído e não é filtrado utilizando a filtragem da OU. A filtragem da U é avaliada antes da filtragem baseada em grupo ser avaliada.

Também é possível que alguns domínios sejam inacessíveis devido às restrições de firewall. Estes domínios não são selecionados por defeito, e apresentam um aviso.  

![Screenshot mostrando domínios inacessíveis.](./media/how-to-connect-install-custom/unreachable.png)  

Se vir este aviso, certifique-se de que estes domínios são de facto inacessíveis e que o aviso é esperado.

### <a name="uniquely-identifying-your-users"></a>Identificar os utilizadores de forma exclusiva

Na página de Identificação dos **utilizadores,** escolha como identificar os utilizadores nos seus diretórios no local e como identificá-los utilizando o atributo SourceAnchor.

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Selecione a forma como os utilizadores devem ser identificados nos seus diretórios no local
Ao utilizar a funcionalidade *Matching across forests,* pode definir como os utilizadores das suas florestas AZure AD DS estão representados no Azure AD. Um utilizador pode ser representado apenas uma vez em todas as florestas ou pode ter uma combinação de contas ativadas e desativadas. O utilizador também pode ser representado como um contacto em algumas florestas.

![Screenshot mostrando a página onde pode identificar exclusivamente os seus utilizadores.](./media/how-to-connect-install-custom/unique2.png)

| Definições | Descrição |
| --- | --- |
| [Os utilizadores são representados apenas uma vez em todas as florestas](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Todos os utilizadores são criados como objetos individuais no Azure AD. Os objetos não estão unidos no metaverso. |
| [Atributo de correio](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Esta opção associa utilizadores e contactos se o atributo de correio tiver o mesmo valor em florestas diferentes. Utilize esta opção quando os seus contactos foram criados utilizando o GALSync. Se escolher esta opção, os objetos do utilizador cujo atributo de correio está despovoado não são sincronizados com a Azure AD. |
| [ObjectSID e msExchangeMasterAccountSID/ msRTCSIP-OriginatorSID](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Esta opção associa um utilizador ativado numa floresta conta a um utilizador desativado numa floresta de recursos. No Exchange, esta configuração é conhecida como uma caixa de correio ligada. Pode usar esta opção se utilizar apenas a Lync e se o Exchange não estiver presente na floresta de recursos. |
| Atributos SAMAccountName e MailNickName |Esta opção junta-se a atributos em que se espera que o ID de inscrição para o utilizador seja encontrado. |
| Escolha um atributo específico |Esta opção permite-lhe selecionar o seu próprio atributo. Se escolher esta opção, os objetos do utilizador cujo atributo (selecionado) é despovoado não são sincronizados com Azure AD. **Limitação:** Apenas os atributos que já estão no metaverso estão disponíveis para esta opção. |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>Selecione como os utilizadores devem ser identificados usando uma âncora de origem
O atributo *SourceAnchor* é imutável durante o tempo de vida de um objeto do utilizador. É a chave primária que liga o utilizador no local com o utilizador em Azure AD.

| Definições | Descrição |
| --- | --- |
| Deixe Azure gerir a âncora de origem | Selecione esta opção se pretender que o Azure AD escolha o atributo por si. Se selecionar esta opção, o Azure AD Connect aplica a lógica de seleção de atributos sourceAnchor descrita na [Utilização de Ms-DS-ConsistênciaGuid como fonteAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Após o acabamento da instalação personalizada, vê qual o atributo escolhido como atributo origemAnchor. |
| Escolha um atributo específico | Selecione esta opção se quiser especificar um atributo AD existente como o atributo SourceAnchor. |

Como o atributo SourceAnchor não pode ser alterado, tem de escolher um atributo apropriado. Um bom candidato é objectGUID. Este atributo não é alterado a menos que a conta de utilizador seja movida entre florestas ou domínios. Não escolha atributos que possam mudar quando uma pessoa se casa ou muda de atribuições. 

Não pode utilizar atributos que incluam um sinal (@), por isso não pode usar e-mail e userPrincipalName. O atributo também é sensível a casos, por isso, quando mover um objeto entre as florestas, certifique-se de preservar maiúsculas e minúsculas. Os atributos binários são codificados pela Base64, mas outros tipos de atributos permanecem no seu estado não codificado. 

Em cenários de federação e algumas interfaces AD AZure, o atributo sourceAnchor também é conhecido como *imutávelID*. 

Para obter mais informações sobre a âncora de origem, consulte [os conceitos de Design.](plan-connect-design-concepts.md#sourceanchor)

### <a name="sync-filtering-based-on-groups"></a>Filtragem de sincronização baseada em grupos
A função filtragem em grupo permite sincronizar apenas um pequeno subconjunto de objetos para um piloto. Para utilizar esta funcionalidade, crie um grupo para o efeito no seu caso no local do Ative Directory. Em seguida, adicione os utilizadores e grupos que devem ser sincronizados com o Azure AD como membros diretos. Mais tarde, pode adicionar utilizadores ou remover utilizadores deste grupo para manter a lista de objetos que devem estar presentes no Azure AD. 

Todos os objetos que pretende sincronizar devem ser membros diretos do grupo. Os utilizadores, grupos, contactos e computadores ou dispositivos devem ser membros diretos. A adesão ao grupo não está resolvida. Quando se adiciona um grupo como membro, apenas o próprio grupo é adicionado. Os seus membros não são adicionados.

![Screenshot mostrando a página onde pode escolher como filtrar utilizadores e dispositivos.](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Esta funcionalidade destina-se a suportar apenas uma implantação piloto. Não o uses numa produção completa.
>

Numa produção completa, seria difícil manter um único grupo e todos os seus objetos para sincronizar. Em vez da função filtragem em grupos, utilize um dos métodos descritos na [filtragem Configure](how-to-connect-sync-configure-filtering.md).

### <a name="optional-features"></a>Funcionalidades opcionais
Na página seguinte, pode selecionar funcionalidades opcionais para o seu cenário.

>[!WARNING]
>As versões Azure AD Connect 1.0.8641.0 e anteriormente dependem do Serviço de Controlo de Acesso Azure para a gravação de passwords.  Este serviço foi reformado a 7 de novembro de 2018.  Se utilizar alguma destas versões do Azure AD Connect e tiver ativado a gravação da palavra-passe, os utilizadores poderão perder a capacidade de alterar ou redefinir as suas palavras-passe quando o serviço for retirado. Estas versões do Azure AD Connect não suportam a gravação de palavras-passe.
>
>Para mais informações, consulte [Migrar do Serviço de Controlo de Acesso Azure.](../azuread-dev/active-directory-acs-migration.md)
>
>Se pretender utilizar a gravação da palavra-passe, descarregue a [versão mais recente do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

![Screenshot mostrando a página "Funcionalidades Opcionais".](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Se o Azure AD Sync ou a Sincronização Direta (DirSync) estiverem ativos, não ative quaisquer funcionalidades de writeback no Azure AD Connect.



| Funcionalidades opcionais | Description |
| --- | --- |
| Implementação híbrida de troca |A funcionalidade de implementação híbrida Exchange permite a coexistência de caixas de correio Exchange tanto no local como na Microsoft 365. O Azure AD Connect sincroniza um conjunto específico de [atributos](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) do Azure AD de volta ao seu diretório no local. |
| Trocar pastas públicas de correio | A funcionalidade de pastas públicas Exchange mail permite sincronizar objetos de pastas públicas ativados por correio a partir do seu caso de Ative Directory para Azure AD. |
| Aplicação Azure AD e filtragem de atributos |Ao ativar a aplicação AD AD e a filtragem de atributos Azure, pode adaptar o conjunto de atributos sincronizados. Esta opção adiciona mais duas páginas de configuração ao assistente. Para obter mais informações, consulte [Aplicação Azure AD e filtragem de atributos](#azure-ad-app-and-attribute-filtering). |
| Sincronização de hash de palavra-passe |Se selecionar a federação como solução de entrada, pode ativar a sincronização do hash de palavra-passe. Então pode usá-lo como uma opção de reserva.  </br></br>Se selecionou a autenticação pass-through, pode ativar esta opção para garantir suporte a clientes antigos e fornecer uma cópia de segurança.</br></br> Para obter mais informações, consulte [a sincronização de hash password](how-to-connect-password-hash-synchronization.md).|
| Repetição de escrita de palavras-passe |Utilize esta opção para garantir que as alterações de palavra-passe originárias do AZure AD sejam escritas de volta para o seu diretório no local. Para mais informações, consulte [Introdução à gestão de palavras-passe](../authentication/tutorial-enable-sspr.md) |
| Repetição de escrita do grupo |Se utilizar o Microsoft 365 Groups, poderá representar grupos no seu caso no local do Ative Directory. Esta opção só está disponível se tiver o Exchange no seu local de instância do Ative Directory. Para obter mais informações, consulte [a gravação do grupo Azure AD Connect](how-to-connect-group-writeback.md).|
| Repetição de escrita do dispositivo |Para cenários de acesso condicional, utilize esta opção para escrever objetos de dispositivo em Azure AD para a sua instância no local do Ative Directory. Para mais informações, consulte [Ativar a repetição de escrita do dispositivo no Azure AD Connect](how-to-connect-device-writeback.md). |
| Sincronização de atributos de extensões de diretórios |Selecione esta opção para sincronizar atributos especificados para Azure AD. Para obter mais informações, consulte [Extensões de diretórios](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Aplicação Azure AD e filtragem de atributos
Se pretende limitar os atributos de sincronização ao AD Azure, comece por selecionar os serviços que utiliza. Se alterar as seleções desta página, terá de selecionar explicitamente um novo serviço repetindo o assistente de instalação.

![Screenshot mostrando funcionalidades opcionais de aplicações Azure A D.](./media/how-to-connect-install-custom/azureadapps2.png)

Com base nos serviços selecionados no passo anterior, esta página mostra todos os atributos que estão sincronizados. Esta lista é uma combinação de todos os tipos de objetos que estão sendo sincronizados. Se precisar de alguns atributos para não se manter insincronizado, pode limpar a seleção desses atributos.

![Screenshot mostrando características opcionais de atributos Azure A D.](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> Remover atributos pode afetar a funcionalidade. Para obter boas práticas e recomendações, consulte [Atributos para sincronizar.](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize)
>

### <a name="directory-extension-attribute-sync"></a>Sincronização de atributos de Extensões de Diretórios
Pode estender o esquema em Azure AD utilizando atributos personalizados que a sua organização adicionou ou utilizando outros atributos no Ative Directory. Para utilizar esta função, na página **Funcionalidades Opcionais,** selecione **Sincronização de atributos de extensão de diretório**. Na página Extensões do **Diretório,** pode selecionar mais atributos para sincronizar.

>[!NOTE]
>O campo **Atributos Disponíveis** é sensível a casos.

![Screenshot mostrando a página "Extensões do Diretório".](./media/how-to-connect-install-custom/extension2.png)

Para obter mais informações, consulte [Extensões de diretórios](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on"></a>Habilitando um único sinal
Na página **de assinatura única,** configurar um único sinal para utilização com sincronização de palavra-passe ou autenticação pass-through. Faça este passo uma vez por cada floresta que está sendo sincronizada com Azure AD. A configuração envolve dois passos:

1.  Crie a conta de computador necessária no seu caso no local do Ative Directory.
2.  Configure a zona intranet das máquinas cliente para suportar uma única sação.

#### <a name="create-the-computer-account-in-active-directory"></a>Criar a conta de computador no Active Directory
Para cada floresta que foi adicionada no Azure AD Connect, é necessário fornecer credenciais de administrador de domínio para que a conta de computador possa ser criada em cada floresta. As credenciais são usadas apenas para criar a conta. Não são armazenados ou usados para qualquer outra operação. Adicione as credenciais na página **de inscrição única** ativa, como mostra a seguinte imagem.

![Screenshot mostrando a página "Ativar um único sinal de insusitado". As credenciais da floresta são adicionadas.](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Podes saltar florestas onde não queiras usar uma única sposição.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Configurar a zona intranet para máquinas clientes
Para garantir que o cliente assina automaticamente na zona intranet, certifique-se de que o URL faz parte da zona intranet. Este passo garante que o computador ligado ao domínio envia automaticamente um bilhete Kerberos para a Azure AD quando está ligado à rede corporativa.

Num computador que dispõe de ferramentas de gestão de Políticas de Grupo:

1.  Abra as ferramentas de gestão da Política de Grupo.
2.  Editar a política de grupo que será aplicada a todos os utilizadores. Por exemplo, a política de domínio predefinido.
3.  Aceda à **configuração do utilizador**  >  **Modelos Administrativos**  >  **Windows Componentes** Internet  >  **Explorer** Internet  >  **Control Panel**  >  **Security Page**. Em seguida, selecione **Site para a Lista de Atribuição de Zonas**.
4.  Habilitar a política. Em seguida, na caixa de diálogo, introduza um nome de valor `https://autologon.microsoftazuread-sso.com` e valor de `1` . A sua configuração deve parecer a seguinte imagem.
  
    ![Screenshot mostrando zonas intranet.](./media/how-to-connect-install-custom/sitezone.png)

6.  Selecione **OK** duas vezes.

## <a name="configuring-federation-with-ad-fs"></a>Configurar a federação com o AD FS
Pode configurar AD FS com Azure AD Connect em apenas alguns cliques. Antes de começar, precisa:

* Windows Server 2012 R2 ou mais tarde para o servidor da federação. A gestão remota deve ser ativada.
* Windows Server 2012 R2 ou mais tarde para o servidor Proxy da Aplicação Web. A gestão remota deve ser ativada.
* Um certificado TLS/SSL para o nome de serviço da federação que pretende utilizar (por exemplo, sts.contoso.com).

>[!NOTE]
>Pode atualizar um certificado TLS/SSL para a sua quinta AD FS utilizando o Azure AD Connect, mesmo que não o utilize para gerir a confiança da sua federação.

### <a name="ad-fs-configuration-prerequisites"></a>Pré-requisitos de configuração AD FS
Para configurar a sua fazenda AD FS utilizando o Azure AD Connect, certifique-se de que o WinRM está ativado nos servidores remotos. Certifique-se de que completou as outras tarefas na [Federação pré-requisitos.](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration) Certifique-se também de que segue os requisitos das portas que estão listados na tabela de [servidores AZure AD Connect e Federation/WAP.](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap)

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Criar um novo farm do AD FS ou utilizar um existente
Você pode usar uma fazenda AD FS existente ou criar uma nova. Se optar por criar um novo, deve fornecer o certificado TLS/SSL. Se o certificado TLS/SSL estiver protegido por uma palavra-passe, é solicitado que forneça a palavra-passe.

![Screenshot mostrando a página "A D F S Farm"](./media/how-to-connect-install-custom/adfs1.png)

Se optar por utilizar uma quinta AD FS existente, consulte a página onde pode configurar a relação de confiança entre a AD FS e a Azure AD.

>[!NOTE]
>Você pode usar Azure AD Connect para gerir apenas uma fazenda AD FS. Se você tem um fundo de federação existente onde Azure AD está configurado na fazenda AD FS selecionada, Azure AD Connect recria a confiança do zero.

### <a name="specify-the-ad-fs-servers"></a>Especificar os servidores do AD FS
Especifique os servidores onde pretende instalar O FS AD. Pode adicionar um ou mais servidores, dependendo das suas necessidades de capacidade. Antes de configurar esta configuração, junte todos os servidores AD FS ao Ative Directory. Este passo não é necessário para os servidores Proxy da Aplicação Web. 

A Microsoft recomenda a instalação de um único servidor do AD FS para implementações de teste e piloto. Após a configuração inicial, pode adicionar e implementar mais servidores para atender às suas necessidades de escala, executando novamente o Azure AD Connect.

> [!NOTE]
> Antes de configurar esta configuração, certifique-se de que todos os seus servidores estão unidos a um domínio AD Azure.
>


![Screenshot mostrando a página "Servidores da Federação".](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Especificar os servidores Proxy de Web Apps
Especifique os seus servidores Proxy de aplicação web. O servidor Proxy da Aplicação Web é implantado na rede do seu perímetro, virado para a extranet. Suporta pedidos de autenticação da extranet. Pode adicionar um ou mais servidores, dependendo das suas necessidades de capacidade. 

A Microsoft recomenda a instalação de um único servidor Proxy de aplicação web para implementações de testes e pilotos. Após a configuração inicial, pode adicionar e implementar mais servidores para atender às suas necessidades de escala, executando novamente o Azure AD Connect. Recomendamos que tenha um número equivalente de servidores proxy para satisfazer a autenticação da intranet.

> [!NOTE]
> - Se a conta que utiliza não for um administrador local nos servidores Proxy da Aplicação Web, então é solicitado para obter credenciais de administração.
> - Antes de especificar os servidores Proxy da Aplicação Web, certifique-se de que existe conectividade HTTP/HTTPS entre o servidor Azure AD Connect e o servidor Proxy da Aplicação Web.
> - Certifique-se de que existe conectividade HTTP/HTTPS entre o Servidor de Aplicações Web e o servidor AD FS para permitir que os pedidos de autenticação fluam.
>


![Screenshot mostrando a página de servidores Proxy da Aplicação Web.](./media/how-to-connect-install-custom/adfs3.png)

É-lhe pedido que introduza credenciais para que o servidor de aplicações web possa estabelecer uma ligação segura ao servidor AD FS. Estas credenciais devem ser para uma conta de administrador local no servidor AD FS.

![Screenshot mostrando a página "Credenciais". As credenciais do administrador são inseridas no campo do nome de utilizador e no campo da palavra-passe.](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Especifique a conta de serviço para o serviço do AD FS
O serviço AD FS requer uma conta de serviço de domínio para autenticar os utilizadores e procurar informações do utilizador no Ative Directory. Pode suportar dois tipos de contas de serviço:

* **Conta de serviço gerida pelo grupo**: Este tipo de conta foi introduzido em DS AD pelo Windows Server 2012. Este tipo de conta fornece serviços como AD FS. É uma única conta na qual não precisa de atualizar a palavra-passe regularmente. Utilize esta opção se já tiver controladores de domínio do Windows Server 2012 no domínio a que pertencem os servidores do AD FS.
* **Conta de utilizador de domínio**: Este tipo de conta requer que forneça uma palavra-passe e a atualize regularmente quando expirar. Utilize esta opção apenas quando não tiver controladores de domínio do Windows Server 2012 no domínio a que os seus servidores AD FS pertencem.

Se selecionou **Criar uma conta de serviço gerida por grupo** e esta funcionalidade nunca tiver sido utilizada no Ative Directory, insira as credenciais de administração da empresa. Estas credenciais são utilizadas para iniciar o armazenamento de chaves e ativar a funcionalidade no Active Directory.

> [!NOTE]
> O Azure AD Connect verifica se o serviço AD FS já está registado como nome principal de serviço (SPN) no domínio.  O Azure AD DS não permite que as SPNs duplicadas sejam registadas ao mesmo tempo.  Se for encontrada uma SPN duplicada, não pode prosseguir até que o SPN seja removido.

![Screenshot mostrando a página "A D F S service account".](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>Selecione o domínio AD A Azul que pretende federar
Utilize a página **de Domínio AZure AD** para configurar a relação da federação entre AD FS e Azure AD. Aqui, você configura AD FS para fornecer fichas de segurança ao Azure AD. Você também configura Azure AD para confiar nos tokens deste caso AD FS. 

Nesta página, pode configurar apenas um único domínio na instalação inicial. Pode configurar mais domínios posteriormente, executando novamente o Azure AD Connect.

![Screenshot que mostra a página "Azure A D Domain".](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Verificar o domínio do Azure AD selecionado para federação
Quando seleciona o domínio que pretende federar, o Azure AD Connect fornece informações que pode utilizar para verificar um domínio não verificado. Para obter mais informações, consulte [Adicionar e verificar o domínio](../fundamentals/add-custom-domain.md).

![Screenshot mostrando a página "Azure A D Domain", incluindo informações que pode usar para verificar o domínio.](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> O Azure AD Connect tenta verificar o domínio durante a fase de configuração. Se não adicionar os registos necessários do Sistema de Nome de Domínio (DNS), a configuração não pode ser concluída.
>


## <a name="configuring-federation-with-pingfederate"></a>Configurar a federação com o PingFederate
Pode configurar PingFederate com Azure AD Connect em apenas alguns cliques. São necessários os seguintes pré-requisitos:
- PingFederate 8.4 ou mais tarde.  Para obter mais informações, consulte [a integração do PingFederate com o Azure Ative Directory e o Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html).
- Um certificado TLS/SSL para o nome de serviço da federação que pretende utilizar (por exemplo, sts.contoso.com).

### <a name="verify-the-domain"></a>Verificar o domínio
Depois de optar por criar uma federação utilizando o PingFederate, é-lhe pedido que verifique o domínio que pretende federar.  Selecione o domínio do menu suspenso.

![Screenshot que mostra a página "Azure A D Domain". O domínio de exemplo "contoso.com" é selecionado.](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>Exportar as definições do PingFederate


Configure PingFederate como o servidor da federação para cada domínio federado de Azure.  Selecione **Definições de Exportação** para partilhar esta informação com o seu administrador PingFederate.  O administrador do servidor da federação atualiza a configuração e, em seguida, fornece o URL do servidor PingFederate e o número de porta para que o Azure AD Connect possa verificar as definições de metadados.  

![Screenshot mostrando a página "Definições PingFederate". O botão "Definições de exportação" aparece perto do topo da página.](./media/how-to-connect-install-custom/ping2.png)

Contacte o administrador de PingFederate para resolver quaisquer problemas de validação.  A imagem a seguir mostra informações sobre um servidor PingFederate que não tem uma relação de confiança válida com o Azure.

![Screenshot mostrando informações do servidor: O servidor PingFederate foi encontrado, mas a ligação do fornecedor de serviços para Azure está desaparecida ou desativada.](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Verificar a conectividade da federação
O Azure AD Connect tenta validar os pontos finais de autenticação que obtém dos metadados PingFederate no passo anterior.  Azure AD Connect tenta resolver os pontos finais utilizando os servidores DNS locais.  Em seguida, tenta resolver os pontos finais utilizando um fornecedor externo de DNS.  Contacte o administrador de PingFederate para resolver quaisquer problemas de validação.  

![Screenshot mostrando a página "Verificar conectividade".](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>Verificar o s-in da federação
Por fim, pode verificar o fluxo de início de sessão federado recém configurado, ao iniciar sessão no domínio federado. Se o seu sinal-in for bem sucedido, então a federação com PingFederate está configurada com sucesso.

![Screenshot mostrando a página "Verificar o login federado". Uma mensagem na parte inferior indica um início de sção bem sucedido.](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Páginas de configuração e verificação
A configuração ocorre na página **Configure.**

> [!NOTE]
> Se configurar a federação, certifique-se de que também configura a [resolução de nome para servidores da federação](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) antes de continuar a instalação.
>



![Screenshot mostrando a página "Pronto para configurar".](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>Use o modo de preparação
É possível configurar um novo servidor de sincronização em paralelo com o modo de paragem. Se quiser utilizar esta configuração, apenas um servidor de sincronização pode exportar para um diretório na nuvem. Mas se quiser mover-se de outro servidor, por exemplo, um servidor que executa o DirSync, então pode ativar o Azure AD Connect no modo de fase. 

Quando ativa a configuração da montagem, o motor de sincronização importa e sincroniza os dados normalmente. Mas não exporta dados para a Azure AD ou Diretório Ativo. No modo de fase, a função de sincronização de palavra-passe e a função de desativação da palavra-passe são desativadas.

![Screenshot mostrando a opção "Ativar o modo de preparação".](./media/how-to-connect-install-custom/stagingmode.png)

No modo de preparação, pode fazer as alterações necessárias ao motor de sincronização e rever o que será exportado. Quando lhe parecer que a configuração está boa, execute novamente o assistente de instalação e desative o modo de teste. 

Os dados são agora exportados para Azure AD a partir do servidor. Não se esqueça de desativar ao mesmo tempo o outro servidor para que haja apenas um servidor a exportar ativamente.

Para obter mais informações, consulte [Modo de teste](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Verificar a configuração de federação
A Azure AD Connect verifica as definições de DNS quando seleciona o botão **Verificar.** Verifica as seguintes definições:

* **Conectividade intranet**
    * Resolver a federação FQDN: Azure AD Connect verifica se o DNS pode resolver a federação FQDN para garantir a conectividade. Se o Azure AD Connect não conseguir resolver o FQDN, então a verificação falha. Para completar a verificação, certifique-se de que está presente um registo DNS para o serviço da federação FQDN.
    * DNS Um registo: Azure AD Connect verifica se o seu serviço de federação tem um registo A. Na ausência de um registo A, a verificação falha. Para completar a verificação, crie um registo A (não um registo CNAME) para a sua federação FQDN.
* **Conectividade extranet**
    * Resolver a federação FQDN: Azure AD Connect verifica se o DNS pode resolver a federação FQDN para garantir a conectividade.

      ![Screenshot mostrando a página "Instalação completa".](./media/how-to-connect-install-custom/completed.png)

      ![Screenshot mostrando a página "Instalação completa". Uma mensagem indica que a configuração intranet foi verificada.](./media/how-to-connect-install-custom/adfs7.png)

Para validar a autenticação de ponta a ponta, efetue manualmente um ou mais dos seguintes testes:

* Quando a sincronização terminar, no Azure AD Connect, utilize a tarefa adicional de **login federada** para verificar a autenticação de uma conta de utilizador no local que escolher.
* A partir de uma máquina de domínio na intranet, certifique-se de que pode iniciar sôm no navegador. Ligue-se a https://myapps.microsoft.com . Em seguida, utilize a sua conta de login para verificar a inscrição. A conta de administrador da Azure AD DS incorporada não está sincronizada e não pode usá-la para verificação.
* Certifique-se de que pode iniciar sôms a partir de um dispositivo na extranet. Numa máquina doméstica ou num dispositivo móvel, ligue-se a https://myapps.microsoft.com . Então forneça as suas credenciais.
* Valide o início de sessão de cliente avançado. Ligue-se a https://testconnectivity.microsoft.com . Em seguida, selecione **Office**  >  **365 Office 365 Single Sign-On Test**.

## <a name="troubleshoot"></a>Resolução de problemas
Esta secção contém informações de resolução de problemas que pode utilizar se tiver algum problema durante a instalação do Azure AD Connect.

Quando personalizar uma instalação Azure AD Connect, na página **de componentes necessários,** pode selecionar **Utilize um servidor SQL existente**. Pode ver o seguinte erro: "A base de dados ADSync já contém dados e não pode ser substituída. Por favor, remova a base de dados existente e tente novamente."

![Screenshot que mostra a página "Instalar componentes necessários". Aparece um erro na parte inferior da página.](./media/how-to-connect-install-custom/error1.png)

Você vê este erro porque uma base de dados chamada *ADSync* já existe na instância SQL do SQL Server que especificou.

Normalmente, vê este erro depois de ter desinstalado o Azure AD Connect.  A base de dados não é eliminada do computador que executa o SQL Server quando desinstala o Azure AD Connect.

Para resolver este problema:

1. Verifique a base de dados ADSync que o Azure AD Connect utilizou antes de ser desinstalado. Certifique-se de que a base de dados já não está a ser utilizada.

2. A fazer o reforço da base de dados.

3. Eliminar a base de dados:
    1. Utilize **o Microsoft SQL Server Management Studio** para ligar à instância SQL. 
    1. Encontre a base de **dados ADSync** e clique com o botão direito.
    1. No menu de contexto, selecione **Delete**.
    1. Selecione **OK** para eliminar a base de dados.

![Screenshot mostrando Microsoft SQL Server Management Studio. É selecionado um D Sync.](./media/how-to-connect-install-custom/error2.png)

Depois de eliminar a base de dados ADSync, selecione **Instalar** para voltar a tentar a instalação.

## <a name="next-steps"></a>Passos seguintes
Após o fim da instalação, assine fora do Windows. Em seguida, inscreva-se novamente antes de utilizar o Gestor de Serviços de Sincronização ou o Editor de Regras de Sincronização.

Agora que instalou o Azure AD Connect, pode [verificar a instalação e atribuir licenças](how-to-connect-post-installation.md).

Para obter mais informações sobre as funcionalidades que ativou durante a instalação, consulte [Prevenir as eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) e [a Azure AD Connect Health](how-to-connect-health-sync.md).

Para obter mais informações sobre outros tópicos comuns, consulte [a sincronização Azure AD Connect: Scheduler](how-to-connect-sync-feature-scheduler.md) e [Integre as suas identidades no local com a Azure AD](whatis-hybrid-identity.md).
