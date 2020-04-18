---
title: 'Azure AD Connect: Versão versão lançar histórico / Microsoft Docs'
description: Este artigo lista todos os lançamentos de Azure AD Connect e Azure AD Sync
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/17/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 815d3afe68003f56a5748584b322b731ef5a3dc7
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639638"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: histórico de versões
A equipa azure Ative Directory (Azure AD) atualiza regularmente o Azure AD Connect com novas funcionalidades e funcionalidades. Nem todas as adições são aplicáveis a todos os públicos.

Este artigo foi concebido para o ajudar a acompanhar as versões que foram lançadas e a perceber quais são as alterações na versão mais recente.

Esta tabela é uma lista de tópicos relacionados:

Tópico |  Detalhes
--------- | --------- |
Passos para atualizar a partir de Azure AD Connect | Diferentes métodos para [atualizar de uma versão anterior para o mais recente](how-to-upgrade-previous-version.md) lançamento do Azure AD Connect.
Permissões obrigatórias | Para obter permissões necessárias para aplicar uma atualização, consulte [contas e permissões](reference-connect-accounts-permissions.md#upgrade).
Transferência| [Baixar Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Lançar uma nova versão do Azure AD Connect é um processo que requer vários passos de controlo de qualidade para garantir a funcionalidade de operação do serviço, e enquanto passamos por este processo, o número de versão de uma nova versão, bem como o estado de lançamento, serão atualizados para refletir o estado mais recente.
Enquanto passamos por este processo, o número de versão do lançamento será mostrado com um "X" na posição de número de lançamento menor, como em "1.3.X.0" - isto indica que as notas de lançamento neste documento são válidas para todas as versões a partir de "1.3". Assim que finalizarmos o processo de lançamento, o número da versão de lançamento será atualizado para a versão mais recente e o estado de lançamento será atualizado para "Released for download and auto upgrade".
Nem todos os lançamentos do Azure AD Connect serão disponibilizados para atualização automática. O estado de lançamento indicará se um lançamento é disponibilizado para upgrade automático ou apenas para download. Se a atualização automática for ativada no seu servidor Azure AD Connect, então esse servidor irá automaticamente atualizar para a versão mais recente do Azure AD Connect que é lançado para atualização automática. Note que nem todas as configurações do Azure AD Connect são elegíveis para upgrade automático. Por favor, siga este link para ler mais sobre [atualização automática](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> A partir de 1 de novembro de 2020, começaremos a implementar um processo de depreciação pelo qual versões do Azure AD Connect que foram lançadas há mais de 18 meses serão depreciadas. Nessa altura iniciaremos este processo deprecendo todos os lançamentos do Azure AD Connect com a versão 1.3.20.0 (que foi lançada em 4/24/2019) e mais antiga, e vamos proceder à avaliação da depreciação de versões mais antigas do Azure AD Connect sempre que uma nova versão for lançada.
>
> Tem de se certificar de que está a executar uma versão recente do Azure AD Connect para receber uma experiência de suporte ideal. 
>
>Se executar uma versão depreciada do Azure AD Connect poderá não ter as mais recentes correções de segurança, melhorias de desempenho, ferramentas de resolução de problemas e diagnóstico e melhorias de serviço, e se necessitar de apoio, poderemos não ser capazes de lhe fornecer o nível de serviço que a sua organização necessita.
>
>Se tiver ativado o Azure AD Connect para sincronização, em breve começará automaticamente a receber notificações de Saúde que o avisam sobre as próximas depreciações quando estiver a executar uma das versões mais antigas.
>
>Consulte [este artigo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version) para saber mais sobre como atualizar o Azure AD Connect para a versão mais recente.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Estado de lançamento
04/09/2020: Lançado para download

### <a name="fixed-issues"></a>Problemas corrigidos
Esta construção de hotfix corrige um problema com a build 1.5.18.0 se tiver a função de filtragem de grupo ativada e utilizar mS-DS-Consistência Como âncora de origem.

> [!IMPORTANT]
> Se utilizar o mS-DS-ConsistênciaComo âncora de origem, e clonou a regra de sincronização **In from AD - Group Join** e planeia atualizar, complete os seguintes passos como parte da atualização:
> 1. Durante a atualização, desfaça a opção Iniciar o processo de **sincronização quando a configuração estiver concluída**.
> 2. Editar a regra de sincronização de agregação clonada e adicionar as duas transformações seguintes:
>     - Definir fluxo `objectGUID` `sourceAnchorBinary`direto para .
>     - Definir fluxo `ConvertToBase64([objectGUID])` `sourceAnchor`de expressão para .     
> 3. Ativar o `Set-ADSyncScheduler -SyncCycleEnabled $true`programador utilizando .


## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Estado de lançamento
04/02/2020: Lançado para download

### <a name="functional-changes-adsyncautoupgrade"></a>Alterações funcionais ADSyncAutoUpgrade 

- Suporte adicional para a função mS-DS-Consistência-Guia para objetos de grupo. Isto permite-lhe mover grupos entre florestas ou reconectar grupos em AD para Azure AD onde o objectID do grupo AD mudou, por exemplo, quando um servidor AD é reconstruído após uma calamidade. Para mais informações consulte [Grupos de deslocação entre florestas](how-to-connect-migrate-groups.md).
- O atributo mS-DS-Consistência-ConsistênciaÉ automaticamente definido em todos os grupos sincronizados e não tem de fazer nada para ativar esta funcionalidade. 
- Removeu o Get-ADSyncRunProfile porque já não está a ser utilizado. 
- Alterou o aviso que vê ao tentar utilizar uma conta de Administrador da Enterprise ou Dodomínio para a conta de conector AD DS para fornecer mais contexto. 
- Adicione um novo cmdlet para remover objetos do espaço do conector, a antiga ferramenta CSDelete.exe é removida, e é substituída pelo novo cmdlet Remove-ADSyncCSObject. O cmdlet Remove-ADSyncCSObject tem um CsObject como entrada. Este objeto pode ser recuperado utilizando o cmdlet Get-ADSyncCSObject.

>[!NOTE]
>A antiga ferramenta CSDelete.exe foi removida e substituída pelo novo cmdlet Remove-ADSyncCSObject 

### <a name="fixed-issues"></a>Problemas corrigidos

- Fixou um bug no seletor de reprodução de reprodução de reprodução da floresta/OU do grupo na reexecução do assistente Azure AD Connect depois de desativar a funcionalidade. 
- Introduziu uma nova página de erro que será exibida se faltarem os valores de registo dCOM necessários com um novo link de ajuda. A informação também está escrita para registar ficheiros. 
- Corrigiu um problema com a criação da conta de sincronização do Diretório Ativo Azure onde a habilitação de extensões de diretório ou PHS pode falhar porque a conta não se propagou em todas as réplicas de serviço antes de tentar a utilização. 
- Fixou um bug na utilidade de compressão de erros de sincronização que não manuseava corretamente os caracteres de substituição. 
- Fixou um bug na atualização automática que deixou o servidor no estado suspenso do programador. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Estado de lançamento
12/9/2019: Lançamento para download. Não disponível através de auto-upgrade.
### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
- Atualizámos o Password Hash Sync para os Serviços de Domínio AD Azure para prestar contas adequadamente ao estofamento em hashes Kerberos.  Isto proporcionará uma melhoria de desempenho durante a sincronização de palavras-passe de AAD para Serviços de Domínio AD Azure.
- Adicionámos suporte para sessões fiáveis entre o agente de autenticação e o autocarro de serviço.
- Esta versão aplica o TLS 1.2 para comunicação entre o agente de autenticação e os serviços na nuvem.
- Adicionámos uma cache DNS para ligações websocket entre o agente de autenticação e os serviços de nuvem.
- Adicionámos a capacidade de direcionar agentes específicos da nuvem para testar a conectividade do agente.

### <a name="fixed-issues"></a>Problemas corrigidos
- O lançamento 1.4.18.0 tinha um bug onde o cmdlet PowerShell para DSSO estava a usar as credenciais de janelas de login em vez das credenciais de administração fornecidas durante a execução do PS. Como resultado do qual não foi possível ativar o DSSO em múltiplas florestas através da interface de utilizador AADConnect. 
- Foi feita uma correção para permitir o DSSO simultaneamente em toda a floresta através da interface de utilizador AADConnect

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Estado de lançamento
11/08/2019: Lançado para download. Não disponível através de auto-upgrade.

>[!IMPORTANT]
>Devido a uma alteração interna de esquemas nesta versão do Azure AD Connect, se gerir as definições de configuração de configuração de relações fiduciárias AD FS utilizando o MSOnline PowerShell, então deve atualizar o módulo MSOnline PowerShell para a versão 1.1.183.57 ou superior.

### <a name="fixed-issues"></a>Problemas corrigidos

Esta versão corrige um problema com dispositivos aderes híbridos azure existentes. Esta versão contém uma nova regra de sincronização do dispositivo que corrige este problema.
Note que esta alteração de regra pode causar a eliminação de dispositivos obsoletos da AD Azure. Isto não é motivo de preocupação, uma vez que estes objetos do dispositivo não são utilizados pela Azure AD durante a autorização de acesso condicional. Para alguns clientes, o número de dispositivos que serão eliminados através desta alteração de regra pode exceder o limiar de eliminação. Se vir a eliminação de objetos do dispositivo em Azure AD que exceda o Limiar de Eliminação de Exportação, é aconselhável permitir que as supressões passem. [Como permitir que os apagues fluam quando excedem o limiar de eliminação](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Estado de lançamento
28/09/2019: Lançado para atualização automática para selecionar inquilinos. Não disponível para download.

Esta versão corrige um bug onde alguns servidores que foram automaticamente atualizados de uma versão anterior para 1.4.18.0 e experimentaram problemas com redefinição de palavra-passe self-service (SSPR) e Password Writeback.

### <a name="fixed-issues"></a>Problemas corrigidos

Em certas circunstâncias, os servidores que foram automaticamente atualizados para a versão 1.4.18.0 não reativaram o reset da palavra-passe self-service e a Password Writeback após a atualização ter sido concluída. Esta versão de atualização automática corrige esse problema e reativa o reset da palavra-passe self-service e a Password Writeback.

Corrigimos um bug na utilidade de compressão de erros de sincronização que não estava a manusear corretamente os caracteres de substituição.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Estamos a investigar um incidente em que alguns clientes estão a ter um problema com os dispositivos aderes à AD Hybrid Azure existentes após a atualização para esta versão do Azure AD Connect. Aconselhamos os clientes que implementaram a Hybrid Azure AD a juntarem-se para adiar a atualização para esta versão até que a causa principal destas questões seja totalmente compreendida e atenuada. Mais informações serão fornecidas o mais rapidamente possível.

>[!IMPORTANT]
>Com esta versão do Azure AD Connect, alguns clientes podem ver alguns ou todos os seus dispositivos Windows desaparecerem do Azure AD. Isto não é motivo de preocupação, uma vez que estas identidades do dispositivo não são utilizadas pela Azure AD durante a autorização de acesso condicional. Para mais informações consulte [Understanding Azure AD Connect 1.4.xx.x desaparecimento](reference-connect-device-disappearance.md) do dispositivo


### <a name="release-status"></a>Estado de lançamento
25/09/2019: Lançado apenas para atualização automática.

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
- A nova ferramenta de resolução de problemas ajuda a resolver cenários de "utilizador não sincronizado", "grupo não sincronizando" ou "membro do grupo não sincronizando".
- Adicione suporte para nuvens nacionais no script de resolução de problemas AAD Connect 
- Os clientes devem ser informados de que os pontos finais de WMI deprecatados para MIIS_Service foram agora removidos. Quaisquer operações de WMI devem agora ser feitas através de cmdlets PS.
- Melhoria da segurança através da reposição da delegação restrita sobre o objeto AZUREADSSOACC
- Ao adicionar/editar uma regra de sincronização, se houver atributos usados na regra que esteja no esquema do conector mas não adicionado ao conector, os atributos são automaticamente adicionados ao conector. O mesmo acontece com o tipo de objeto que a regra afeta. Se algo for adicionado ao conector, o conector será marcado para a importação total no próximo ciclo de sincronização.
- A utilização de um administrador da Enterprise ou do Domínio, uma vez que a conta de conector já não é suportada em novas implementações de ligação Azure AD. As atuais implementações do AAD Connect utilizando um administrador da Enterprise ou do Domínio, uma vez que a conta de conector não será afetada por esta versão.
- No Gestor de Sincronização é executado um sincronização completo na criação de regras/editar/eliminação. Aparecerá um popup em qualquer alteração de regra que notifique o utilizador se a importação completa ou a sincronização completa for em funcionamento.
- Dados de mitigação adicionados para erros de senha a "conectores > propriedades > conectividade"
- Adicione um aviso de depreciação para o gestor de serviço sincronizado na página de propriedades do conector. Este aviso avisa o utilizador de que as alterações devem ser efetuadas através do assistente De Ligação AD Azure.
- Adicione um novo erro para problemas com a política de senha de um utilizador.
- Evite a configuração errada da filtragem do grupo por filtros de domínio e U. A filtragem em grupo mostrará um erro quando o domínio/OU do grupo introduzido já estiver filtrado e impedir o utilizador de avançar até que o problema seja resolvido.
- Os utilizadores já não podem criar um conector para Serviços de Domínio de Diretório Ativo ou Diretório Ativo Windows Azure no UI do Gestor de Serviços de Sincronização.
- Acessibilidade fixa dos controlos de UI personalizados no Gestor de Serviços de Sincronização.
- Habilitado seis tarefas de gestão da federação para todos os métodos de inscrição no Azure AD Connect.  (Anteriormente, apenas a tarefa "Update AD FS TLS/SSL" estava disponível para todos os sign-ins.)
- Acrescentou um aviso ao alterar o método de entrada da federação para PHS ou PTA de que todos os domínios e utilizadores da AD Azure serão convertidos para autenticação gerida.
- Os certificados de assinatura de token removidos da tarefa "Reset Azure AD e AD FS trust" e adicionaram uma sub-tarefa separada para atualizar estes certificados.
- Acrescentou uma nova tarefa de gestão da federação chamada "Gerir certificados" que tem sub-tarefas para atualizar os certificados de tLS ou de assinatura de token para a exploração aD FS.
- Adicionou uma nova sub-tarefa de gestão da federação chamada "Especificar servidor primário" que permite aos administradores especificar um novo servidor primário para a fazenda AD FS.
- Adicionou uma nova tarefa de gestão da federação chamada "Gerir servidores" que tem sub-tarefas para implementar um servidor AD FS, implementar um servidor proxy de aplicação web e especificar o servidor primário.
- Acrescentou uma nova tarefa de gestão da federação chamada "Ver configuração da federação" que apresenta as atuais definições de AD FS.  (Devido a esta adição, as definições de AD FS foram removidas da página "Reveja a sua solução".)

### <a name="fixed-issues"></a>Problemas corrigidos
- Problema de erro de sincronização resolvido para o cenário em que um objeto de utilizador que assumiu o seu objeto de contacto correspondente tem uma autorreferência (por exemplo, o utilizador é o seu próprio gestor).
- Ajude os popups a mostrarem-se agora no foco do teclado.
- Para a atualização automática, se alguma aplicação conflituosa estiver a funcionar a partir de 6 horas, mate-a e continue com a atualização.
- Limite o número de atributos que um cliente pode selecionar a 100 por objeto ao selecionar extensões de diretório. Isto evitará que o erro ocorra durante a exportação, uma vez que o Azure tem um máximo de 100 atributos de extensão por objeto.
- Fixou um bug para tornar o script de Conectividade AD mais robusto
- Fixou um bug para tornar o AADConnect instalado numa máquina utilizando um serviço WCF de Tubos Nomeados existente.
- Melhores diagnósticos e resolução de problemas em torno de políticas de grupo que não permitem que o serviço ADSync comece quando inicialmente instalado.
- Fixou um bug onde o nome do visualização de um computador Windows foi escrito incorretamente.
- Corrija um bug em que o tipo OS para um computador Windows foi escrito incorretamente.
- Fixou um bug onde computadores não Windows 10 sincronizavam inesperadamente. Note que o efeito desta alteração é que os computadores não Windows-10 que foram previamente sincronizados serão agora eliminados. Isto não afeta nenhuma funcionalidade, uma vez que o sincronizado dos computadores Windows é utilizado apenas para a adesão ao domínio Hybrid Azure AD, que funciona apenas para dispositivos Windows-10.
- Adicionevários cmdlets novos (internos) ao módulo ADSync PowerShell.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Existe um problema conhecido com a atualização do Azure AD Connect de uma versão anterior para 1.3.21.0 onde o portal O365 não reflete a versão atualizada, apesar de o Azure AD Connect ter atualizado com sucesso.
>
> Para resolver isto, é necessário importar o módulo`Set-ADSyncDirSyncConfiguration` **AdSync** e, em seguida, executar o cmdlet PowerShell no servidor Azure AD Connect.  Pode utilizar os seguintes passos:
>
>1. Abra a PowerShell em modo administator.
>2. Execute `Import-Module "ADSync"`.
>3. Execute `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>Estado de lançamento 

05/14/2019: Lançado para download

### <a name="fixed-issues"></a>Problemas corrigidos 

- Fixou uma elevação da vulnerabilidade de privilégio que existe no Microsoft Azure Ative Directory Connect build 1.3.20.0.  Esta vulnerabilidade, sob determinadas condições, pode permitir que um intruso execute dois cmdlets PowerShell no contexto de uma conta privilegiada, e realizar ações privilegiadas.  Esta atualização de segurança aborda o problema desativando estes cmdlets. Para mais informações consulte a [atualização de segurança](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Estado de lançamento 

04/24/2019: Lançado para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 

- Adicione suporte para a atualização de domínio 
- Funcionalidade de Pastas Públicas de Troca de Correio saem ga 
- Melhorar o manuseamento de erros do assistente para falhas de serviço 
- Link de aviso adicionado no Gestor de Serviços de Sincronização UI na página de propriedades do conector. 
- A funcionalidade De Reescrita de Grupos Unificados é agora GA 
- Mensagem de erro SSPR melhorada quando o DC está a perder um controlo LDAP 
- Diagnósticos adicionados para erros de registo de DCOM durante a instalação  
- Rastreio melhorado dos erros do PHS RPC 
- Permitir credenciais de EA de um domínio infantil 
- Permitir que o nome da base de dados seja introduzido durante a instalação (nome predefinido ADSync)
- Upgrade para ADAL 3.19.8 para pegar uma correção WS-Trust para Ping e adicionar suporte para novos casos Azure 
- Modificar regras de sincronização de grupo para fluir samAccountName, DomainNetbios e DomainFQDN para cloud - necessário para sinistros 
- Manipulação modificada da regra de sincronização padrão - leia mais [aqui](how-to-connect-fix-default-rules.md).
- Adicionei um novo agente a funcionar como um serviço de janelas. Este agente, chamado "Agente Administrador", permite diagnósticos remotos mais profundos do servidor Azure AD Connect para ajudar os Engenheiros da Microsoft a resolver problemas quando abre um caso de suporte. Este agente não está instalado e ativado por predefinição.  Para obter mais informações sobre como instalar e ativar o agente ver [O que é o Agente de Administração Azure AD Connect?](whatis-aadc-admin-agent.md) 
- Atualizado o Contrato de Licença de Utilizador Final (EULA) 
- Adicionou suporte de atualização automática para implementações que usam AD FS como o seu tipo de login.  Isto também removeu a exigência de atualização do AD FS Azure AD Trust Trust como parte do processo de atualização. 
- Adicionou uma tarefa de gestão de confiança da Azure AD que fornece duas opções: analisar/atualizar a confiança e repor a confiança. 
- Alterou o comportamento de confiança do AD FS Azure AD Trusting Party para que utilize sempre o interruptor -SupportMultipleDomain (inclui atualizações de trust e azure AD). 
- Alterou a instalação de um novo comportamento agrícola AD FS de modo a que este necessite de um certificado .pfx removendo a opção de usar um certificado pré-instalado.
- Atualizou o novo fluxo de trabalho da quinta AD FS de modo a que só permita a implementação de 1 AD FS e 1 servidor WAP.  Todos os servidores adicionais serão feitos após a instalação inicial. 

### <a name="fixed-issues"></a>Problemas corrigidos 

- Corrija a lógica de religação do SQL para o serviço ADSync 
- Fixe para permitir a instalação limpa utilizando um SQL AOA DB vazio 
- Corrigir o roteiro de permissões PS para refinar permissões GWB 
- Corrigir erros VSS com O DB Local  
- Fixe a mensagem de erro enganosa quando o tipo de objeto não estiver no âmbito 
- Corrigiu um problema em que a instalação de PowerShell Azure AD num servidor poderia potencialmente causar um conflito de montagem com o Azure AD Connect. 
- Bug PHS fixo no Servidor de Preparação quando as credenciais de conector são atualizadas no UI do Gestor de Serviços de Sincronização. 
- Corrigiu algumas fugas de memória 
- Correções de autoupgrade diversos 
- Correções diversas para exportação e processamento de importações não confirmadas 
- Fixou um bug com o manuseamento de um backslash no domínio e na filtragem da U 
- Corrigiu um problema em que o serviço ADSync demora mais de 2 minutos a parar e causa um problema no momento da atualização. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Estado de lançamento

12/18/2018: Lançado para download

### <a name="fixed-issues"></a>Problemas corrigidos

Esta construção atualiza os conectores não standard (por exemplo, Conector Genérico LDAP e Conector Genérico SQL) enviados com Ligação AD Azure. Para mais informações sobre os conectores aplicáveis, consulte a versão 1.1.911.0 no Histórico de Lançamento da [Versão Connector](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Estado de lançamento
12/11/2018: Lançado para download

### <a name="fixed-issues"></a>Problemas corrigidos
Esta construção de hotfix permite ao utilizador selecionar um domínio de destino, dentro da floresta especificada, para o recipiente RegisteredDevices ao permitir a reutilização do dispositivo.  Nas versões anteriores que contêm a nova funcionalidade Opções de Dispositivos (1.1.819.0 – 1.2.68.0), a localização do contentor RegisteredDevices limitou-se à raiz florestal e não permitiu domínios infantis.  Esta limitação manifestou-se apenas em novas implementações – as atualizações no local não foram afetadas.  

Se alguma construção que contenha a funcionalidade de Opções de Dispositivos atualizada sou implementada para um novo servidor e a reutilização do dispositivo foi ativada, terá de especificar manualmente a localização do recipiente se não o quiser na raiz da floresta.  Para tal, é necessário desativar a reescrita do dispositivo e reativa-lo, o que lhe permitirá especificar a localização do contentor na página "Writeback Forest".



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Estado de lançamento 

11/30/2018: Lançado para download

### <a name="fixed-issues"></a>Problemas corrigidos

Esta construção de hotfix corrige um conflito onde pode ocorrer um erro de autenticação devido à presença independente do módulo MSOnline PowerShell Gallery no servidor de sincronização.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Estado de lançamento 

11/19/2018: Lançado para download

### <a name="fixed-issues"></a>Problemas corrigidos

Esta construção de hotfix corrige uma regressão na construção anterior onde o Password Writeback falha ao utilizar um controlador de domínio ADDS no Windows Server 2008/R2.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Estado de lançamento 

10/25/2018: lançado para download

 
### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 


- Alterou a funcionalidade de redevolução de atributos para garantir que o correio de voz hospedado está a funcionar como esperado.  Em certos cenários, a Azure AD estava a sobrepor-se ao atributo msExchUcVoicemailSettings durante o retempo com um valor nulo.  A Azure AD deixará agora de limpar o valor no local deste atributo se o valor da nuvem não for definido.
- Adicione diagnósticos no assistente Azure AD Connect para investigar e identificar problemas de conectividade para a AD Azure. Estes mesmos diagnósticos também podem ser executados diretamente através da PowerShell utilizando o Cmdlet de Conectividade De Teste- AdSyncAzureService. 
- Adicione diagnósticos no assistente Azure AD Connect para investigar e identificar problemas de conectividade em AD. Estes mesmos diagnósticos também podem ser executados diretamente através do PowerShell utilizando a função Start-ConnectivityValidação no módulo ADConnectivityTools PowerShell.  Para mais informações consulte [O que é o Módulo PowerShell ADConnectivityTool?](how-to-connect-adconnectivitytools.md)
- Adicionei uma versão de esquema aD pré-verificação para Hybrid Azure Ative Directory Join e redisponamento do dispositivo 
- Alterou a página de extensão do diretório para ser sensível a casos.
-   Acrescentou suporte total para TLS 1.2. Esta versão suporta todos os outros protocolos desativados e apenas o TLS 1.2 está ativado na máquina onde o Azure AD Connect está instalado.  Para mais informações consulte [tLS 1.2 aplicação para Azure AD Connect](reference-connect-tls-enforcement.md)

 

### <a name="fixed-issues"></a>Problemas corrigidos   

- Fixou um bug onde o Upgrade de Ligação AD Azure falharia se o SQL Always On estivesse a ser utilizado. 
- Fixou um bug para analisar corretamente os nomes da Ou que contêm um corte para a frente. 
- Corrigiu um problema em que a autenticação pass-through seria desativada para uma instalação limpa no modo de preparação. 
- Fixou um bug que impediu que o módulo PowerShell fosse carregado ao executar as ferramentas de resolução de problemas 
- Fixou um bug que impediria os clientes de usarem valores numéricos no primeiro personagem de um nome anfitrião. 
- Fixou um bug onde o Azure AD Connect permitiria divisórias inválidas e seleção de contentores 
- Fixou a mensagem de erro "Palavra-passe inválida" quando o Desktop SSO estiver ativado. 
- Várias correções de bugs para AD FS Trust Management  
- Ao configurar a Writeback do dispositivo - fixou a verificação de esquemas para procurar a classe de objetos MSDs-DeviceContainer (introduzido no WS2012 R2)

 
## <a name="118820"></a>1.1.882.0  

9/7/2018: lançado para download, não será lançado para atualização automática 

### <a name="fixed-issues"></a>Problemas corrigidos  

A atualização de ligação Azure AD falha se o SQL Always On Availability estiver configurado para o ADSync DB. Este hotfix aborda este problema e permite que o Upgrade tenha sucesso. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Estado de lançamento

8/21/2018: Lançado para download e atualização automática. 

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

- A integração ping federate no Azure AD Connect está agora disponível para disponibilidade geral. [Saiba mais sobre como federar Azure AD com Ping Federate](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- O Azure AD Connect cria agora a cópia de segurança do fundo Azure AD em AD FS sempre que é feita uma atualização e armazena-a num ficheiro separado para facilitar a restauração, se necessário. Saiba mais sobre a nova funcionalidade e a [gestão fiducida da Azure AD no Azure AD Connect](https://aka.ms/fedtrustinaadconnect).
- Nova ferramenta de resolução de problemas ajuda a resolver problemas alterando endereço de e-mail primário e escondendo conta da lista de endereços globais
- Azure AD Connect foi atualizado para incluir o mais recente SQL Server 2012 Client Nativo
- Quando muda o registo do utilizador para a Sincronização de Hash password ou para a autenticação pass-through na tarefa "Alterar o registo do utilizador", a caixa de verificação Single Sign-On sem emenda está ativada por defeito.
- Suporte adicional para Windows Server Essentials 2019
- O agente Azure AD Connect Health foi atualizado para a versão mais recente 3.1.7.0
- Durante uma atualização, se o instalador detetar alterações às regras de sincronização predefinidas, o administrador é solicitado com um aviso antes de anular as regras modificadas. Isto permitirá ao utilizador tomar medidas corretivas e retomar mais tarde. Comportamento Antigo: Se houvesse alguma regra fora da caixa modificada, então a atualização manual estava a sobrepor-se a essas regras sem dar qualquer aviso ao utilizador e o programador de sincronização foi desativado sem informar o utilizador. Novo Comportamento: O utilizador será solicitado com aviso antes de anular as regras modificadas de sincronização fora da caixa. O utilizador terá a opção de parar o processo de atualização e retomar mais tarde depois de tomar medidas corretivas.
- Forneça um melhor tratamento de um problema de conformidade com os FIPS, fornecendo uma mensagem de erro para a geração de hash MD5 num ambiente compatível com FIPS e uma ligação à documentação que fornece um trabalho em torno deste problema.
- Atualização da UI para melhorar as tarefas da federação no assistente, que estão agora sob um subgrupo separado para a federação. 
- Todas as tarefas adicionais da federação estão agora agrunadas sob um único sub-menu para facilitar a utilização.
- Um novo Módulo ADSyncConfig Posh (AdSyncConfig.psm1) com novas funções de Permissões AD deslocadas do antigo ADSyncPrep.psm1 (que pode ser depreciado em breve)

### <a name="fixed-issues"></a>Problemas corrigidos 

- Fixou um bug onde o servidor AAD Connect mostraria uma alta utilização de CPU após a atualização para .NET 4.7.2
- Fixou um bug que produziria intermitentemente uma mensagem de erro para um problema de impasse SQL resolvido automaticamente
- Corrigiu vários problemas de acessibilidade para o Editor de Regras de Sincronização e para o Gestor de Serviços sync  
- Fixou um bug onde o Azure AD Connect não pode obter informações de definição de registo
- Fixou um bug que criou problemas quando o utilizador vai para a frente/para trás no assistente
- Fixou um bug para evitar que um erro ocorram devido a uma entrega incorreta de vários fios no assistente
- Quando a página de filtragem de sincronização do grupo encontra um erro LDAP na resolução de grupos de segurança, o Azure AD Connect devolve agora a exceção com toda a fidelidade.  A causa principal da exceção de referência ainda é desconhecida e será abordada por um bug diferente.
-  Fixou um bug onde as permissões para as teclas STK e NGC (ms-DS-KeyCredentialLink atributo em objetos utilizador/dispositivo para WHfB) não foram corretamente definidas.     
- Fixou um bug onde 'Set-ADSyncRestrictedPermissions' não foi chamado corretamente
-  Adicionar suporte para concessão de permissão no Group Writeback no assistente de instalação da AADConnect
- Ao alterar o sinal no método de Password Hash Sync para AD FS, o Password Hash Sync não foi desativado.
- Verificação adicional para endereços IPv6 na configuração AD FS
- Atualizou a mensagem de notificação para informar que existe uma configuração existente.
- A reescrita do dispositivo não deteta o contentor numa floresta não fidedigna. Isto foi atualizado para fornecer uma melhor mensagem de erro e um link para a documentação apropriada
- Desseleccionar um Ou e, em seguida, sincronização/reescrita correspondente a esse OU dá um erro de sincronização genérico. Isto foi alterado para criar uma mensagem de erro mais compreensível.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Estado de lançamento

5/14/2018: Lançado para upgrade e download automático.

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

Novas funcionalidades e melhorias

- Este lançamento inclui a pré-visualização pública da integração do PingFederate no Azure AD Connect. Com esta versão, os clientes podem configurar facilmente e de forma fiável o seu ambiente de Diretório Ativo Azure para alavancar o PingFederate como seu fornecedor da federação. Para saber mais sobre como utilizar esta nova funcionalidade, visite a nossa [documentação online.](plan-connect-user-signin.md#federation-with-pingfederate) 
- Atualizado o Utilitário de Resolução de Problemas do Assistente De Ligação Azure, onde agora analisa mais cenários de erro, como caixas de correio ligadas e Grupos Dinâmicos AD. Leia mais sobre o utilitário de resolução de problemas [aqui](tshoot-connect-objectsync.md).
- A configuração De writeback do dispositivo é agora gerida exclusivamente dentro do Assistente de Ligação AD Azure.
- Um novo Módulo PowerShell chamado ADSyncTools.psm1 é adicionado que pode ser usado para resolver problemas de conectividade SQL e vários outros utilitários de resolução de problemas. Leia mais sobre o módulo ADSyncTools [aqui](tshoot-connect-tshoot-sql-connectivity.md). 
- Foi adicionada uma nova tarefa adicional "Configure opções de dispositivos". Pode utilizar a tarefa para configurar as seguintes duas operações: 
  - **Ad Hybrid Azure junta-se:** Se o seu ambiente tiver uma pegada ad no local e também quiser beneficiar das capacidades fornecidas pelo Azure Ative Directory, pode implementar dispositivos híbridos Azure AD. Estes são dispositivos associados tanto ao Active Directory no local, como ao Azure Active Directory.
  - **Redação do dispositivo**: A reescrita do dispositivo é utilizada para permitir o acesso condicional com base em dispositivos a Dispositivos AD FS (2012 R2 ou superior) protegidos

    >[!NOTE] 
    > - A opção de permitir a reescrita do dispositivo a partir de opções de sincronização Personalizadas será acinzentada. 
    > -  O módulo PowerShell para ADPrep é depreciado com esta versão.



### <a name="fixed-issues"></a>Problemas corrigidos 

- Esta versão atualiza a instalação Do SQL Server Express para o SQL Server 2012 SP4, que, entre outros, fornece correções para várias vulnerabilidades de segurança.  Consulte [aqui](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) mais informações sobre o SQL Server 2012 SP4.
- Processamento de regras de sincronização: saída As regras de sincronização de adesão sem condição de adesão devem ser desaplicadas se a regra de sincronização dos pais deixar de ser aplicável
- Várias correções de acessibilidade foram aplicadas ao Gestor de Serviços de Sincronização UI e ao Sync Rules Editor
- Assistente de ligação Azure AD: Erro criando conta de conector AD quando o Azure AD Connect está num grupo de trabalho
- Assistente de ligação Azure AD: Na página de sinalização Azure AD exibe a caixa de verificação sempre que exista qualquer incompatibilidade nos domínios ad e nos domínios verificados do Azure AD
- Atualização automática Da correção PowerShell para definir o estado de atualização automática corretamente em certos casos após a tentativa de atualização automática.
- Assistente de ligação azure AD: telemetria atualizada para capturar informações anteriormente desaparecidas
- Assistente de ligação Azure AD: Foram efetuadas as seguintes alterações quando utiliza a tarefa de **iniciar sessão** do utilizador Alterar para mudar de AD FS para Autenticação Pass-through:
    - O Agente de Autenticação Pass-through está instalado no servidor Azure AD Connect e a funcionalidade de autenticação pass-through está ativada, antes de convertermos domínios de federados para geridos.
    - Os utilizadores já não são convertidos de federados para geridos. Apenas os domínios são convertidos.
- Assistente de ligação Azure AD: AD FS Multi Domain Regex não está correto quando o utilizador UPN tem a atualização especial de caracteres Regex para suportar caracteres especiais
- Assistente de ligação Azure AD: Remova a mensagem "Configure source anchor attribute" quando não há alterações 
- Assistente de ligação Azure AD: Suporte AD FS para o cenário da dupla federação
- Assistente de ligação Azure AD: AD FS Claims não são atualizados para domínio adicionado ao converter um domínio gerido para federado
- Assistente de ligação Azure AD: Durante a deteção de pacotes instalados, encontramos produtos relacionados dirsync/Azure AD Sync/Azure AD Connect. Tentaremos agora desinstalar os produtos velhos.
- Assistente de ligação azure AD: Correta error message mapping quando a instalação do agente de autenticação de passagem falha
- Assistente de ligação Azure AD: recipiente de "configuração" removido da página de filtragem de Domínio OU
- Instalação do Sync Engine: remova lógica de legado desnecessária que ocasionalmente falhou na instalação do Sync Engine msi
- Assistente de ligação ad ad do Azure: corrigir texto de ajuda popup na página de Funcionalidades Opcionais para Password Hash Sync
- Tempo de execução do motor sync: Fixe o cenário em que um objeto CS tenha uma exclusão importada e as Regras de Sincronização tentem reabastecer o objeto.
- Tempo de execução do motor sync: Adicione o link de ajuda para o guia de resolução de problemas de conectividade online para o registo de eventos para um erro de importação
- Tempo de execução do motor sync: Utilização reduzida da memória do Programador de Sincronização ao enumerar conectores
- Assistente de ligação Azure AD: Corrija um problema resolvendo uma conta de serviço de sincronização personalizada que não tem privilégios de Leitura aD
- Assistente de ligação Azure AD: Melhorar a exploração madeireira de seleções de filtragem de domínio e DE
- Assistente de ligação Azure AD: AD FS Adicionar créditos padrão ao fundo da federação criado para o cenário mFA
- Assistente de ligação Azure AD: AD FS Deploy WAP: Adicionar servidor não utiliza novo certificado
- Assistente de ligação Azure AD: exceção DSSO quando as credenciais onPrem não são inicializadas para um domínio 
- Fluxo preferencialmente o atributo ad nome distinto do objeto Utilizador Ativo.
- Um bug cosmético fixo foi a precedência da primeira Regra de Sincronização OOB foi definida para 99 em vez de 100



## <a name="117510"></a>1.1.751.0
Estado 4/12/2018: Lançado apenas para download

>[!NOTE]
>Este lançamento é um hotfix para Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Sincronização do Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
Corrigido um problema foi a descoberta automática de azure por exemplo para os inquilinos da China estava ocasionalmente a falhar.  

### <a name="ad-fs-management"></a>Gestão ad FS
#### <a name="fixed-issues"></a>Problemas corrigidos

Havia um problema na lógica de reconfiguração que resultaria numa ArgumentaÇão De nota afirmando que "já foi adicionado um item com a mesma chave".  Isto faria com que todas as operações de retenção falhassem.

## <a name="117500"></a>1.1.750.0
Estado 3/22/2018: Lançado para atualização automática e download.
>[!NOTE]
>Quando a atualização para esta nova versão estiver concluída, irá automaticamente desencadear uma sincronização completa e uma importação completa para o conector Azure AD e uma sincronização completa para o conector AD. Uma vez que isto pode demorar algum tempo, dependendo do tamanho do seu ambiente Azure AD Connect, certifique-se de que tomou as medidas necessárias para suportar isso ou adiar a atualização até encontrar um momento conveniente para o fazer.

>[!NOTE]
>"A funcionalidade AutoUpgrade foi incorretamente desativada para alguns inquilinos que implementaram construções superiores a 1.1.524.0. Para garantir que a sua instância Azure AD Connect ainda é elegível para AutoUpgrade, execute o seguinte cmdlet PowerShell: "Set-ADSyncAutoUpgrade -AutoupGradeState Habilitado"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos

* O cmdlet set-ADSyncAutoUpgrade bloquearia previamente a atualização automática se o estado de atualização automática estiver definido para suspenso. Esta funcionalidade mudou agora para que não bloqueie o AutoUpgrade de futuras construções.
* Alterou a opção **de página de entrada do utilizador** "Password Synchronization" para "Password Hash Synchronization".  O Azure AD Connect sincroniza os hashes de senha, não as palavras-passe, pelo que isto alinha com o que realmente está a ocorrer.  Para mais informações consulte Implementar sincronização de [hash com sincronização Azure AD Connect](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Estado: Lançado para selecionar clientes

>[!NOTE]
>Quando a atualização para esta nova versão estiver concluída, irá automaticamente desencadear uma sincronização completa e uma importação completa para o conector Azure AD e uma sincronização completa para o conector AD. Uma vez que isto pode demorar algum tempo, dependendo do tamanho do seu ambiente Azure AD Connect, certifique-se de que tomou as medidas necessárias para suportar isso ou adiar a atualização até encontrar um momento conveniente para o fazer.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
* Fixe a janela de tempo nas tarefas de fundo para a página de filtragem da partilha ao mudar para a página seguinte.

* Corrigiu um bug que causou violação de Acesso durante a ação personalizada da ConfigDB.

* Fixou um bug para recuperar do tempo de ligação SQL.

* Fixou um bug onde os certificados com wildcards SAN falharam uma verificação pré-requisito.

* Fixou um bug que faz com que miiserver.exe se despenhe durante uma exportação de conecone Azure AD.

* Fixou um bug que a má tentativa de senha registou em DC ao executar o assistente Azure AD Connect para alterar a configuração.


#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Adicionar Definições de Privacidade para o Regulamento Geral de Proteção de Dados (RGPD).  Para mais informações consulte o artigo [Aqui](reference-connect-user-privacy.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* telemetria de aplicações - a administração pode mudar esta classe de dados ligado/desligado à vontade

* Dados da Azure AD Health - administração deve visitar o portal de saúde para controlar as suas definições de saúde.
   Uma vez alterada a política de serviços, os agentes lê-la-ão e aplicarão-na.

* Ações adicionadas de configuração de redevolução de dispositivos e uma barra de progresso para a inicialização da página

* Diagnósticos Gerais Melhorados com relatório HTML e recolha completa de dados num relatório ZIP-Text / HTML

* Melhorou a fiabilidade da atualização automática e adicionou telemetria adicional para garantir que a saúde do servidor pode ser determinada

* Restringir permissões disponíveis para contas privilegiadas na conta AD Connector

  * Para novas instalações, o assistente irá restringir as permissões que as contas privilegiadas têm na conta MSOL após a criação da conta MSOL.

As alterações cuidarão do seguinte:
1. Instalações Express
2. Instalações personalizadas com conta Auto-Criar
3. Alterou o instalador para que não exija privilégio sa na instalação limpa do Azure AD Connect

* Adicionei uma nova utilidade para resolver problemas de sincronização para um objeto específico. Está disponível sob a opção 'Troubleshoot Object Synchronization' de Azure AD Connect Wizard Troubleshoot Tarefa adicional. Atualmente, os serviços de utilidade verificam o seguinte:

  * Desajuste do UserPrincipalName entre o objeto de utilizador sincronizado e a conta de utilizador no Azure AD Tenant.
  * Se o objeto for filtrado da sincronização devido à filtragem do domínio
  * Se o objeto for filtrado da sincronização devido à filtragem da unidade organizacional (OU)

* Adicionei um novo utilitário para sincronizar o hash de senha atual armazenado no Diretório Ativo no local para uma conta de utilizador específica.

O utilitário não requer uma alteração de senha. Está disponível na opção 'Troubleshoot Password Hash Synchronization' do Azure AD Connect Wizard Troubleshoot Tarefa Adicional.






## <a name="116540"></a>1.1.654.0
Estado: 12 de dezembro de 2017

>[!NOTE]
>Esta versão é um hotfix relacionado com a segurança para Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
Foi adicionada uma melhoria à versão 1.1.654.0 (e depois) do Azure AD Connect para garantir que as alterações de permissão recomendadas descritas na secção [Lock down acedam automaticamente à conta AD DS](#lock) quando o Azure AD Connect criar a conta AD DS. 

- Ao configurar o Azure AD Connect, o administrador de instalação pode fornecer uma conta AD DS existente ou deixar o Azure AD Connect criar automaticamente a conta. As alterações de permissão são aplicadas automaticamente à conta AD DS criada pelo Azure AD Connect durante a configuração. Não são aplicadas à conta AD DS existente fornecida pelo administrador de instalação.
- Para os clientes que tenham atualizado de uma versão mais antiga do Azure AD Connect para 1.1.654.0 (ou depois), as alterações de permissão não serão retroativamente aplicadas às contas AD DS existentes criadas antes da atualização. Só serão aplicadas a novas contas AD DS criadas após a atualização. Isto ocorre quando se adicionam novas florestas ad's para serem sincronizadas com a AD Azure.

>[!NOTE]
>Esta versão apenas remove a vulnerabilidade para novas instalações do Azure AD Connect onde a conta de serviço é criada pelo processo de instalação. Para instalações existentes, ou nos casos em que você próprio fornece a conta, deve garantir que esta vulnerabilidade não existe.

#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a>Bloquear o acesso à conta AD DS
Bloqueie o acesso à conta AD DS implementando as seguintes alterações de permissão no ad- no local:  

*   Desativar a herança no objeto especificado
*   Remova todos os ACEs no objeto específico, exceto ACEs específicos de SELF. Queremos manter intactas as permissões por defeito no que diz respeito a SELF.
*   Atribuir estas permissões específicas:

Tipo     | Nome                          | Acesso               | Aplica-se A
---------|-------------------------------|----------------------|--------------|
Permitir    | SISTEMA                        | Controlo Total         | Este objeto  |
Permitir    | Administradores da Empresa             | Controlo Total         | Este objeto  |
Permitir    | Administradores do Domínio                 | Controlo Total         | Este objeto  |
Permitir    | Administradores                | Controlo Total         | Este objeto  |
Permitir    | Controladores de domínio empresarial | Conteúdos da lista        | Este objeto  |
Permitir    | Controladores de domínio empresarial | Ler todas as propriedades  | Este objeto  |
Permitir    | Controladores de domínio empresarial | Ler Permissões     | Este objeto  |
Permitir    | Utilizadores Autenticados           | Conteúdos da lista        | Este objeto  |
Permitir    | Utilizadores Autenticados           | Ler todas as propriedades  | Este objeto  |
Permitir    | Utilizadores Autenticados           | Ler Permissões     | Este objeto  |

Para apertar as definições da conta AD DS pode executar [este script PowerShell](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). O script PowerShell atribuirá as permissões acima mencionadas na conta AD DS.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Script PowerShell para apertar uma conta de serviço pré-existente

Para utilizar o script PowerShell, para aplicar estas definições, numa conta AD DS pré-existente (éter fornecido pela sua organização ou criado por uma instalação anterior do Azure AD Connect, por favor descarregue o script a partir do link fornecido acima.

##### <a name="usage"></a>Utilização:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Onde 

**$ObjectDN** = A conta de Diretório Ativo cujas permissões precisam de ser apertadas.

**$Credential** = Credencial de administrador que tenha os privilégios necessários para restringir as permissões na conta $ObjectDN. Estes privilégios são normalmente detidos pelo administrador da Enterprise ou do Domínio. Utilize o nome de domínio totalmente qualificado da conta do administrador para evitar falhas de procura de conta. Exemplo: contoso.com\admin.

>[!NOTE] 
>$credential. O nome do utilizador deve estar no formato FQDN\username. Exemplo: contoso.com\admin 

##### <a name="example"></a>Exemplo:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Esta vulnerabilidade foi usada para obter acesso não autorizado?

Para ver se esta vulnerabilidade foi usada para comprometer a configuração Azure AD Connect, deve verificar a última data de reset da conta de serviço.  Se o carimbo de tempo numa investigação inesperada e posterior, através do registo do evento, para esse evento de reset de palavra-passe, deve ser realizado.

Para mais informações, consulte [o Microsoft Security Advisory 4056318](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Estado: 27 de outubro de 2017

>[!NOTE]
>Esta construção não está disponível para os clientes através da funcionalidade azure AD Connect Auto Upgrade.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Problema  corrigido
* Corrigiu um problema de compatibilidade entre o Azure AD Connect e o Agente de Saúde Azure AD Connect (para sincronização). Este problema afeta os clientes que estão a realizar o upgrade do Azure AD Connect no local para a versão 1.1.647.0, mas atualmente tem a versão 3.0.127.0 do Health Agent. Após a atualização, o Agente de Saúde já não pode enviar dados de saúde sobre o Serviço de Sincronização de Ligação Azure AD para o Serviço de Saúde Azure AD. Com esta correção, a versão 3.0.129.0 do Health Agent é instalada durante a atualização do Azure AD Connect no local. A versão 3.0.129.0 do Health Agent não tem problemas de compatibilidade com a versão 1.1.649.0 do Azure AD Connect.


## <a name="116470"></a>1.1.647.0
Estado: 19 de outubro de 2017

> [!IMPORTANT]
> Existe um problema de compatibilidade conhecido entre a versão 1.1.1.647.0 do Azure AD Connect E o Azure AD Connect Health Agent (para sincronização) versão 3.0.127.0. Este problema impede o Agente de Saúde de enviar dados de saúde sobre o Serviço de Sincronização de Ligação AD Azure (incluindo erros de sincronização de objetos e dados de histórico de execução) para o Serviço de Saúde Azure AD. Antes de atualizar manualmente a sua implementação de Azure AD Connect para a versão 1.1.647.0, verifique a versão atual do Agente de Saúde Azure AD Connect instalado no seu servidor Azure AD Connect. Pode fazê-lo indo ao Painel de *Controlo → Adicionar Programas removedores* e procurar aplicação Microsoft *Azure AD Connect Health Agent for Sync*. Se a sua versão for de 3.0.127.0, recomenda-se que aguarde que a próxima versão Do Azure AD Connect esteja disponível antes da atualização. Se a versão do Agente de Saúde não for de 3.0.127.0, é bom proceder com a atualização manual e no local. Note que este problema não afeta a atualização do balanço ou os clientes que estão a realizar uma nova instalação do Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigiu um problema com a tarefa *de entrada do utilizador Change* no assistente Azure AD Connect:

  * O problema ocorre quando tem uma implementação de Ligação Azure AD existente com sincronização de palavra-passe **ativada**, e está a tentar definir o método de entrada do utilizador como *Autenticação Pass-through*. Antes da alteração ser aplicada, o assistente mostra incorretamente o aviso de sincronização da*palavra-passe " Desativar a palavra-passe.* No entanto, a Sincronização de Palavras-Passe permanece ativada após a alteração ser aplicada. Com esta correção, o assistente já não mostra o pedido.

  * Por design, o assistente não desativa a Sincronização de Palavras-Passe quando atualiza o método de entrada do utilizador utilizando a tarefa *de iniciar sessão* do utilizador Change. Isto é para evitar perturbações para os clientes que pretendam manter a Sincronização de Passwords, mesmo que estejam a permitir a autenticação ou federação pass-through como o seu método principal de entrada de utilizador.
  
  * Se desejar desativar a sincronização da palavra-passe após atualizar o método de entrada do utilizador, deve executar a tarefa de configuração de *sincronização personalizada* no assistente. Quando navegar na página *de funcionalidades Opcionais,* desverifique a opção de sincronização da *palavra-passe.*
  
  * Note que o mesmo problema também ocorre se tentar ativar/desativar o único sinal de sem emenda. Especificamente, tem uma implementação de Ligação Azure AD existente com sincronização de palavra-passe ativada e o método de entrada do utilizador já está configurado como *Autenticação Pass-through*. Utilizando a tarefa de *entrada do utilizador Change,* tente verificar/desmarcar a opção *Enable Seamless Single Sign-On* enquanto o método de entrada do utilizador permanece configurado como "Autenticação Pass-through". Antes da alteração ser aplicada, o assistente mostra incorretamente o aviso de sincronização da*palavra-passe " Desativar a palavra-passe.* No entanto, a Sincronização de Palavras-Passe permanece ativada após a alteração ser aplicada. Com esta correção, o assistente já não mostra o pedido.

* Corrigiu um problema com a tarefa *de entrada do utilizador Change* no assistente Azure AD Connect:

  * O problema ocorre quando tem uma implementação de Ligação Azure AD existente com sincronização de palavra-passe **desativada,** e está a tentar definir o método de entrada do utilizador como *Autenticação Pass-through*. Quando a alteração é aplicada, o assistente ativa tanto a autenticação pass-through como a sincronização da palavra-passe. Com esta correção, o assistente já não permite a sincronização da palavra-passe.

  * Anteriormente, a Sincronização de Passwords era um pré-requisito para permitir a autenticação pass-through. Quando definir o método de entrada do utilizador como *Autenticação Pass-through,* o assistente ativaria tanto a autenticação pass-through como a sincronização da palavra-passe. Recentemente, a Sincronização de Passwords foi removida como um pré-requisito. Como parte da versão 1.1.557.0 do Azure AD Connect, foi feita uma alteração no Azure AD Connect para não ativar a sincronização da palavra-passe quando definiu o método de entrada do utilizador como *Autenticação Pass-through*. No entanto, a alteração foi aplicada apenas à instalação Azure AD Connect. Com esta correção, a mesma alteração também é aplicada à tarefa de iniciar sessão do *utilizador Change.*
  
  * Note que o mesmo problema também ocorre se tentar ativar/desativar o único sinal de sem emenda. Especificamente, tem uma implementação de Ligação Azure AD existente com sincronização de palavra-passe desativada e o método de entrada do utilizador já está configurado como *Autenticação Pass-through*. Utilizando a tarefa de *entrada do utilizador Change,* tente verificar/desmarcar a opção *Enable Seamless Single Sign-On* enquanto o método de entrada do utilizador permanece configurado como "Autenticação Pass-through". Quando a alteração é aplicada, o assistente ativa a Sincronização de Passwords. Com esta correção, o assistente já não permite a sincronização da palavra-passe. 

* Corrigiu um problema que fez com que a atualização do Azure AD Connect falhasse com o erro " Incapaz de atualizar o Serviço de*Sincronização*". Além disso, o Serviço de Sincronização já não pode começar com erro de evento " O serviço não foi capaz de*iniciar porque a versão da base de dados é mais recente do que a versão dos binários instalados*". O problema ocorre quando o administrador que executa a atualização não tem privilégio de sisadmina para o servidor SQL que está a ser utilizado pelo Azure AD Connect. Com esta correção, o Azure AD Connect apenas requer que o administrador tenha db_owner privilégio para a base de dados ADSync durante a atualização.

* Corrigiu um problema de upgrade de ligação AD Azure que afetou clientes que ativaram o único sinal de [entrada sem emenda](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Depois de o Azure AD Connect ser atualizado, o Single Sign-On sem emenda aparece incorretamente como desativado no assistente Azure AD Connect, mesmo que a funcionalidade permaneça activae totalmente funcional. Com esta correção, a funcionalidade aparece agora corretamente como ativada no assistente.

* Corrigiu um problema que fez com que o assistente de Ligação AD Azur do Azure mostrasse sempre a "*Configuração source anchor*" na página *Ready to Configure,* mesmo que não tenham sido feitas alterações relacionadas com a Âncora de Origem.

* Ao realizar a atualização manual do Azure AD Connect, o cliente é obrigado a fornecer as credenciais do Administrador Global do correspondente inquilino Azure AD. Anteriormente, a atualização poderia prosseguir, mesmo que as credenciais do Administrador Global pertencessem a um inquilino azure diferente. Embora a atualização pareça estar concluída com sucesso, certas configurações não são corretamente persistidas com a atualização. Com esta alteração, o assistente impede que a atualização prossiga se as credenciais fornecidas não corresponderem ao inquilino da AD Azure.

* Removeu a lógica redundante que reiniciou desnecessariamente o serviço Azure AD Connect Health no início de uma atualização manual.


#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* Acrescentou lógica para simplificar os passos necessários para configurar o Azure AD Connect com a Microsoft Germany Cloud. Anteriormente, é-lhe exigido que atualize chaves de registo específicas no servidor Azure AD Connect para que funcione corretamente com a Microsoft Germany Cloud, como descrito neste artigo. Agora, o Azure AD Connect pode detetar automaticamente se o seu inquilino está na Microsoft Germany Cloud com base nas credenciais de administrador global fornecidas durante a configuração.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
> [!NOTE]
> Nota: O Serviço de Sincronização tem uma interface WMI que lhe permite desenvolver o seu próprio programador personalizado. Esta interface está agora degradada e será removida das futuras versões do Azure AD Connect enviadas após 30 de junho de 2018. Os clientes que pretendam personalizar o calendário de sincronização devem utilizar o [programador incorporado.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler)

#### <a name="fixed-issues"></a>Problemas corrigidos
* Quando o assistente azure AD Connect cria a conta AD Connector necessária para sincronizar alterações a partir do Diretório Ativo no local, não atribui corretamente a conta à permissão necessária para ler objetos de Pastas Públicas. Este problema afeta tanto a instalação express como a instalação personalizada. Esta mudança corrige o problema.

* Corrigiu um problema que fez com que a página de resolução de problemas do Azure AD Connect Wizard não tornasse corretamente para os administradores que executassem o Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* Ao resolver problemas a sincronização da palavra-passe utilizando a página de resolução de problemas do assistente Azure AD Connect, a página de resolução de problemas devolve agora o estado específico do domínio.

* Anteriormente, se tentou ativar a sincronização de Hash Password, o Azure AD Connect não verifica se a conta de Conector AD exigiu permissões para sincronizar hashes de senha a partir de AD no local. Agora, o assistente Azure AD Connect verificará e avisa-o se a conta AD Connector não tiver permissões suficientes.

### <a name="ad-fs-management"></a>Gestão ad FS
#### <a name="fixed-issue"></a>Problema  corrigido
* Corrigiu um problema relacionado com a utilização de [ms-DS-Consistência como](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) recurso Source Anchor. Este problema afeta os clientes que configuraram a *Federação com AD FS* como o método de inscrição do utilizador. Quando executa a tarefa de âncora de *origem configurada* no assistente, o Azure AD Connect muda para utilizar *ms-DS-ConsistênciaComo atributo de origem para imutávelId. Como parte desta alteração, o Azure AD Connect tenta atualizar as regras de reclamação para ImutableId em AD FS. No entanto, este passo falhou porque o Azure AD Connect não tinha as credenciais de administrador necessárias para configurar a AD FS. Com esta correção, o Azure AD Connect pede-lhe agora que introduza as credenciais de administrador para AD FS quando executar a tarefa de Configurar Source *Anchor.*



## <a name="116140"></a>1.1.614.0
Estado: 05 de setembro de 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Problemas conhecidos
* Existe um problema conhecido que está a fazer com que a atualização do Azure AD Connect falhe com o erro " Incapaz de atualizar o Serviço de*Sincronização*". Além disso, o Serviço de Sincronização já não pode começar com erro de evento " O serviço não foi capaz de*iniciar porque a versão da base de dados é mais recente do que a versão dos binários instalados*". O problema ocorre quando o administrador que executa a atualização não tem privilégio de sisadmina para o servidor SQL que está a ser utilizado pelo Azure AD Connect. As permissões dbo não são suficientes.

* Existe um problema conhecido com o Azure AD Connect Upgrade que está a afetar os clientes que ativaram o [Single Sign-On seamless](how-to-connect-sso.md). Depois de o Azure AD Connect ser atualizado, a funcionalidade aparece como desativada no assistente, mesmo que a funcionalidade permaneça ativada. Uma correção para esta questão será fornecida no futuro lançamento. Os clientes que se preocupam com este problema de exibição podem corrigi-lo manualmente, permitindo um único sinal perfeito no assistente.

#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigiu um problema que impedia o Azure AD Connect de atualizar as regras de reclamações no AD FS no local, permitindo ao mesmo tempo a [funcionalidade ms-DS-Consistência como](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) recurso Source Anchor. O problema ocorre se tentar ativar a funcionalidade para uma implementação de Azure AD Connect existente que tenha AD FS configurado como o método de entrada. O problema ocorre porque o assistente não solicita credenciais ADFS antes de tentar atualizar as regras de reclamações em AD FS.
* Corrigiu um problema que fez com que o Azure AD Connect falhasse a instalação se a floresta ad-local tiver a NTLM desativada. O problema deve-se ao facto de o assistente Azure AD Connect não fornecer credenciais totalmente qualificadas na criação dos contextos de segurança necessários para a autenticação kerberos. Isto faz com que a autenticação Kerberos falhe e o assistente Azure AD Connect recue para a utilização do NTLM.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigiu uma questão em que não se pode criar uma nova regra de sincronização se o atributo tag não for povoado.
* Corrigiu um problema que fez com que o Azure AD Connect se ligasse ao AD no local para sincronização de palavras-passe utilizando a NTLM, apesar de kerberos estar disponível. Este problema ocorre se a topologia ad no local tiver um ou mais controladores de domínio que foram restaurados a partir de uma cópia de segurança.
* Corrigiu um problema que fez com que as etapas de sincronização completa ocorram desnecessariamente após a atualização. Em geral, é necessário executar passos de sincronização completos após a atualização se houver alterações às regras de sincronização fora da caixa. O problema deveu-se a um erro na lógica de deteção de mudanças que detetou incorretamente uma mudança ao encontrar expressão de regra de sincronização com caracteres de linha nova. Os caracteres newline são inseridos na expressão de regra sincronizada para melhorar a legibilidade.
* Corrigiu um problema que pode fazer com que o servidor Azure AD Connect não funcione corretamente após a Atualização Automática. Este problema afeta os servidores Azure AD Connect com a versão 1.1.443.0 (ou anterior). Para mais detalhes sobre o problema, consulte o artigo [Azure AD Connect não está a funcionar corretamente após uma atualização automática](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Corrigiu um problema que pode fazer com que a Atualização Automática seja novamente experimentada a cada 5 minutos quando os erros são encontrados. Com a correção, a Atualização Automática retenta com back-off exponencial quando os erros são encontrados.
* Corrigiu um problema em que o evento de sincronização de palavras-passe 611 é incorretamente mostrado nos registos do Windows Application Event como **informativoem** em vez de **erro**. O evento 611 é gerado sempre que a sincronização de palavras-passe encontra um problema. 
* Corrigiu um problema no assistente Azure AD Connect que permite ativar a funcionalidade de reescrita do Grupo sem selecionar um OU necessário para a reescrita do Grupo.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* Adicionei uma tarefa de Troubleshoot ao assistente Azure AD Connect em tarefas adicionais. Os clientes podem aproveitar esta tarefa para resolver problemas relacionados com a sincronização de passwords e recolher diagnósticos gerais. No futuro, a tarefa Troubleshoot será alargada de modo a incluir outras questões relacionadas com a sincronização do diretório.
* O Azure AD Connect suporta agora um novo modo de instalação chamado **Use Existing Database**. Este modo de instalação permite que os clientes instalem o Azure AD Connect que especifica uma base de dados ADSync existente. Para mais informações sobre esta funcionalidade, consulte o artigo [Utilize uma base](how-to-connect-install-existing-database.md)de dados existente .
* Para uma maior segurança, o Azure AD Connect passa a inserir-se na utilização do TLS1.2 para ligar ao Azure AD para sincronização de diretórios. Anteriormente, o padrão era TLS1.0.
* Quando o Agente de Sincronização de Passwords De Ligação Azure AD começa, tenta ligar-se ao ponto final conhecido da AD Azure para a sincronização de passwords. Após uma ligação bem sucedida, é redirecionado para um ponto final específico da região. Anteriormente, o Agente de Sincronização de Palavras-Passe cacheo o ponto final específico da região até que seja reiniciado. Agora, o agente limpa a cache e tenta com o ponto final conhecido se encontrar problemade conexão com o ponto final específico da região. Esta alteração garante que a sincronização da palavra-passe pode falhar num ponto final específico da região quando o ponto final específico da região em cache já não estiver disponível.
* Para sincronizar alterações de uma floresta do AD local, precisa de uma conta do AD DS. Você pode (i) criar a conta AD DS por si mesmo e fornecer a sua credencial para Azure AD Connect, ou (ii) fornecer as credenciais de um Administrador da Empresa e deixar o Azure AD Connect criar a conta AD DS para si. Anteriormente, (i) é a opção predefinida no assistente Azure AD Connect. Agora, (ii) é a opção padrão.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* Suporte adicional para microsoft Azure Government Cloud e Microsoft Cloud Germany.

### <a name="ad-fs-management"></a>Gestão ad FS
#### <a name="fixed-issues"></a>Problemas corrigidos
* O cmdlet Initialize-ADSyncNGCKeysWriteBack no módulo de preparação AD PowerShell estava a aplicar incorretamente ACLs no recipiente de registo do dispositivo e, portanto, herdaria apenas as permissões existentes.  Isto foi atualizado para que a conta de serviço de sincronização tenha as permissões corretas.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* A tarefa de login AAD Connect Check ADFS foi atualizada de modo a verificar logins contra o Microsoft Online e não apenas a recuperação simbólica da ADFS.
* Ao criar uma nova quinta ADFS utilizando o AAD Connect, a página que pedia credenciais ADFS foi movida para que ocorra agora antes de o utilizador ser solicitado a fornecer servidores ADFS e WAP.  Isto permite ao AAD Connect verificar se a conta especificada tem as permissões corretas.
* Durante a atualização do AAD Connect, deixaremos de falhar uma atualização se o ADFS AAD Trust não atualizar.  Se isso acontecer, o utilizador receberá uma mensagem de aviso adequada e deverá proceder à redefinição da confiança através da tarefa adicional do AAD Connect.

### <a name="seamless-single-sign-on"></a>Insígnia única sem emenda
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigiu um problema que fez com que o assistente de Ligação AD AD do Azure devolvesse um erro se tentasse ativar o simples sinal de [entrada sem emenda](how-to-connect-sso.md). A mensagem de erro é *"Configuração do Agente de Autenticação de Ligação AD do Microsoft Azure falhou."* Este problema afeta os clientes existentes que tinham atualizado manualmente a versão de pré-visualização dos Agentes de Autenticação para [Autenticação Pass-through](how-to-connect-sso.md) com base nos passos descritos neste [artigo](how-to-connect-pta-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Estado: 23 de julho de 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema  corrigido

* Corrigiu um problema que fez com que a regra de sincronização fora da caixa "out to AD - User ImutableId" fosse removida:

  * O problema ocorre quando o Azure AD Connect é atualizado, ou quando a opção de tarefa *Atualiza a configuração de sincronização* no assistente Azure AD Connect é usada para atualizar a configuração de sincronização do Azure AD Connect.
  
  * Esta regra de sincronização é aplicável aos clientes que permitiram o [ms-DS-Consistência-Guia como recurso Source Anchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Esta funcionalidade foi introduzida na versão 1.1.524.0 e depois. Quando a regra de sincronização é removida, o Azure AD Connect já não pode preencher no local a atribuição ad ms-DS-Consistência-Consistência Com o valor do atributo ObjectGuid. Não impede que novos utilizadores sejam aprovisionados em Anúncios Azure.
  
  * A correção garante que a regra de sincronização deixará de ser removida durante a atualização, ou durante a alteração de configuração, desde que a funcionalidade esteja ativada. Para os clientes existentes que foram afetados por este problema, a correção também garante que a regra de sincronização é adicionada de volta após a atualização para esta versão do Azure AD Connect.

* Corrigiu uma questão que faz com que as regras de sincronização fora da caixa tenham valor de precedência inferior a 100:

  * Em geral, os valores de precedência 0 - 99 são reservados para regras de sincronização personalizadas. Durante a atualização, os valores de precedência para regras de sincronização fora da caixa são atualizados para acomodar alterações de regras de sincronização. Devido a esta questão, as regras de sincronização fora da caixa podem ser atribuídas ao valor de precedência inferior a 100.
  
  * A correção impede que o problema ocorra durante a atualização. No entanto, não restaura os valores de precedência para os clientes existentes que foram afetados pela questão. No futuro será fornecida uma correção separada para ajudar na restauração.

* Corrigiu um problema em que o ecrã de [filtragem de Domínio e U](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente Azure AD Connect está a mostrar *sincronizar todos os domínios e* opções de OUs conforme selecionado, mesmo que a filtragem baseada em OU esteja ativada.

*   Corrigiu um problema que fez com que o ecrã de Partições de [Diretório configure](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) no Gestor de Serviçode Sincronização devolvesse um erro se o botão *Refresh* estiver clicado. A mensagem de erro é *"Um erro foi encontrado enquanto refrescava domínios: Incapaz de lançar objeto do tipo 'System.Collections.ArrayList' para escrever 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject.".* O erro ocorre quando o novo domínio ad foi adicionado a uma floresta aD existente e está a tentar atualizar o Azure AD Connect utilizando o botão Refresh.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* [A funcionalidade de atualização automática](how-to-connect-install-automatic-upgrade.md) foi expandida para apoiar os clientes com as seguintes configurações:
  * Ativou a funcionalidade de reprodução do dispositivo.
  * Permitiu a funcionalidade de redação em grupo.
  * A instalação não é uma definição express ou uma atualização DirSync.
  * Tem mais de 100.000 objetos no metaverso.
  * Estás a ligar-te a mais do que uma floresta. A configuração expressa só se liga a uma floresta.
  * A conta AD Connector já não é a conta padrão MSOL_.
  * O servidor está definido para estar em modo de encenação.
  * Ativou a funcionalidade de reutilização do utilizador.
  
  >[!NOTE]
  >A expansão do âmbito da funcionalidade de atualização automática afeta os clientes com a azure AD Connect construir 1.1.105.0 e depois. Se não quiser que o seu servidor Azure AD Connect seja automaticamente atualizado, tem de executar `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`o seguinte cmdlet no seu servidor Azure AD Connect: . Para mais informações sobre a ativação/desativação da atualização automática, consulte o artigo [Azure AD Connect: Upgrade automático](how-to-connect-install-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Estado: Não será libertado. As alterações nesta construção estão incluídas na versão 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema  corrigido

* Corrigiu um problema que fez com que a regra de sincronização fora da caixa "out to AD - User ImmutableId" fosse removida quando a configuração de filtragem baseada em OU for atualizada. Esta regra de sincronização é necessária para o [ms-DS-Consistência-Guia como recurso De âncora de origem](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Corrigiu um problema em que o ecrã de [filtragem de Domínio e U](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente Azure AD Connect está a mostrar *sincronizar todos os domínios e* opções de OUs conforme selecionado, mesmo que a filtragem baseada em OU esteja ativada.

*   Corrigiu um problema que fez com que o ecrã de Partições de [Diretório configure](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) no Gestor de Serviçode Sincronização devolvesse um erro se o botão *Refresh* estiver clicado. A mensagem de erro é *"Um erro foi encontrado enquanto refrescava domínios: Incapaz de lançar objeto do tipo 'System.Collections.ArrayList' para escrever 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject.".* O erro ocorre quando o novo domínio ad foi adicionado a uma floresta aD existente e está a tentar atualizar o Azure AD Connect utilizando o botão Refresh.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* [A funcionalidade de atualização automática](how-to-connect-install-automatic-upgrade.md) foi expandida para apoiar os clientes com as seguintes configurações:
  * Ativou a funcionalidade de reprodução do dispositivo.
  * Permitiu a funcionalidade de redação em grupo.
  * A instalação não é uma definição express ou uma atualização DirSync.
  * Tem mais de 100.000 objetos no metaverso.
  * Estás a ligar-te a mais do que uma floresta. A configuração expressa só se liga a uma floresta.
  * A conta AD Connector já não é a conta padrão MSOL_.
  * O servidor está definido para estar em modo de encenação.
  * Ativou a funcionalidade de reutilização do utilizador.
  
  >[!NOTE]
  >A expansão do âmbito da funcionalidade de atualização automática afeta os clientes com a azure AD Connect construir 1.1.105.0 e depois. Se não quiser que o seu servidor Azure AD Connect seja automaticamente atualizado, tem de executar `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`o seguinte cmdlet no seu servidor Azure AD Connect: . Para mais informações sobre a ativação/desativação da atualização automática, consulte o artigo [Azure AD Connect: Upgrade automático](how-to-connect-install-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Estado: julho 2017

>[!NOTE]
>Esta construção não está disponível para os clientes através da funcionalidade azure AD Connect Auto Upgrade.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema  corrigido
* Corrigiu um problema com o cmdlet Initialize-ADSyncDomainJoinedComputerSync que fez com que o domínio verificado configurado no objeto de ponto de ligação de serviço existente fosse alterado mesmo que ainda seja um domínio válido. Este problema ocorre quando o seu inquilino Azure AD tem mais do que um domínio verificado que pode ser usado para configurar o ponto de ligação de serviço.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* A reescrita de palavra-passe já está disponível para pré-visualização com a nuvem do Governo Microsoft Azure e a Microsoft Cloud Germany. Para obter mais informações sobre o suporte azure AD Connect para as diferentes instâncias de serviço, consulte o artigo [Azure AD Connect: Considerações especiais por exemplo](reference-connect-instances.md).

* O Initialize-ADSyncDomainJoinedComputerSync cmdlet tem agora um novo parâmetro opcional chamado AzureADDomain. Este parâmetro permite especificar qual o domínio verificado a utilizar para configurar o ponto de ligação de serviço.

### <a name="pass-through-authentication"></a>Autenticação pass-through

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* O nome do agente necessário para a autenticação pass-through foi alterado do *Conector proxy da aplicação ad* microsoft Azure para o Agente de Autenticação de *AD Connect Microsoft Azure*.

* Ativar a autenticação pass-through já não permite a sincronização de hash por padrão.


## <a name="115530"></a>1.1.553.0
Estado: junho 2017

> [!IMPORTANT]
> Há alterações de regras de esquemas e sincronização introduzidas nesta construção. O Serviço de Sincronização de Ligação Azure AD irá desencadear passos de importação completa e sincronização completa após a atualização. Os detalhes das alterações são descritos abaixo. Para adiar temporariamente os passos completos de importação e sincronização completa após a atualização, consulte o artigo [Como adiar a sincronização completa após](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)a atualização .
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Problema conhecido
* Existe um problema que afeta os clientes que estão a usar [filtragem baseada em OU](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) com sincronização Azure AD Connect. Quando navega para a página de [filtragem de Domínio e OU](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente Azure AD Connect, espera-se o seguinte comportamento:
  * Se a filtragem baseada em OU estiver ativada, os **domínios selecionados** sync e a opção OUs são selecionados.
  * Caso contrário, a **sincronização de todos os domínios e** a opção OUs são selecionadas.

A questão que surge é que a **sincronização de todos os domínios e opção OUs** são sempre selecionadas quando executa o Assistente.  Isto ocorre mesmo que a filtragem baseada em OU tenha sido previamente configurada. Antes de guardar quaisquer alterações de configuração do AAD Connect, certifique-se de que os domínios selecionados sync e a **opção OUs são selecionados** e confirme que todas as OUs que precisam de sincronizar estão novamente ativadas. Caso contrário, a filtragem baseada em OU será desativada.

#### <a name="fixed-issues"></a>Problemas corrigidos

* Corrigiu um problema com a reescrita da Palavra-Passe que permite a um Administrador De D.A. Azure redefinir a palavra-passe de uma conta de utilizador privilegiada ad no local. O problema ocorre quando o Azure AD Connect recebe a permissão de palavra-passe redefinida sobre a conta privilegiada. O problema é abordado nesta versão do Azure AD Connect, não permitindo que um Administrador DaD Azure redefinir a palavra-passe de uma conta de utilizador privilegiada no local, a menos que o administrador seja o proprietário dessa conta. Para mais informações, consulte o [Aviso de Segurança 4033453](https://technet.microsoft.com/library/security/4033453).

* Corrigiu um problema relacionado com o [ms-DS-Consistência Como](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) recurso Source Anchor onde o Azure AD Connect não reescreve para o atributo AD ms-DS-Consistência. O problema ocorre quando existem múltiplas florestas ad-in-instalações adicionadas ao Azure AD Connect e as *identidades do Utilizador existem em várias listas de opção.* Quando essa configuração é utilizada, as regras de sincronização resultantes não povoam o atributo sourceAnchorBinary no Metaverso. O atributo sourceAnchorBinary é usado como atributo de origem para ms-DS-Consistência-Atributo. Como resultado, a reescrita ao atributo ms-DSConsistênciaGuid não ocorre. Para corrigir o problema, foram atualizadas as seguintes regras de sincronização para garantir que o atributo SourceAnchorBinary no Metaverse é sempre preenchido:
  * Em AD - InetOrgPerson AccountEnabled.xml
  * Em AD - InetOrgPerson Common.xml
  * Em AD - Conta de utilizadorEnabled.xml
  * Em AD - User Common.xml
  * Em AD - User Join SOAInAAD.xml

* Anteriormente, mesmo que a funcionalidade [ms-DS-Consistência como recurso Source Anchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) não esteja ativada, a regra de sincronização "out to AD – User ImutableId" ainda é adicionada ao Azure AD Connect. O efeito é benigno e não causa a reescrita do atributo ms-DS-Consistência-Guia a ocorrer. Para evitar confusão, foi adicionada lógica para garantir que a regra de sincronização só é adicionada quando a funcionalidade está ativada.

* Corrigiu um problema que fez com que a sincronização do hash da palavra-passe falhasse com o erro do evento 611. Este problema ocorre depois de um ou mais controladores de domínio terem sido removidos do local. No final de cada ciclo de sincronização de palavras-passe, o cookie de sincronização emitido por AD no local contém IDs de invocação dos controladores de domínio removidos com valor USN (Número de sequência de atualização) de 0. O Gestor de Sincronização de Passwords é incapaz de persistir cookie de sincronização contendo valor USN de 0 e falha com o erro do evento 611. Durante o próximo ciclo de sincronização, o Gestor de Sincronização de Passwordres reutiliza o último cookie de sincronização persistente que não contém valor USN de 0. Isto faz com que as mesmas alterações de senha sejam resincronizadas. Com esta correção, o Gestor de Sincronização de Passwords persiste corretamente o cookie de sincronização.

* Anteriormente, mesmo que a Atualização Automática tenha sido desativada utilizando o cmdlet Set-ADSyncAutoUpgrade, o processo de Atualização Automática continua a verificar a atualização periodicamente, e conta com o instalador descarregado para honrar a desativação. Com esta correção, o processo de Atualização Automática já não verifica periodicamente a atualização. A correção é aplicada automaticamente quando o instalador de atualização para esta versão Azure AD Connect for executada uma vez.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Anteriormente, o [ms-DS-Consistência-Guia como](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) recurso Source Anchor estava disponível apenas para novas implementações. Agora, está disponível para as implementações existentes. Mais especificamente:
  * Para aceder à funcionalidade, inicie o assistente Azure AD Connect e escolha a opção *Anchor Source Update.*
  * Esta opção só é visível para as implementações existentes que estão usando objectGuid como atributo sourceAnchor.
  * Ao configurar a opção, o assistente valida o estado do atributo ms-DS-Consistência-Guia no seu Diretório Ativo no local. Se o atributo não estiver configurado em qualquer objeto de utilizador no diretório, o assistente utiliza o ms-DS-ConsistênciaComo atributo sourceAnchor. Se o atributo estiver configurado num ou mais objetos de utilizador no diretório, o assistente conclui que o atributo está a ser utilizado por outras aplicações e não é adequado como atributo sourceAnchor e não permite que a alteração da Âncora Fonte prossiga. Se tem a certeza de que o atributo não é utilizado pelas aplicações existentes, tem de contactar o Suporte para obter informações sobre como suprimir o erro.

* Específico do atributo do **UserCertificate** em objetos do Dispositivo, o Azure AD Connect procura agora os valores de certificados necessários para a ligação de [dispositivos ligados ao domínio ao Azure AD para a experiência do Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) e filtra o resto antes de sincronizar para o Azure AD. Para permitir este comportamento, foi atualizada a regra de sincronização fora da caixa "out to AAD - Device Join SOAInAD".

* O Azure AD Connect suporta agora a reprodução de Exchange Online **cloudPublicDelegates** atribui aos atributos **públicos** aditados no local. Isto permite o cenário em que uma caixa de correio Exchange Online pode ser concedida direitos de Envio De EnvioDe Para utilizadores com caixa de correio de troca no local. Para suportar esta funcionalidade, foi adicionada uma nova regra de sincronização fora da caixa "out to AD – User Exchange Hybrid PublicDelegates" foi adicionada. Esta regra de sincronização só é adicionada ao Azure AD Connect quando a funcionalidade Exchange Hybrid estiver ativada.

* O Azure AD Connect suporta agora sincronizar o atributo **altRecipient** da Azure AD. Para suportar esta alteração, foram atualizadas as regras de sincronização fora da caixa para incluir o fluxo de atributo suportou:
  * Em AD – Permuta de Utilizadores
  * Out to AAD – User ExchangeOnline
  
* O atributo **cloudSOAExchMailbox** no Metaverso indica se um determinado utilizador tem caixa de correio Exchange Online ou não. A sua definição foi atualizada para incluir tipos de visualização de destinatários online de intercâmbio adicionais como tais caixas de correio de equipamentos e salas de conferências. Para permitir esta alteração, a definição do atributo cloudSOAExchMailbox, que é encontrada sob a regra de sincronização fora da caixa "In from AAD – User Exchange Hybrid", foi atualizada a partir de:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... Ao seguinte:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* Acrescentou o seguinte conjunto de funções compatíveis com X509Certificate2 para a criação de expressões de regra de sincronização para lidar com valores de certificado no atributo do Certificado de utilizador:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|Algoritmo CertKey|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotAntes|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Selecione|
    |CertKeyAlgorithmParams|CertHashString|Onde|
    |||Com|

* Foram introduzidas alterações de esquemas que permitem aos clientes criar regras de sincronização personalizadas para fluir sAMAccountName, domainNetBios e domainFQDN para objetos de grupo, bem como nome distinto para objetos de utilizador:

  * Foram adicionados seguintes atributos ao esquema MV:
    * Grupo: Nome de conta
    * Grupo: domínioNetBios
    * Grupo: domínioFQDN
    * Pessoa: nome distinto

  * Foram adicionados seguintes atributos ao esquema do Conector Azure AD:
    * Grupo: OnPremisesSamAccountName
    * Grupo: NetBiosName
    * Grupo: DnsDomainName
    * Utilizador: OnPremisesDistinguishedName

* O script ADSyncDomainJoinedComputerSync cmdlet tem agora um novo parâmetro opcional chamado AzureEnvironment. O parâmetro é utilizado para especificar em que região está hospedado o correspondente inquilino do Diretório Ativo Azure. Os valores válidos incluem:
  * AzureCloud (padrão)
  * AzureChinaCloud
  * AzureGermanyCloud
  * Governo dos EUA
 
* Editor de regras de sincronização atualizado para usar Join (em vez de Provision) como o valor padrão do tipo de link durante a criação de regras de sincronização.

### <a name="ad-fs-management"></a>Gestão aD FS

#### <a name="issues-fixed"></a>Questões corrigidas

* A seguir aos URLs estão os novos pontos finais da WS-Federation introduzidos pela Azure AD para melhorar a resiliência contra a interrupção da autenticação e serão adicionados à configuração de confiança do partido AD FS no local:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Corrigiu um problema que fez com que a AD FS gerasse um valor de reclamação incorreto para o Emitente. O problema ocorre se existirem múltiplos domínios verificados no inquilino da AD Azure e o sufixo de domínio do atributo do userPrincipalName utilizado para gerar a alegação Deemite é de pelo menos 3 níveis de profundidade (por exemplo, johndoe@us.contoso.com). A questão é resolvida atualizando o regex utilizado pelas regras de reclamação.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* Anteriormente, a funcionalidade de Gestão de Certificados ADFS fornecida pelo Azure AD Connect só pode ser utilizada com explorações ADFS geridas através do Azure AD Connect. Agora, pode utilizar a funcionalidade com quintas ADFS que não são geridas utilizando o Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Lançado: maio de 2017

> [!IMPORTANT]
> Há alterações de regras de esquemas e sincronização introduzidas nesta construção. O Serviço de Sincronização de Ligação Azure AD irá acionar os passos de Importação Completa e Sincronização Completa após a atualização. Os detalhes das alterações são descritos abaixo.
>
>

**Questões fixas:**

Sincronização do Azure AD Connect

* Corrigiu um problema que faz com que oCorra a Atualização Automática no servidor Azure AD Connect, mesmo que o cliente tenha desativado a funcionalidade utilizando o cmdlet Set-ADSyncAutoUpgrade. Com esta correção, o processo de Atualização Automática no servidor ainda verifica a atualização periodicamente, mas o instalador descarregado honra a configuração de Upgrade Automático.
* Durante a atualização no local do DirSync, o Azure AD Connect cria uma conta de serviço Azure AD a ser utilizada pelo conector Azure AD para sincronização com a AD Azure. Após a criação da conta, o Azure AD Connect autentica com a Azure AD utilizando a conta. Por vezes, a autenticação falha devido a problemas transitórios, o que por sua vez faz com que a atualização do DirSync no local falhe com o erro *"Ocorreu um erro executando a tarefa de Configuração AAD Sync: AADSTS50034: Para assinar nesta aplicação, a conta deve ser adicionada ao diretório xxx.onmicrosoft.com."* Para melhorar a resiliência da atualização do DirSync, o Azure AD Connect volta agora a tentar novamente o passo de autenticação.
* Houve um problema com a construção 443 que faz com que a atualização do DirSync no local tenha sucesso, mas os perfis de execução necessários para a sincronização do diretório não são criados. A lógica de cura está incluída nesta construção do Azure AD Connect. Quando o cliente atualiza para esta construção, o Azure AD Connect deteta perfis de execução em falta e cria-os.
* Corrigiu um problema que faz com que o processo de sincronização da palavra-passe não comece com o Id 6900 do Evento e error *"Um item com a mesma chave já foi adicionado"*. Este problema ocorre se atualizar a configuração de filtragem de OU para incluir a divisão de configuração ad. Para corrigir este problema, o processo de sincronização da palavra-passe sincroniza agora as alterações de palavra-passe apartir apenas das divisórias de domínio AD. Divisórias não-domínio tais como divisórias de configuração são ignoradas.
* Durante a instalação do Expresso, o Azure AD Connect cria uma conta AD DS no local a ser utilizada pelo conector AD para comunicar com a AD no local. Anteriormente, a conta é criada com o PASSWD_NOTREQD bandeira definida no atributo de controlo de conta e uma palavra-passe aleatória é definida na conta. Agora, o Azure AD Connect remove explicitamente a bandeira PASSWD_NOTREQD depois de a palavra-passe ser definida na conta.
* Corrigiu um problema que faz com que a atualização do DirSync falhe com o erro *"ocorreu um impasse no servidor sql que tenta adquirir um bloqueio de aplicação"* quando o atributo do mailNickname é encontrado no esquema AD no local, mas não está limitado à classe de objetos ad user.
* Corrigiu um problema que faz com que a funcionalidade de reutilização do Dispositivo seja automaticamente desativada quando um administrador está a atualizar a configuração de sincronização do Azure AD Connect utilizando o assistente Azure AD Connect. Este problema é causado pelo assistente que executa uma verificação pré-necessária para a configuração de repreensão do Dispositivo existente no AD no local e a verificação falha. A correção é saltar a verificação se a recompra do Dispositivo já está ativada anteriormente.
* Para configurar a filtragem ou, pode utilizar o assistente Azure AD Connect ou o Gestor de Serviçode Sincronização. Anteriormente, se utilizar o assistente Azure AD Connect para configurar a filtragem de OU, novas OUs criadas posteriormente estão incluídas para sincronização de diretórios. Se não quiser que sejam incluídas novas OUs, tem de configurar a filtragem da U utilizando o Gestor de Serviços de Sincronização. Agora, podes obter o mesmo comportamento usando o assistente Azure AD Connect.
* Corrigiu um problema que faz com que os procedimentos armazenados exigidos pelo Azure AD Connect sejam criados sob o esquema do administrador de instalação, em vez de sob o esquema dbo.
* Corrigiu um problema que faz com que o atributo TrackingId devolvido pela Azure AD seja omitido nos Registos de Eventos do Servidor de Ligação AAD. O problema ocorre se o Azure AD Connect receber uma mensagem de reorientação da Azure AD e o Azure AD Connect não conseguir ligar-se ao ponto final fornecido. O TrackingId é utilizado por Engenheiros de Suporte para correlacionar com os registos laterais do serviço durante a resolução de problemas.
* Quando o Azure AD Connect recebe um erro de LargeObject da Azure AD, o Azure AD Connect gera um evento com o EventID 6941 e a mensagem *"O objeto provisionado é demasiado grande. Aparar o número de valores de atributos neste objeto."* Ao mesmo tempo, o Azure AD Connect também gera um evento enganador com o EventID 6900 e a mensagem *"Microsoft.Online.Coexistence.ProvisionRetryException: Incapaz de comunicar com o serviço de Diretório Ativo Windows Azure."* Para minimizar a confusão, o Azure AD Connect já não gera este último evento quando o erro do LargeObject é recebido.
* Corrigiu um problema que faz com que o Gestor de Serviços de Sincronização fique sem resposta ao tentar atualizar a configuração do conector Genérico LDAP.

**Novas funcionalidades/melhorias:**

Sincronização do Azure AD Connect
* Alterações da regra de sincronização – Foram implementadas as seguintes alterações de regra de sincronização:
  * Regra de sincronização predefinida atualizada definida para não exportar atribui certificado **de utilizador** e **userSMIMECertificate** se os atributos tiverem mais de 15 valores.
  * AD atribui **o employeeID** e **msExchBypassModerationLink** estão agora incluídos no conjunto de regras de sincronização padrão.
  * A **foto** de atributo AD foi removida do conjunto de regras de sincronização padrão.
  * Adicionou **a preferredDataLocation** ao esquema metaverso e ao esquema do Conector AAD. Os clientes que pretendam atualizar qualquer um dos atributos em Azure AD podem implementar regras de sincronização personalizadas para o fazer. 
  * Adicionou o **userType** ao esquema metaverso e ao esquema do conector AAD. Os clientes que pretendam atualizar qualquer um dos atributos em Azure AD podem implementar regras de sincronização personalizadas para o fazer.

* O Azure AD Connect permite agora automaticamente a utilização do atributo Do ConsistênciaGuid como atributo de Âncora Fonte para objetos AD no local. Além disso, o Azure AD Connect povoa o atributo ConsistênciaGuid com o valor do atributo objectGuid se estiver vazio. Esta funcionalidade aplica-se apenas a novas implementações. Para saber mais sobre esta funcionalidade, consulte a secção de artigos [Azure AD Connect: Design concepts - Utilizando ms-DS-Consistência-Guid como fonteAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* Foi adicionado novo cmdlet de resolução de problemas Invoke-ADSyncDiagnostics para ajudar a diagnosticar problemas relacionados com a sincronização de hash de password. Para obter informações sobre a utilização do cmdlet, consulte o artigo Sincronização de hash de [palavra-passe de Troubleshoot com sincronização Azure AD Connect](tshoot-connect-password-hash-synchronization.md).
* O Azure AD Connect suporta agora objetos de pasta pública ativados por correio, desde o anúncio do Local AD até ao Azure AD. Pode ativar a funcionalidade utilizando o assistente Azure AD Connect em funcionalidades opcionais. Para saber mais sobre esta funcionalidade, consulte o [artigo Office 365 Directory Based Edge Blocking support for on-premises Mail Enabled Public Folders](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* O Azure AD Connect requer uma conta AD DS para sincronizar a partir do local a d.C. Anteriormente, se instalasse o Azure AD Connect utilizando o modo Express, poderia fornecer as credenciais de uma conta Enterprise Admin e o Azure AD Connect criaria a conta AD DS necessária. No entanto, para uma instalação personalizada e adicionando florestas a uma implantação existente, foi-lhe exigido que fornecesse a conta AD DS. Agora, você também tem a opção de fornecer as credenciais de uma conta Enterprise Admin durante uma instalação personalizada e deixar o Azure AD Connect criar a conta AD DS necessária.
* O Azure AD Connect suporta agora o SQL AOA. Tem de ativar o SQL AOA antes de instalar o Azure AD Connect. Durante a instalação, o Azure AD Connect deteta se a instância SQL fornecida está ativada para o SQL AOA ou não. Se o SQL AOA estiver ativado, o Azure AD Connect descobre ainda se o SQL AOA estiver configurado para utilizar replicação sincronizada ou replicação assíncrona. Ao configurar o Ouvinte do Grupo de Disponibilidade, recomenda-se que defina a propriedade RegisterAllProvidersIP para 0. Esta recomendação é porque o Azure AD Connect usa atualmente o Cliente Nativo SQL para se conectar ao SQL e ao SQL Client Nativo não suporta o uso da propriedade MultiSubNetFailover.
* Se estiver a utilizar o LocalDB como base de dados para o seu servidor Azure AD Connect e tiver atingido o seu limite de tamanho de 10 GB, o Serviço de Sincronização já não começa. Anteriormente, é necessário executar a operação ShrinkDatabase no LocalDB para recuperar espaço DB suficiente para o Serviço de Sincronização começar. Depois disso, pode utilizar o Gestor de Serviços de Sincronização para eliminar o histórico de execução para recuperar mais espaço DB. Agora, pode usar o cmdlet Start-ADSyncPurgeRunHistory para expurgar os dados do histórico do LocalDB para recuperar o espaço DB. Além disso, este cmdlet suporta um modo offline (especificando o parâmetro offline) que pode ser utilizado quando o Serviço de Sincronização não está em funcionamento. Nota: O modo offline só pode ser utilizado se o Serviço de Sincronização não estiver em funcionamento e a base de dados utilizada for LocalDB.
* Para reduzir a quantidade de espaço de armazenamento necessário, o Azure AD Connect agora comprime os detalhes de erro de sincronização antes de os armazenar nas bases de dados LocalDB/SQL. Ao atualizar de uma versão mais antiga do Azure AD Connect para esta versão, o Azure AD Connect realiza uma compressão única nos detalhes de erro de sincronização existentes.
* Anteriormente, após a atualização da configuração de filtragem da U, deve executar manualmente a importação completa para garantir que os objetos existentes estão corretamente incluídos/excluídos da sincronização do diretório. Agora, o Azure AD Connect aciona automaticamente a importação total durante o próximo ciclo de sincronização. Além disso, a importação integral só é aplicada aos conectores AD afetados pela atualização. Nota: esta melhoria aplica-se às atualizações de filtragem da U feitas apenas utilizando o assistente Azure AD Connect. Não é aplicável à atualização de filtragem de OU feita utilizando o Gestor de Serviços de Sincronização.
* Anteriormente, a filtragem baseada em Grupo suporta apenas utilizadores, grupos e objetos de contacto. Agora, a filtragem baseada em grupo também suporta objetos de computador.
* Anteriormente, pode eliminar os dados do Connector Space sem desativar o programador de sincronização Azure AD Connect. Agora, o Gestor de Serviços de Sincronização bloqueia a eliminação dos dados do Espaço do Connector se detetar que o programador está ativado. Além disso, um aviso é devolvido para informar os clientes sobre a potencial perda de dados se os dados do espaço Connector forem eliminados.
* Anteriormente, deve desativar a transcrição do PowerShell para que o assistente Azure AD Connect seja executado corretamente. Esta questão está parcialmente resolvida. Pode ativar a transcrição do PowerShell se estiver a utilizar o assistente Azure AD Connect para gerir a configuração de sincronização. Deve desativar a transcrição do PowerShell se estiver a utilizar o assistente Azure AD Connect para gerir a configuração ADFS.



## <a name="114860"></a>1.1.486.0
Lançado: abril 2017

**Questões fixas:**
* Corrigiu o problema em que o Azure AD Connect não irá instalar com sucesso na versão localizada do Windows Server.

## <a name="114840"></a>1.1.484.0
Lançado: abril 2017

**Questões conhecidas:**

* Esta versão do Azure AD Connect não será instalada com sucesso se as seguintes condições forem todas verdadeiras:
   1. Está a realizar a atualização do DirSync no local ou a instalação fresca do Azure AD Connect.
   2. Está a utilizar uma versão localizada do Windows Server onde o nome do grupo de administrador incorporado no servidor não é "Administradores".
   3. Está a utilizar o SQL Server 2012 Express LocalDB instalado com o Azure AD Connect em vez de fornecer o seu próprio SQL completo.

**Questões fixas:**

Sincronização do Azure AD Connect
* Corrigiu um problema em que o programador de sincronização salta todo o passo de sincronização se um ou mais conectores estiverem a faltar ao perfil de execução para esse passo de sincronização. Por exemplo, adicionou manualmente um conector utilizando o Gestor de Serviços de Sincronização sem criar um perfil de execução Delta Import para o mesmo. Esta correção garante que o programador de sincronização continua a executar a Delta Import para outros conectores.
* Corrigiu um problema em que o Serviço de Sincronização deixa imediatamente de processar um perfil de execução quando se encontra um problema com uma das etapas de execução. Esta correção garante que o Serviço de Sincronização ignora esse passo e continua a processar o resto. Por exemplo, tem um perfil de execução Delta Import para o seu conector AD com vários passos de execução (um para cada domínio AD no local). O Serviço de Sincronização funcionará com a Delta Import com os outros domínios ad, mesmo que um deles tenha problemas de conectividade de rede.
* Corrigiu um problema que faz com que a atualização do Coneconctor Azure AD seja ignorada durante a Atualização Automática.
* Corrigiu um problema que faz com que o Azure AD Connect determine incorretamente se o servidor é um controlador de domínio durante a configuração, o que por sua vez faz com que a atualização do DirSync falhe.
* Corrigiu um problema que faz com que a atualização do DirSync no local não crie qualquer perfil de execução para o Conector AD Azure.
* Corrigiu um problema em que a interface de utilizador do Gestor de Serviços de Sincronização fica sem resposta ao tentar configurar o Conector LDAP genérico.

Gestão aD FS
* Corrigiu um problema em que o assistente Azure AD Connect falha se o nó primário AD FS tiver sido transferido para outro servidor.

Ambiente de trabalho SSO
* Corrigiu um problema no assistente de Ligação AD Azur, onde o ecrã De entrada não permite ativar a funcionalidade Desktop SSO se escolher a Sincronização de Passwords como opção De entrada em funcionamento durante a nova instalação.

**Novas funcionalidades/melhorias:**

Sincronização do Azure AD Connect
* O Azure AD Connect Sync suporta agora a utilização da Conta de Serviço Virtual, da Conta de Serviço Gerida e da Conta de Serviço Gerida pelo Grupo como conta de serviço. Isto aplica-se apenas à nova instalação do Azure AD Connect. Ao instalar o Azure AD Connect:
    * Por padrão, o assistente Azure AD Connect criará uma Conta de Serviço Virtual e utiliza-a como conta de serviço.
    * Se estiver a instalar um controlador de domínio, o Azure AD Connect recua para comportamentos anteriores onde irá criar uma conta de utilizador de domínio e a utilizar como conta de serviço.
    * Pode anular o comportamento predefinido fornecendo um dos seguintes:
      * Uma conta de serviço gerida pelo grupo
      * Uma conta de serviço gerida
      * Uma conta de utilizador de domínio
      * Uma conta de utilizador local
* Anteriormente, se atualizar para uma nova construção do Azure AD Connect contendo alterações de atualização ou regra de sincronização, o Azure AD Connect irá desencadear um ciclo de sincronização completo. Agora, o Azure AD Connect aciona seletivamente o passo de Importação Completa apenas para conectores com atualização, e passo de sincronização completa apenas para conectores com alterações de regra de sincronização.
* Anteriormente, o limiar de eliminação das exportações aplica-se apenas às exportações desencadeadas através do programador de sincronização. Agora, a funcionalidade é estendida para incluir exportações manualmente desencadeadas pelo cliente utilizando o Gestor de Serviços de Sincronização.
* No seu inquilino Azure AD, existe uma configuração de serviço que indica se a funcionalidade de Sincronização de Palavras-passe está ativada para o seu inquilino ou não. Anteriormente, é fácil para a configuração do serviço ser configurada incorretamente pelo Azure AD Connect quando tem um servidor ativo e um servidor de encenação. Agora, o Azure AD Connect tentará manter a configuração do serviço consistente apenas com o seu servidor Azure AD Connect ativo.
* O assistente azure AD Connect deteta e devolve um aviso se o AD no local não tiver a Caixa de Reciclagem AD ativada.
* Anteriormente, a exportação para a AD Azure tem tempo e falha se o tamanho combinado dos objetos no lote exceder determinado limiar. Agora, o Serviço de Sincronização tentará reenviar os objetos em lotes separados e menores se o problema for encontrado.
* A aplicação de Gestão de Chaves do Serviço de Sincronização foi removida do Menu De Arranque do Windows. A gestão da chave de encriptação continuará a ser suportada através da interface da linha de comando utilizando miiskmu.exe. Para obter informações sobre a gestão da chave de encriptação, consulte o artigo [Abandonando a chave de encriptação Azure AD Connect Sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-adsync-service-account-encryption-key).
* Anteriormente, se alterar a senha de serviço de sincronização Do Azure AD Connect, o Serviço de Sincronização não poderá começar corretamente até ter abandonado a chave de encriptação e reinicializado a senha de serviço de sincronização Do Azure AD Connect. Agora, este processo já não é necessário.

Ambiente de trabalho SSO

* O assistente Azure AD Connect já não necessita de a abertura da porta 9090 na rede ao configurar a autenticação pass-through e o SSO do Ambiente de Trabalho. Só é necessária a porta 443. 

## <a name="114430"></a>1.1.443.0
Lançado: março de 2017

**Questões fixas:**

Sincronização do Azure AD Connect
* Corrigiu um problema que faz com que o assistente azure AD Connect falhe se o nome de visualização do Conector AD Azure não contiver o domínio onmicrosoft.com inicial atribuído ao inquilino DaD Azure.
* Corrigiu um problema que faz com que o assistente de Ligação AD Do Azure falhe ao mesmo tempo que faz a ligação à base de dados SQL quando a palavra-passe da Conta de Serviço Sincronizado contém caracteres especiais como apóstrofe, cólon e espaço.
* Corrigiu um problema que causa o erro "A imagem tem uma âncora diferente da imagem" a ocorrer num servidor Azure AD Connect em modo de encenação, depois de ter excluído temporariamente um objeto AD no local de sincronização e, em seguida, incluiu-o novamente para sincronização.
* Corrigiu um problema que faz com que o erro "O objeto localizado pelo DN é um fantasma" ocorra num servidor Azure AD Connect no modo de preparação, depois de ter excluído temporariamente um objeto AD no local de sincronização e, em seguida, o incluiu novamente para sincronização.

Gestão aD FS
* Corrigiu um problema em que o assistente Azure AD Connect não atualiza a configuração Do DFS e define as reivindicações certas sobre a confiança da parte de confiança após a configuração do ID de Login Alternativo.
* Corrigiu um problema em que o assistente de Ligação Azure AD Connect não consegue manusear corretamente os servidores AD FS cujas contas de serviço estão configuradas utilizando o formato UserPrincipalName em vez do formato SAMAccountName.

Autenticação pass-through
* Corrigiu um problema que faz com que o assistente de Ligação AD Do Azure falhe se for selecionado o Passe Através da Autenticação, mas o registo do conector falha.
* Corrigiu um problema que faz com que o assistente de Ligação AD Do Azure ignore as verificações de validação no método de entrada selecionado quando a funcionalidade Desktop SSO estiver ativada.

Repor palavra-passe
* Corrigiu um problema que pode fazer com que o servidor Azure AAD Connect não tente voltar a ligar-se se a ligação for morta por uma firewall ou por procuração.

**Novas funcionalidades/melhorias:**

Sincronização do Azure AD Connect
* Get-ADSyncScheduler cmdlet agora devolve uma nova propriedade Boolean chamada SyncCycleInProgress. Se o valor devolvido for verdadeiro, significa que há um ciclo de sincronização programado em andamento.
* A pasta de destino para armazenar registos de instalação e configuração Azure AD Connect foi transferida de %localappdata%\AADConnect para %programdata%\AADConnect para melhorar a acessibilidade aos ficheiros de registo.

Gestão aD FS
* Suporte adicional para a atualização do Certificado AD FS Farm TLS/SSL.
* Suporte adicional para a gestão da AD FS 2016.
* Pode agora especificar o gMSA existente (Conta de Serviço Gerida pelo Grupo) durante a instalação AD FS.
* Agora pode configurar SHA-256 como algoritmo de hash de assinatura para a Confiança do Partido Azure AD.

Repor palavra-passe
* Introduziram melhorias para permitir que o produto funcione em ambientes com regras de firewall mais rigorosas.
* Melhor fiabilidade da ligação ao Ônibus de Serviço Azure.

## <a name="113800"></a>1.1.380.0
Lançado: dezembro de 2016

**Questão fixa:**

* Corrigiu a questão em que falta a regra de reclamação emitida para os Serviços da Federação de Diretórios Ativos (AD FS) nesta construção.

>[!NOTE]
>Esta construção não está disponível para os clientes através da funcionalidade azure AD Connect Auto Upgrade.

## <a name="113710"></a>1.1.371.0
Lançado: dezembro de 2016

**Known issue:**

* A regra de reivindicação emitida para a AD FS está em falta nesta construção. A regra de reclamação emitida é necessária se estiver a federar vários domínios com o Azure Ative Directory (Azure AD). Se estiver a utilizar o Azure AD Connect para gerir a sua implementação aD FS no local, a atualização para esta construção remove a regra de reclamação emitida existente da sua configuração AD FS. Pode contornar o problema adicionando a regra de reclamação emitida após a instalação/atualização. Para mais detalhes sobre a adição da regra de reclamação emitida, consulte este artigo sobre suporte de [domínio múltiplo para federação com AD Azure](how-to-connect-install-multiple-domains.md).

**Questão fixa:**

* Se o Porto 9090 não estiver aberto para a ligação de saída, a instalação ou atualização do Azure AD Connect falha.

>[!NOTE]
>Esta construção não está disponível para os clientes através da funcionalidade azure AD Connect Auto Upgrade.

## <a name="113700"></a>1.1.370.0
Lançado: dezembro de 2016

**Questões conhecidas:**

* A regra de reivindicação emitida para a AD FS está em falta nesta construção. A regra de reclamação emitida é necessária se estiver a federar vários domínios com a AD Azure. Se estiver a utilizar o Azure AD Connect para gerir a sua implementação aD FS no local, a atualização para esta construção remove a regra de reclamação emitida existente da sua configuração AD FS. Pode contornar o problema adicionando a regra de reclamação emitida após a instalação/atualização. Para mais detalhes sobre a adição da regra de reclamação emitida, consulte este artigo sobre suporte de [domínio múltiplo para federação com AD Azure](how-to-connect-install-multiple-domains.md).
* O porto 9090 deve estar aberto para a instalação completa.

**Novas funcionalidades:**

* Autenticação pass-through (Pré-visualização).

>[!NOTE]
>Esta construção não está disponível para os clientes através da funcionalidade azure AD Connect Auto Upgrade.

## <a name="113430"></a>1.1.343.0
Lançado: novembro de 2016

**Known issue:**

* A regra de reivindicação emitida para a AD FS está em falta nesta construção. A regra de reclamação emitida é necessária se estiver a federar vários domínios com a AD Azure. Se estiver a utilizar o Azure AD Connect para gerir a sua implementação aD FS no local, a atualização para esta construção remove a regra de reclamação emitida existente da sua configuração AD FS. Pode contornar o problema adicionando a regra de reclamação emitida após a instalação/atualização. Para mais detalhes sobre a adição da regra de reclamação emitida, consulte este artigo sobre suporte de [domínio múltiplo para federação com AD Azure](how-to-connect-install-multiple-domains.md).

**Questões fixas:**

* Por vezes, a instalação do Azure AD Connect falha porque não é capaz de criar uma conta de serviço local cuja palavra-passe satisfaz o nível de complexidade especificado pela política de passwords da organização.
* Fixa uma questão em que as regras de adesão não são reavaliadas quando um objeto no espaço do conector simultaneamente se torna fora de âmbito para uma regra de adesão e se torna no âmbito de outra. Isto pode acontecer se tiver duas ou mais regras de adesão cujas condições de adesão são mutuamente exclusivas.
* Uma questão em que as regras de sincronização de entrada (da Azure AD), que não contêm regras de adesão, não são processadas se tiverem valores de precedência mais baixos do que os que contêm regras de adesão.

**Melhorias:**

* Suporte adicional para instalar O Azure AD Connect no Windows Server 2016 Standard ou superior.
* Suporte adicionado para a utilização do SQL Server 2016 como base de dados remota para Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Lançado: agosto de 2016

**Questões fixas:**

* As alterações ao intervalo de sincronização só se realizam depois de concluído o próximo ciclo de sincronização.
* O assistente Azure AD Connect não aceita uma conta Azure\_AD cujo nome de utilizador começa com um sublinhado ( ).
* O assistente Azure AD Connect não autentica a conta Azure AD se a palavra-passe da conta contiver demasiados caracteres especiais. Mensagem de erro "Incapaz de validar credenciais. Ocorreu um erro inesperado." é devolvido.
* Desinstalar o servidor de encenação desativa a sincronização de passwords no inquilino da AD Azure e faz com que a sincronização da palavra-passe falhe com o servidor ativo.
* A sincronização da palavra-passe falha em casos incomuns quando não há hash de senha armazenada no utilizador.
* Quando o servidor Azure AD Connect está ativado para o modo de realização, a reescrita da palavra-passe não está temporariamente desativada.
* O assistente Azure AD Connect não mostra a sincronização real da palavra-passe e a configuração de redação de palavras-passe quando o servidor está no modo de preparação. Mostra-os sempre como deficientes.
* As alterações de configuração para a sincronização de palavras-passe e a redação de palavras-passe não são persistidas pelo assistente De Ligação AD Azur quando o servidor está no modo de preparação.

**Melhorias:**

* Atualizou o cmdlet Start-ADSyncSyncCycle para indicar se é capaz de iniciar com sucesso um novo ciclo de sincronização ou não.
* Acrescentou o cmdlet Stop-ADSyncSyncCycle para terminar o ciclo de sincronização e o funcionamento, que estão atualmente em curso.
* Atualizou o cmdlet Stop-ADSyncScheduler para terminar o ciclo de sincronização e o funcionamento, que estão atualmente em curso.
* Ao configurar [as extensões](how-to-connect-sync-feature-directory-extensions.md) de Diretório no assistente Azure AD Connect, o atributo AD Azure do tipo "Cadeia Teletex" pode agora ser selecionado.

## <a name="111890"></a>1.1.189.0
Lançado: junho de 2016

**Questões fixas e melhorias:**

* O Azure AD Connect pode agora ser instalado num servidor compatível com FIPS.
  * Para sincronização de palavra-passe, consulte Sincronização de [hash password e FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Corrigiu um problema em que um nome NetBIOS não podia ser resolvido para a FQDN no Conector de Diretório Ativo.

## <a name="111800"></a>1.1.180.0
Lançado: maio de 2016

**Novas funcionalidades:**

* Avisa e ajuda a verificar domínios se não o fez antes de executar o Azure AD Connect.
* Suporte adicional para [a Microsoft Cloud Germany](reference-connect-instances.md#microsoft-cloud-germany).
* Suporte adicional para a mais recente infraestrutura [em nuvem do Governo do Microsoft Azure](reference-connect-instances.md#microsoft-azure-government) com novos requisitos de URL.

**Questões fixas e melhorias:**

* Adicione filtragem ao Editor de Regras de Sincronização para facilitar a descoberta de regras de sincronização.
* Melhor desempenho ao apagar um espaço de conector.
* Corrigiu um problema quando o mesmo objeto foi apagado e adicionado na mesma execução (chamado eliminar/adicionar).
* Uma regra de sincronização desativada já não permite que os objetos e atributos incluídos incluam objetos e atributos na atualização de esquemas de upgrade ou de diretório.

## <a name="111300"></a>1.1.130.0
Lançado: abril 2016

**Novas funcionalidades:**

* Suporte acrescido para atributos de [vários valores às extensões de Diretório](how-to-connect-sync-feature-directory-extensions.md).
* Suporte adicional para mais variações de configuração para [upgrade automático](how-to-connect-install-automatic-upgrade.md) para ser considerado elegível para upgrade.
* Adicionei alguns cmdlets para [programador personalizado](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Lançado: março de 2016

**Questões fixas:**

* Certifique-se de que a instalação express não pode ser utilizada no Windows Server 2008 (pré-R2) porque a sincronização de palavras-passe não é suportada neste sistema operativo.
* O upgrade do DirSync com uma configuração de filtro personalizada não funcionou como esperado.
* Ao atualizar para uma versão mais recente e não houver alterações na configuração, não deve ser agendada uma importação/sincronização completa.

## <a name="111100"></a>1.1.110.0
Lançado: fevereiro de 2016

**Questões fixas:**

* A atualização de lançamentos anteriores não funciona se a instalação não estiver na pasta 'Ficheiros' de Programa predefinido.
* Se instalar e limpar Iniciar o processo de **sincronização** no final do assistente de instalação, executar o assistente de instalação uma segunda vez não ativará o programador.
* O programador não funciona como esperado em servidores onde o formato data/hora us-en não é usado. Também bloqueará `Get-ADSyncScheduler` a devolução dos tempos corretos.
* Se instalou um lançamento anterior do Azure AD Connect com AD FS como opção de entrada e atualização, não poderá voltar a executar o assistente de instalação.

## <a name="111050"></a>1.1.105.0
Lançado: fevereiro de 2016

**Novas funcionalidades:**

* [Funcionalidade de atualização automática](how-to-connect-install-automatic-upgrade.md) para clientes de definições express.
* Suporte para a administração global utilizando a Autenticação de Multi-Factors Azure e a Gestão de Identidade Privilegiada no assistente de instalação.
  * Tem de permitir que o seu https://secure.aadcdn.microsoftonline-p.com representante também permita que o tráfego se utilizar a Autenticação Multi-Factor.
  * Tem de https://secure.aadcdn.microsoftonline-p.com adicionar à sua lista de sites fidedignos para a autenticação multi-factor para funcionar corretamente.
* Deixe alterar o método de entrada do utilizador após a instalação inicial.
* Deixe [a filtragem de Domínio e U](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente de instalação. Isto também permite a ligação a florestas onde nem todos os domínios estão disponíveis.
* [O programador](how-to-connect-sync-feature-scheduler.md) está integrado no motor de sincronização.

**Funcionalidades promovidas desde a pré-visualização para GA:**

* [Reprodução do dispositivo.](how-to-connect-device-writeback.md)
* [Extensões de diretório.](how-to-connect-sync-feature-directory-extensions.md)

**Novas funcionalidades de pré-visualização:**

* O novo intervalo de ciclo de sincronização padrão é de 30 minutos. Costumava ser três horas para todos os lançamentos anteriores. Adiciona suporte para alterar o comportamento do [programador.](how-to-connect-sync-feature-scheduler.md)

**Questões fixas:**

* A página de domínios DNS de verificação nem sempre reconheceu os domínios.
* Solicita credenciais de administração de domínio ao configurar AD FS.
* As contas ad in-instalações não são reconhecidas pelo assistente de instalação se localizadas num domínio com uma árvore DNS diferente do domínio radicular.

## <a name="1091310"></a>1.0.9131.0
Lançado: dezembro de 2015

**Questões fixas:**

* A sincronização de passwords pode não funcionar quando muda as palavras-passe nos Serviços de Domínio do Diretório Ativo (AD DS), mas funciona quando define uma palavra-passe.
* Quando tiver um servidor proxy, a autenticação para a AD Azure pode falhar durante a instalação ou se uma atualização for cancelada na página de configuração.
* A atualização de um lançamento anterior do Azure AD Connect com uma instância completa do Servidor SQL falha se não for um administrador do sistema SQL Server (SA).
* A atualização de um lançamento anterior do Azure AD Connect com um Servidor SQL remoto mostra o erro "Incapaz de aceder à base de dados ADSync SQL".

## <a name="1091250"></a>1.0.9125.0
Lançado: novembro de 2015

**Novas funcionalidades:**

* Pode reconfigurar a AD FS para a confiança da AD Azure.
* Pode refrescar o esquema de Diretório Ativo e regenerar as regras de sincronização.
* Pode desativar uma regra de sincronização.
* Pode definir "AuthoritativeNull" como um novo literal numa regra de sincronização.

**Novas funcionalidades de pré-visualização:**

* [Azure AD Connect Health para sincronização](how-to-connect-health-sync.md).
* Suporte para sincronização de senha sincronização de [passwords do Azure AD Domain Services.](../user-help/active-directory-passwords-update-your-own-password.md)

**Novo cenário apoiado:**

* Suporta várias organizações de intercâmbio no local. Para mais informações, consulte [implantações híbridas com múltiplas florestas de Diretório Ativo.](https://docs.microsoft.com/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150))

**Questões fixas:**

* Problemas de sincronização de palavras-passe:
  * Um objeto deslocado de fora do alcance para o in-scope não terá a sua palavra-passe sincronizada. Isto inclui a filtragem de ou e atributos.
  * A seleção de um novo OU para incluir em sincronização não requer uma sincronização completa da palavra-passe.
  * Quando um utilizador desativado está ativado, a palavra-passe não sincroniza.
  * A fila de retry password é infinita e o limite anterior de 5.000 objetos a retirar foi removido.
* Não é possível ligar-se ao Ative Directory com o windows server 2016 nível funcional da floresta.
* Não é possível alterar o grupo utilizado para filtragem em grupo após a instalação inicial.
* Já não cria um novo perfil de utilizador no servidor Azure AD Connect para cada utilizador que faça uma alteração de palavra-passe com a redação de palavra-passe ativada.
* Não é capaz de usar valores Long Integer em âmbitos de regras de sincronização.
* A caixa de verificação "reescrita do dispositivo" permanece desativada se existirem controladores de domínio inacessíveis.

## <a name="1086670"></a>1.0.8667.0
Lançado: agosto de 2015

**Novas funcionalidades:**

* O assistente de instalação Azure AD Connect está agora localizado em todos os idiomas do Windows Server.
* Suporte adicional para desbloquear conta ao utilizar a gestão de passwords Azure AD.

**Questões fixas:**

* O assistente de instalação Azure AD Connect falha se outro utilizador continuar a instalação em vez da pessoa que iniciou a instalação pela primeira vez.
* Se uma desinstalação anterior do Azure AD Connect não conseguir desinstalar a sincronização Azure AD Connect de forma limpa, não é possível reinstalar.
* Não é possível instalar o Azure AD Connect utilizando a instalação Express se o utilizador não estiver no domínio raiz da floresta ou se for utilizada uma versão não inglesa do Ative Directory.
* Se o FQDN da conta de utilizador do Diretório Ativo não puder ser resolvido, é mostrada uma mensagem de erro enganosa "Falhou em cometer o esquema".
* Se a conta utilizada no Conector de Diretório Ativo for alterada for alterada fora do assistente, o assistente falha nas execuções subsequentes.
* O Azure AD Connect por vezes falha em instalar-se num controlador de domínio.
* Não é possível ativar e desativar o "modo de preparação" se tiverem sido adicionados atributos de extensão.
* A reescrita da palavra-passe falha em algumas configurações devido a uma má palavra-passe no Conector de Diretório Ativo.
* O DirSync não pode ser atualizado se for utilizado um nome distinto (DN) na filtragem do atributo.
* Utilização excessiva do CPU ao utilizar o reset da palavra-passe.

**Funcionalidades de pré-visualização removidas:**

* A funcionalidade de pré-visualização [A reutilização](how-to-connect-preview.md#user-writeback) do utilizador foi temporariamente removida com base no feedback dos nossos clientes de pré-visualização. Será acrescentado novamente mais tarde, depois de termos abordado o feedback fornecido.

## <a name="1086410"></a>1.0.8641.0
Lançado: junho de 2015

**Lançamento inicial do Azure AD Connect.**

Mudou de nome de Azure AD Sync para Azure AD Connect.

**Novas funcionalidades:**

* [Instalação de configurações expressas](how-to-connect-install-express.md)
* Pode [configurar AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* Pode [fazer upgrade a partir do DirSync](how-to-dirsync-upgrade-get-started.md)
* [Impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Modo [de encenação](how-to-connect-sync-staging-server.md) introduzido

**Novas funcionalidades de pré-visualização:**

* [Redação do utilizador](how-to-connect-preview.md#user-writeback)
* [Repetição de escrita do grupo](how-to-connect-preview.md#group-writeback)
* [Repetição de escrita do dispositivo](how-to-connect-device-writeback.md)
* [Extensões de diretórios](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Lançado: maio de 2015

**Novo requisito:**

* O Azure AD Sync necessita agora da versão .NET Framework 4.5.1 para ser instalada.

**Questões fixas:**

* A reescrita de palavra-passe da Azure AD está a falhar com um erro de conectividade azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Lançado: abril de 2015

**Questões fixas e melhorias:**

* O Conector de Diretório Ativo não processa corretamente se o recipiente de reciclagem estiver ativado e existirem vários domínios na floresta.
* O desempenho das operações de importação foi melhorado para o Conector de Diretório Ativo Azure.
* Quando um grupo excedeu o limite de adesão (por padrão, o limite é fixado para 50.000 objetos), o grupo foi eliminado no Diretório Ativo Azure. Com o novo comportamento, o grupo não é apagado, um erro é lançado, e novas mudanças de adesão não são exportadas.
* Um novo objeto não pode ser provisionado se um desacerto encenado com o mesmo DN já estiver presente no espaço do conector.
* Alguns objetos estão marcados para sincronização durante uma sincronização delta, mesmo que não haja alterações encenadas no objeto.
* Forçar uma sincronização de palavra-passe também remove a lista de DC preferida.
* O CSExportAnalyzer tem problemas com alguns estados de objetos.

**Novas funcionalidades:**

* Uma adesão pode agora ligar-se ao tipo de objeto "ANY" no MV.

## <a name="104850222"></a>1.0.485.0222
Lançado: fevereiro de 2015

**Melhorias:**

* Melhor desempenho das importações.

**Questões fixas:**

* Password Sync homenageia o atributo cloudFiltered que é usado por filtragem de atributos. Os objetos filtrados já não estão no âmbito da sincronização da palavra-passe.
* Em situações raras em que a topologia tinha muitos controladores de domínio, a sincronização de palavras-passe não funciona.
* "Stop-server" ao importar do Conector AD Azure após a gestão do dispositivo ter sido ativada em Azure AD/Intune.
* A adesão a Diretores de Segurança Estrangeira (FSPs) de vários domínios na mesma floresta causa um erro de união ambígua.

## <a name="104751202"></a>1.0.475.1202
Lançado: dezembro de 2014

**Novas funcionalidades:**

* A sincronização da palavra-passe com filtragem baseada em atributos é agora suportada. Para mais informações, consulte a [sincronização da palavra-passe com a filtragem](how-to-connect-sync-configure-filtering.md).
* O atributo ms-DS-ExternalDirectoryObjectID é reescrito para Ative Directory. Esta funcionalidade adiciona suporte para as aplicações do Office 365. Utiliza o OAuth2 para aceder a caixas de correio online e no local numa Implantação de Intercâmbio Híbrido.

**Problemas de atualização fixos:**

* Uma versão mais recente do assistente de inscrição está disponível no servidor.
* Foi utilizado um caminho de instalação personalizado para instalar o Azure AD Sync.
* Um critério de adesão personalizado inválido bloqueia a atualização.

**Outras correções:**

* Fixou os modelos para o Office Pro Plus.
* Problemas de instalação fixos causados por nomes de utilizador que começam com um traço.
* Fixando a definição de origemAnchor ao executar o assistente de instalação uma segunda vez.
* Rastreio fixo da ETW para sincronização de palavras-passe.

## <a name="104701023"></a>1.0.470.1023
Lançado: outubro de 2014

**Novas funcionalidades:**

* Sincronização de passwords de vários diretórios ativos no local para Azure AD.
* UI de instalação localizada em todos os idiomas do Servidor do Windows.

**Upgrade de AADSync 1.0 GA**

Se já tem o Azure AD Sync instalado, há um passo adicional que tens de dar caso tenhas alterado alguma das regras de sincronização fora da caixa. Depois de ter atualizado para o lançamento de 1.0.470.1023, as regras de sincronização que modificou são duplicadas. Para cada regra de sincronização modificada, faça o seguinte:

1. Localize a regra de sincronização que modificou e tome nota das alterações.
1. Apague a regra da sincronização.
1. Localize a nova regra de sincronização que é criada pelo Azure AD Sync e, em seguida, volte a aplicar as alterações.

**Permissões para a conta De Diretório Ativo**

A conta De Diretório Ativo deve ser concedida permissões adicionais para poder ler as hashes de palavra-passe do Diretório Ativo. As permissões para conceder são chamadas de "Replicating Directory Changes" e "Replicating Directory Changes All". Ambas as permissões são necessárias para poder ler as hashes de senha.

## <a name="104190911"></a>1.0.419.0911
Lançado: setembro de 2014

**Lançamento inicial do Azure AD Sync.**

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
