---
title: Perguntas frequentes sobre Azure Active Directory Connect-| Microsoft Docs
description: Este artigo responde a perguntas frequentes sobre Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6b551ee9a0a9c7ef9a8f5ff1bd7452a24dc04b7
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69014184"
---
# <a name="azure-active-directory-connect-faq"></a>FAQ do Azure Active Directory Connect

## <a name="general-installation"></a>Instalação geral

**P: Como posso proteger meu servidor de Azure AD Connect para diminuir a superfície de ataque de segurança?**

A Microsoft recomenda proteger o servidor de Azure AD Connect para diminuir a superfície de ataque de segurança para esse componente crítico de seu ambiente de ti.  Seguir as recomendações abaixo diminuirá os riscos de segurança para sua organização.

* Implantar Azure AD Connect em um servidor ingressado no domínio e restringir o acesso administrativo a administradores de domínio ou a outros grupos de segurança rigidamente controlados

Para saber mais, consulte: 

* [Protegendo grupos de administradores](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Protegendo contas de administrador internas](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Melhoria e manutenção da segurança ao reduzir as superfícies de ataque](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Reduzindo a superfície de ataque de Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**P: A instalação funcionará se o administrador global do Azure Active Directory (Azure AD) tiver a autenticação de dois fatores (2FA) habilitada?**  
A partir das compilações de fevereiro de 2016, há suporte para esse cenário.

**P: Há uma maneira de instalar Azure AD Connect autônomos?**  
Só há suporte para a instalação Azure AD Connect quando você usa o assistente de instalação. Não há suporte para instalações autônomas e silenciosas.

**P: Tenho uma floresta em que um domínio não pode ser contatado. Como fazer instalar Azure AD Connect?**  
A partir das compilações de fevereiro de 2016, há suporte para esse cenário.

**P: O agente de integridade do Azure Active Directory Domain Services (AD DS do Azure) funciona no Server Core?**  
Sim. Depois de instalar o agente, você pode concluir o processo de registro usando o seguinte cmdlet do PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**P: Azure AD Connect dá suporte à sincronização de dois domínios para no Azure AD?**  
Sim, há suporte para esse cenário. Consulte [vários domínios](how-to-connect-install-multiple-domains.md).
 
**P: Você pode ter vários conectores para o mesmo domínio de Active Directory no Azure AD Connect?**  
Não, não há suporte para vários conectores para o mesmo domínio do AD. 

**P: Posso mover o banco de dados Azure AD Connect do banco de dados local para uma instância de SQL Server remota?**    
Sim, as etapas a seguir fornecem diretrizes gerais sobre como fazer isso. No momento, estamos trabalhando em um documento mais detalhado.
1. Faça backup do banco de dados ADSync LocalDB.
A maneira mais simples de fazer isso é usar SQL Server Management Studio instalado no mesmo computador que Azure AD Connect. Conecte-se a *(LocalDb) .\ADSync*e, em seguida, faça backup do banco de dados AdSync.

2. Restaure o banco de dados ADSync para sua instância de SQL Server remota.

3. Instale Azure AD Connect no banco de [dados SQL remoto](how-to-connect-install-existing-database.md)existente.
   O artigo demonstra como migrar para o usando um banco de dados SQL local. Se você estiver migrando para o usando um banco de dados SQL remoto, na etapa 5 do processo, você também deverá inserir uma conta de serviço existente na qual o serviço de sincronização do Windows será executado. Essa conta de serviço do mecanismo de sincronização é descrita aqui:
   
      **Usar uma conta de serviço existente**: Por padrão, o Azure AD Connect usa uma conta de serviço virtual para que os serviços de sincronização usem. Se você usar uma instância de SQL Server remota ou usar um proxy que exija autenticação, use uma conta de serviço gerenciado ou uma conta de serviço no domínio e saiba a senha. Nesses casos, introduza a conta a utilizar. Verifique se os usuários que estão executando a instalação são administradores do sistema no SQL para que as credenciais de logon da conta de serviço possam ser criadas. Para obter mais informações, consulte [contas e permissões do Azure ad Connect](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      Com a compilação mais recente, o aprovisionamento da base de dados pode agora ser realizado fora de banda pelo administrador SQL e, em seguida, instalado pelo administrador do Azure AD Connect com direitos de proprietário da base de dados. Para obter mais informações, consulte [instalar Azure ad Connect usando permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md).

Para simplificar as coisas, recomendamos que os usuários que instalam Azure AD Connect sejam administradores do sistema no SQL. No entanto, com as compilações recentes, agora você pode usar os administradores do SQL delegados, conforme descrito em [instalar Azure ad Connect usando permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md).

**P: Quais são algumas das práticas recomendadas do campo?**  

Este é um documento informativo que apresenta algumas das práticas recomendadas que a engenharia, o suporte e os nossos consultores desenvolveram ao longo dos anos.  Isso é apresentado em uma lista com marcadores que pode ser referenciada rapidamente.  Embora essa lista tente ser abrangente, pode haver mais práticas recomendadas que talvez ainda não tenham sido feitas na lista.

- Se estiver usando o SQL completo, ele deverá permanecer local versus remoto
    - Menos saltos
    - Mais fácil de solucionar problemas
    - Menos complexidade
    - É necessário designar recursos para o SQL e permitir a sobrecarga para Azure AD Connect e o sistema operacional
- Ignorar proxy se possível, se não for possível ignorar o proxy, você precisará garantir que o valor de tempo limite seja maior que 5 minutos.
- Se o proxy for necessário, você deverá adicionar o proxy ao arquivo Machine. config
- Esteja atento aos trabalhos e à manutenção locais do SQL e como eles terão impacto Azure AD Connect-especialmente a reindexação
- Verifique se o DNS pode ser resolvido externamente
- Verifique se as [especificações do servidor](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) são por recomendação se você está usando servidores físicos ou virtuais
- Verifique se você está usando um servidor virtual que os recursos necessários são dedicados
- Verifique se você tem a configuração de disco e disco que atende às práticas recomendadas para SQL Server
- Instalar e configurar Azure AD Connect Health para monitoramento
- Use o limite de exclusão interno do Azure AD Connect.
- Revise cuidadosamente as atualizações de versão a serem preparadas para todas as alterações e novos atributos que possam ser adicionados
- Fazer backup de tudo
    - Chaves de backup
    - Regras de sincronização de backup
    - Configuração do servidor de backup
    - Fazer backup do banco de dados SQL
- Verifique se não há agentes de backup de terceiros que estejam fazendo backup do SQL sem o gravador VSS do SQL (comum em servidores virtuais com instantâneos de terceiros)
- Limitar a quantidade de regras de sincronização personalizadas que são usadas à medida que adicionam complexidade
- Tratar servidores Azure AD Connect como servidores de camada 0
- Seja Leery de modificar regras de sincronização de nuvem sem grande compreensão do impacto e dos drivers de negócios certos
- Verifique se as portas da URL e do firewall corretas estão abertas para suporte de Azure AD Connect e Azure AD Connect Health
- Aproveite o atributo de nuvem filtrado para solucionar problemas e impedir objetos fantasmas
- Com o servidor de preparo, verifique se você está usando o Azure AD Connect o Documentador de configuração quanto à consistência entre os servidores
- Os servidores de preparo devem estar em data centers separados (locais físicos
- Os servidores de preparo não devem ser uma solução de alta disponibilidade, mas você pode ter vários servidores de preparo
- A introdução de um servidor de preparo de "retardo" pode mitigar algum tempo de inatividade em potencial em caso de erro
- Testar e validar todas as atualizações no servidor de preparo primeiro
- Sempre valide as exportações antes de passar para o serverLeverage de preparo para o servidor de preparo para importações completas e Sincronizações completas para reduzir o impacto nos negócios
- Mantenha a consistência da versão entre Azure AD Connect servidores o máximo possível 

**P: Posso permitir que Azure AD Connect crie a conta do conector do Azure AD no computador do grupo de trabalho?**
Não.  Para permitir que Azure AD Connect crie automaticamente a conta do conector do Azure AD, o computador deve estar ingressado no domínio.  

## <a name="network"></a>Rede
**P: Tenho um firewall, um dispositivo de rede ou algo que limita o tempo que as conexões podem permanecer abertas na minha rede. Qual deve ser o limite de tempo limite do lado do cliente quando eu usar Azure AD Connect?**  
Todos os softwares de rede, dispositivos físicos ou qualquer outra coisa que limita o tempo máximo que as conexões podem permanecer abertas devem usar um limite de pelo menos cinco minutos (300 segundos) para a conectividade entre o servidor em que o cliente do Azure AD Connect está instalado e Azure Active Directory. Essa recomendação também se aplica a todas as ferramentas de Sincronização de Identidades do Microsoft lançadas anteriormente.

**P: Há suporte para SLDs (domínios de rótulo único)?**  
Embora seja altamente recomendável em relação a essa configuração de rede ([consulte o artigo](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), o uso de Azure ad Connect sincronização com um único domínio de rótulo tem suporte, desde que a configuração de rede para o domínio de nível único esteja funcionando corretamente.

**P: Há suporte para florestas com domínios não contíguos do AD?**  
Não, Azure AD Connect não dá suporte a florestas locais que contêm namespaces não contíguos.

**P: Há suporte para nomes de NetBIOS "pontilhados"?**  
Não, Azure AD Connect não dá suporte a florestas ou domínios locais em que o nome NetBIOS contém um ponto (.).

**P: Há suporte para o ambiente puro de IPv6?**  
Não, Azure AD Connect não dá suporte a um ambiente IPv6 puro.

**P: ter um ambiente de várias florestas e a rede entre as duas florestas está usando NAT (conversão de endereços de rede). Está usando Azure AD Connect entre essas duas florestas com suporte?**</br>
Não, não há suporte para o uso de Azure AD Connect sobre NAT. 

## <a name="federation"></a>Federação
**P: O que devo fazer se eu receber um email que me peça para renovar meu certificado do Office 365?**  
Para obter orientação sobre como renovar o certificado, consulte renovar [certificados](how-to-connect-fed-o365-certs.md).

**P: Tenho "atualizar automaticamente a terceira parte confiável" definida para a terceira parte confiável do Office 365. É necessário realizar qualquer ação quando meu certificado de autenticação de tokens passa automaticamente?**  
Use as diretrizes descritas no artigo renovar [certificados](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Ambiente
**P: Há suporte para renomear o servidor após a instalação do Azure AD Connect?**  
Não. A alteração do nome do servidor renderiza o mecanismo de sincronização não é possível conectar-se à instância do banco de dados SQL e o serviço não pode ser iniciado.

**P: Há suporte para regras de sincronização do NGC (criptografia de próxima geração) em uma máquina habilitada para FIPS?**  
Não.  Eles não têm suporte.

**P. Se eu desabilitar um dispositivo sincronizado (por exemplo: HAADJ) na portal do Azure, por que ele é habilitado novamente?**<br>
Dispositivos sincronizados podem ser criados ou mastermente no local. Se um dispositivo sincronizado estiver habilitado localmente, ele poderá ser habilitado novamente no portal do Azure mesmo que tenha sido desabilitado anteriormente por um administrador. Para desabilitar um dispositivo sincronizado, use o Active Directory local para desabilitar a conta do computador.

**P. Se eu bloquear a entrada do usuário no portal do Office 365 ou do AD do Azure para usuários sincronizados, por que ele será desbloqueado após entrar novamente?**<br>
Os usuários sincronizados podem ser criados ou mastermente no local. Se a conta estiver habilitada localmente, ela poderá desbloquear o bloco de entrada colocado pelo administrador.

## <a name="identity-data"></a>Dados de identidade
**P: Por que não o atributo userPrincipalName (UPN) no Azure AD corresponde ao UPN local?**  
Para obter informações, consulte estes artigos:

* [Nomes de acessados no Office 365, no Azure ou no Intune não correspondem ao UPN local ou à ID de logon alternativa](https://support.microsoft.com/kb/2523192)
* [As alterações não são sincronizadas pela ferramenta de sincronização Azure Active Directory depois que você altera o UPN de uma conta de usuário para usar um domínio federado diferente](https://support.microsoft.com/kb/2669550)

Você também pode configurar o Azure AD para permitir que o mecanismo de sincronização atualize o UPN, conforme descrito em [Azure ad Connect recursos do serviço de sincronização](how-to-connect-syncservice-features.md).

**P: Há suporte para a correspondência flexível de um grupo ou objeto de contato do Azure AD local com um grupo ou objeto de contato do Azure AD existente?**  
Sim, essa correspondência flexível se baseia no proxyAddress. Não há suporte para a correspondência flexível para grupos que não são habilitados para email.

**P: É possível definir manualmente o atributo imutávelid em um grupo ou objeto de contato do Azure AD existente para fazer a correspondência física com um grupo ou objeto de contato do Azure AD local?**  
Não, definir manualmente o atributo imutávelid em um grupo ou objeto de contato existente do Azure AD para a correspondência fixa não tem suporte no momento.

## <a name="custom-configuration"></a>Configuração personalizada
**P: Onde estão os cmdlets do PowerShell para Azure AD Connect documentados?**  
Com exceção dos cmdlets documentados neste site, outros cmdlets do PowerShell encontrados no Azure AD Connect não têm suporte para uso do cliente.

**P: Posso usar a opção "servidor de exportação/importação de servidor" encontrada em Synchronization Service Manager para mover a configuração entre servidores?**  
Não. Essa opção não recupera todas as definições de configuração e não deve ser usada. Em vez disso, use o assistente para criar a configuração de base no segundo servidor e use o editor de regra de sincronização para gerar scripts do PowerShell para mover qualquer regra personalizada entre servidores. Para obter mais informações, consulte [migração Swing](how-to-upgrade-previous-version.md#swing-migration).

**P: As senhas podem ser armazenadas em cache para a página de entrada do Azure e esse cache pode ser impedido porque contém um elemento de entrada de senha com o atributo *AutoComplete = "false"* ?**  
No momento, não há suporte para modificar os atributos HTML do campo de **senha** , incluindo a marca de preenchimento automático. No momento, estamos trabalhando em um recurso que permite o JavaScript personalizado, que permite adicionar qualquer atributo ao campo de **senha** .

**P: A página de entrada do Azure exibe os nomes de usuários que se conectaram anteriormente com êxito. Esse comportamento pode ser desativado?**  
No momento, não há suporte para modificar os atributos HTML do campo de entrada de **senha** , incluindo a marca de preenchimento automático. No momento, estamos trabalhando em um recurso que permite o JavaScript personalizado, que permite adicionar qualquer atributo ao campo de **senha** .

**P: Há uma maneira de evitar sessões simultâneas?**  
Não.

## <a name="auto-upgrade"></a>Atualização automática

**P: Quais são as vantagens e consequências de usar a atualização automática?**  
Estamos aconselhando todos os clientes a habilitarem a atualização automática para a instalação Azure AD Connect. O benefício é que você sempre recebe os patches mais recentes, incluindo atualizações de segurança para vulnerabilidades encontradas no Azure AD Connect. O processo de atualização é simples e ocorre automaticamente assim que uma nova versão está disponível. Muitos milhares de Azure AD Connect clientes usam a atualização automática com cada nova versão.

O processo de atualização automática sempre estabelece primeiro se uma instalação está qualificada para atualização automática. Se ele estiver qualificado, a atualização será executada e testada. O processo também inclui a procura de alterações personalizadas em regras e fatores ambientais específicos. Se os testes mostrarem que uma atualização não foi bem-sucedida, a versão anterior será restaurada automaticamente.

Dependendo do tamanho do ambiente, o processo pode levar algumas horas. Enquanto a atualização está em andamento, nenhuma sincronização entre o Windows Server Active Directory e o Azure AD acontece.

**P: Recebi um email dizendo que minha atualização automática não funciona mais e preciso instalar uma nova versão. Por que preciso fazer isso?**  
No ano passado, lançamos uma versão do Azure AD Connect que, em determinadas circunstâncias, pode ter desabilitado o recurso de atualização automática em seu servidor. Corrigimos o problema na versão Azure AD Connect 1.1.750.0. Se tiver sido afetado pelo problema, você poderá atenuá-lo executando um script do PowerShell para repará-lo ou atualizar manualmente para a versão mais recente do Azure AD Connect. 

Para executar o script do PowerShell, [Baixe o script](https://aka.ms/repairaadconnect) e execute-o em seu servidor de Azure ad Connect em uma janela administrativa do PowerShell. Para saber como executar o script, [Veja este breve vídeo](https://aka.ms/repairaadcau).

Para atualizar manualmente, você deve baixar e executar a versão mais recente do arquivo AADConnect. msi.
 
-  Se sua versão atual for mais antiga do que 1.1.750.0, [Baixe e atualize para a versão mais recente](https://www.microsoft.com/download/details.aspx?id=47594).
- Se sua versão de Azure AD Connect for 1.1.750.0 ou posterior, nenhuma ação adicional será necessária. Você já está usando a versão que contém a correção de atualização automática. 

**P: Recebi um email informando-me para atualizar para a versão mais recente para reabilitar a atualização automática. Estou usando a versão 1.1.654.0. É necessário atualizar?**  
Sim, você precisa atualizar para a versão 1.1.750.0 ou posterior para reabilitar a atualização automática. [Baixe e atualize para a versão mais recente](https://www.microsoft.com/download/details.aspx?id=47594).

**P: Recebi um email informando-me para atualizar para a versão mais recente para reabilitar a atualização automática. Se eu tiver usado o PowerShell para habilitar a atualização automática, ainda precisarei instalar a versão mais recente?**  
Sim, você ainda precisa atualizar para a versão 1.1.750.0 ou posterior. Habilitar o serviço de atualização automática com o PowerShell não atenua o problema de atualização automática encontrado em versões anteriores ao 1.1.750.0.

**P: Quero atualizar para uma versão mais recente, mas não tenho certeza de quem instalou o Azure AD Connect e não temos o nome de usuário e a senha. Precisamos disso?**
Você não precisa saber o nome de usuário e a senha que foram usados inicialmente para atualizar Azure AD Connect. Use qualquer conta do Azure AD que tenha a função de administrador global.

**P: Como posso encontrar qual versão do Azure AD Connect estou usando?**  
Para verificar qual versão do Azure ad Connect está instalada no servidor, vá para o painel de controle e procure a versão instalada do Microsoft Azure ad conectar selecionando **programas** > **programas e recursos**, conforme mostrado aqui:

![Azure AD Connect versão no painel de controle](./media/reference-connect-faq/faq1.png)

**P: Como fazer atualizar para a versão mais recente do Azure AD Connect?**  
Para saber como atualizar para a versão mais recente, consulte [Azure ad Connect: Upgrade from a previous version to the latest](how-to-upgrade-previous-version.md) (Sincronização do Azure AD Connect: Atualizar de uma versão anterior para a mais recente). 

**P: Já atualizamos para a versão mais recente do Azure AD Connect no ano passado. Precisamos atualizar novamente?**  
A equipe de Azure AD Connect faz atualizações frequentes no serviço. Para se beneficiar de correções de bugs e atualizações de segurança, bem como novos recursos, é importante manter seu servidor atualizado com a versão mais recente. Se você habilitar a atualização automática, a versão do software será atualizada automaticamente. Para localizar o histórico de lançamento de versão do Azure ad Connect [, consulte Azure ad Connect: Version release history](reference-connect-version-history.md) (Azure AD Connect: Histórico de versões).

**P: Quanto tempo leva para realizar a atualização e qual é o impacto nos meus usuários?**  
O tempo necessário para a atualização depende do tamanho do locatário. Para organizações maiores, pode ser melhor executar a atualização na noite ou no fim de semana. Durante a atualização, nenhuma atividade de sincronização ocorre.

**P: Acredito que eu tenha atualizado para Azure AD Connect, mas o portal do Office ainda menciona o DirSync. Por que isso?**  
A equipe do Office está trabalhando para obter as atualizações do portal do Office para refletir o nome do produto atual. Ele não reflete qual ferramenta de sincronização você está usando.

**P: Meu status de atualização automática diz "suspenso". Por que ele está suspenso? Devo habilitá-lo?**  
Um bug foi introduzido em uma versão anterior que, em determinadas circunstâncias, deixa o status de atualização automática definido como "suspenso". Habilitar manualmente é tecnicamente possível, mas exigiria várias etapas complexas. A melhor coisa que você pode fazer é instalar a versão mais recente do Azure AD Connect.

**P: Minha empresa tem requisitos estritos de gerenciamento de alterações e quero controlar quando ele é enviado por push. Posso controlar quando a atualização automática é iniciada?**  
Não, atualmente não existe um recurso desse tipo. O recurso está sendo avaliado para uma versão futura.

**P: Receberei um email se a atualização automática falhar? Como saber que foi bem-sucedida?**  
Você não será notificado do resultado da atualização. O recurso está sendo avaliado para uma versão futura.

**P: Você publica uma linha do tempo para quando planeja enviar atualizações automáticas?**  
A atualização automática é a primeira etapa no processo de liberação de uma versão mais recente. Sempre que houver uma nova versão, as atualizações serão enviadas automaticamente. As versões mais recentes do Azure AD Connect são anunciadas previamente no [roteiro do Azure ad](../fundamentals/whats-new.md).

**P: A atualização automática também atualiza Azure AD Connect Health?**  
Sim, a atualização automática também atualiza Azure AD Connect Health.

**P: Você também atualiza automaticamente Azure AD Connect servidores no modo de preparo?**  
Sim, você pode atualizar automaticamente um servidor de Azure AD Connect que está no modo de preparo.

**P: Se a atualização automática falhar e meu servidor de Azure AD Connect não for iniciado, o que devo fazer?**  
Em casos raros, o serviço de Azure AD Connect não é iniciado depois que você executa a atualização. Nesses casos, a reinicialização do servidor geralmente corrige o problema. Se o serviço de Azure AD Connect ainda não for iniciado, abra um tíquete de suporte. Para obter mais informações, consulte [criar uma solicitação de serviço para contatar o suporte do Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**P: Não tenho certeza de quais são os riscos quando atualizo para uma versão mais recente do Azure AD Connect. Você pode ligar para mim para me ajudar com a atualização?**  
Se precisar de ajuda para atualizar para uma versão mais recente do Azure AD Connect, abra um tíquete de suporte em [criar uma solicitação de serviço para entrar em contato com o suporte do Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Resolução de problemas
**P: Como posso obter ajuda com Azure AD Connect?**

[Pesquisar na base de dados de conhecimento Microsoft (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Pesquise as soluções técnicas na base de conhecimento para problemas comuns de conserto para Azure AD Connect.

[Fóruns de Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Pesquise perguntas e respostas técnicas ou faça suas próprias perguntas acessando [a Comunidade do Azure ad](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Obter suporte para o Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
