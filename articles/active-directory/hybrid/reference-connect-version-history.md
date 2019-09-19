---
title: 'Azure AD Connect: Histórico de lançamento de versão | Microsoft Docs'
description: Este artigo lista todas as versões de Azure AD Connect e Azure AD Sync
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce66c0239eee3f31695a942a586766694525fbad
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097601"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Histórico de versões
A equipe do Azure Active Directory (AD do Azure) atualiza regularmente Azure AD Connect com novos recursos e funcionalidades. Nem todas as adições são aplicáveis a todos os públicos.


Este artigo foi criado para ajudá-lo a acompanhar as versões que foram lançadas e para entender quais são as alterações na versão mais recente.

Esta tabela é uma lista de tópicos relacionados:

Tópico |  Detalhes
--------- | --------- |
Etapas para atualizar do Azure AD Connect | Métodos diferentes para [Atualizar de uma versão anterior para a versão mais recente do](how-to-upgrade-previous-version.md) Azure ad Connect.
Permissões obrigatórias | Para obter permissões necessárias para aplicar uma atualização, consulte [contas e permissões](reference-connect-accounts-permissions.md#upgrade).

Baixar | [Baixar Azure ad Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>A liberação de uma nova versão do Azure AD Connect é um processo que requer várias etapas de controle de qualidade para garantir a funcionalidade de operação do serviço e, enquanto passamos por esse processo, o número de versão de uma nova versão e o status da versão serão atualizados para refletir o estado mais recente.
Enquanto passarmos por esse processo, o número de versão da versão será mostrado com um "X" na posição do número de liberação secundária, como em "1.3. X. 0" – isso indica que as notas de versão deste documento são válidas para todas as versões que começam com "1,3.". Assim que finalizamos o processo de liberação, o número de versão de lançamento será atualizado para a versão lançada mais recentemente e o status da versão será atualizado para "liberado para download e atualização automática".
Nem todas as versões do Azure AD Connect serão disponibilizadas para atualização automática. O status da versão indicará se uma versão é disponibilizada para atualização automática ou somente para download. Se a atualização automática tiver sido habilitada em seu servidor de Azure AD Connect, esse servidor será atualizado automaticamente para a versão mais recente do Azure AD Connect que é lançado para atualização automática. Observe que nem todas as configurações de Azure AD Connect são elegíveis para a atualização automática. Siga este link para ler mais sobre a [atualização automática](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)

## <a name="14x0"></a>1.4. X. 0

>[!IMPORTANT]
>Anteriormente, os computadores de nível inferior do Windows que ingressaram no AD local estavam sendo sincronizados incorretamente para a nuvem em algumas circunstâncias. Por exemplo, o valor do atributo userCertificate para dispositivos de nível inferior do Windows no AD é populado. Mas esses dispositivos no Azure AD sempre ficam no estado "pendente" porque essas versões do sistema operacional não foram projetadas para serem registradas com o Azure AD via AAD Sync. Nesta versão do Azure AD Connect, AAD Sync interromperá a sincronização de computadores de nível inferior do Windows com o Azure AD e também removerá os dispositivos de nível inferior do Windows previamente sincronizados anteriormente do Azure AD. Observe que essa alteração não excluirá nenhum dispositivo de nível inferior do Windows registrado corretamente com o Azure AD usando o pacote MSI. Esses dispositivos continuarão a funcionar conforme o esperado para fins de acesso condicional com base no dispositivo. Alguns clientes podem ver que alguns ou todos os seus dispositivos de nível inferior do Windows desaparecem do Azure AD. Isso não é uma causa de preocupação, pois essas identidades de dispositivo nunca foram realmente usadas pelo Azure AD durante a autorização de acesso condicional. Esses clientes talvez precisem revisitar https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan e obter seus dispositivos de nível inferior do Windows registrados corretamente para garantir que esses dispositivos possam participar totalmente do acesso condicional baseado em dispositivo. Observe que, se você vir essas exclusões de objetos de computador/dispositivo de nível inferior no Azure AD excedendo o limite de exclusão de exportação, é recomendável que o cliente permita que essas exclusões passem.

### <a name="release-status"></a>Status da versão
9/10/2019: Liberado somente para atualização automática

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
- Novas ferramentas de solução de problemas ajudam a solucionar os cenários "usuário não sincronizando", "grupo não sincronizando" ou "membros do grupo não sincronizando".
- Adicionar suporte para nuvens nacionais em script de solução de problemas do AAD Connect 
- Os clientes devem ser informados de que os pontos de extremidade WMI preteridos para MIIS_Service foram removidos agora. Todas as operações de WMI agora devem ser feitas por meio de cmdlets do PS.
- Melhoria de segurança redefinindo a delegação restrita no objeto AZUREADSSOACC
- Ao adicionar/editar uma regra de sincronização, se houver qualquer atributo usado na regra que esteja no esquema do conector, mas não adicionado ao conector, os atributos adicionados automaticamente ao conector. O mesmo é verdadeiro para o tipo de objeto que a regra afeta. Se algo for adicionado ao conector, o conector será marcado para importação completa no próximo ciclo de sincronização.
- Não há mais suporte para o uso de um administrador corporativo ou de domínio como a conta do conector.
- No Gerenciador de sincronização, uma sincronização completa é executada na criação/edição/exclusão de regra. Um pop-up será exibido em qualquer alteração de regra notificando o usuário se a importação completa ou a sincronização completa for executada.
- Etapas de mitigação adicionadas para erros de senha à página ' conectores > Propriedades > conectividade '
- Foi adicionado um aviso de reprovação para o Gerenciador de serviços de sincronização na página de propriedades do conector. Esse aviso notifica o usuário de que as alterações devem ser feitas por meio do assistente de AADC.
- Adição de novo erro para problemas com a política de senha de um usuário.
- Impedir a configuração incorreta da filtragem de grupo por filtros de domínio e UO. A filtragem de grupo mostrará um erro quando o domínio/UO do grupo inserido já estiver filtrado e impedir que o usuário avance até que o problema seja resolvido.
- Os usuários não podem mais criar um conector para Active Directory Domain Services ou para o Windows Azure Active Directory na interface do usuário antiga.
- Correção da acessibilidade de controles de interface do usuário personalizados no Service Manager de sincronização
- Habilitamos seis tarefas de gerenciamento de Federação para todos os métodos de entrada no Azure AD Connect.  (Anteriormente, apenas a tarefa "atualização AD FS certificado SSL" estava disponível para todas as entradas.)
- Foi adicionado um aviso ao alterar o método de entrada de Federação para PHS ou PTA que todos os domínios e usuários do Azure AD serão convertidos para a autenticação gerenciada.
- Certificados de assinatura de token removidos da tarefa "redefinir o Azure AD e AD FS confiança" e adicionou uma subtarefa separada para atualizar esses certificados.
- Adicionada uma nova tarefa de gerenciamento de Federação chamada "gerenciar certificados" que tem subtarefas para atualizar o SSL ou certificados de assinatura de token para o farm de AD FS.
- Adicionada uma nova subtarefa de gerenciamento de Federação chamada "especificar servidor primário", que permite aos administradores especificar um novo servidor primário para o farm de AD FS.
- Adicionada uma nova tarefa de gerenciamento de Federação chamada "gerenciar servidores" que tem subtarefas para implantar um servidor de AD FS, implantar um servidor de proxy de aplicativo Web e especificar o servidor primário.
- Adicionada uma nova tarefa de gerenciamento de Federação chamada "Exibir configuração da Federação" que exibe as configurações de AD FS atuais.  (Por causa dessa adição, as configurações de AD FS foram removidas da página "examinar sua solução".)

### <a name="fixed-issues"></a>Problemas corrigidos
- Problema de erro de sincronização resolvido para o cenário em que um objeto de usuário assumindo seu objeto de contato correspondente tem uma autoreferência (por exemplo, o usuário é seu próprio gerente).
- Os pop-ups da ajuda agora mostram o foco do teclado.
- Para a atualização automática, se algum aplicativo conflitante estiver sendo executado de 6 horas, encerre-o e continue com a atualização.
- Limite o número de atributos que um cliente pode selecionar para 100 por objeto ao selecionar extensões de diretório. Isso impedirá que o erro ocorra durante a exportação, pois o Azure tem um máximo de 100 atributos de extensão por objeto.
- Correção de um bug para tornar o script de conectividade do AD mais robusto
- Foi corrigido um bug para fazer com que o AADConnect seja instalado em um computador usando um serviço WCF de pipes nomeados existente mais robusto.
- Diagnóstico aprimorado e solução de problemas em relação a diretivas de grupo que não permitem que o serviço ADSync seja iniciado quando inicialmente instalado.
- Corrigido um bug em que o nome de exibição de um computador com Windows foi gravado incorretamente.
- Corrija um bug em que o tipo de so para um computador Windows foi gravado incorretamente.
- Corrigido um bug em que computadores não Windows 10 estavam sincronizando inesperadamente. Observe que o efeito dessa alteração é que computadores não Windows-10 que foram previamente sincronizados agora serão excluídos. Isso não afeta nenhum recurso, pois a sincronização de computadores com Windows é usada somente para ingresso no domínio híbrido do Azure AD, que funciona apenas para dispositivos Windows-10. 
- Corrija um bug em que o nome de exibição de um computador com Windows foi gravado incorretamente.
- Corrija um bug em que o tipo de so para um computador Windows foi gravado incorretamente.
- Foram adicionados vários cmdlets (internos) novos ao módulo do PowerShell do ADSync.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Há um problema conhecido com a atualização de Azure AD Connect de uma versão anterior para 1.3.21.0 em que o portal do O365 não reflete a versão atualizada, mesmo que Azure AD Connect atualizado com êxito.
>
> Para resolver isso, você precisa importar o módulo **AdSync** e, em seguida`Set-ADSyncDirSyncConfiguration` , executar o cmdlet do PowerShell no servidor de Azure ad Connect.  Você pode usar as seguintes etapas:
>
>1. Abrir o PowerShell no modo administrador
>2. Execute `Import-Module "ADSync"`
>3. Execute `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`
 
### <a name="release-status"></a>Status da versão 

05/14/2019: Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos 

- Correção de uma elevação de vulnerabilidade de privilégio existente no Microsoft Azure Active Directory Connect Build 1.3.20.0.  Essa vulnerabilidade, em determinadas condições, pode permitir que um invasor execute dois cmdlets do PowerShell no contexto de uma conta com privilégios e execute ações privilegiadas.  Essa atualização de segurança resolve o problema desabilitando esses cmdlets. Para obter mais informações, consulte [atualização de segurança](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1000).

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Status da versão 

04/24/2019: Liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 

- Adicionar suporte para atualização de domínio 
- O recurso de pastas públicas do Exchange mail vai para GA 
- Melhorar o tratamento de erros do assistente para falhas de serviço 
- Adicionado link de aviso para interface do usuário antiga na página Propriedades do conector. 
- O recurso de write-back de grupos unificados agora está disponível 
- Mensagem de erro SSPR aprimorada quando o controlador de domínio está sem um controle LDAP 
- Foram adicionados diagnósticos para erros de registro DCOM durante a instalação  
- Rastreamento aprimorado de erros de RPC PHS 
- Permitir creds de EA de um domínio filho 
- Permitir que o nome do banco de dados seja inserido durante a instalação (nome padrão ADSync)
- Atualizar para a ADAL 3.19.8 para obter uma correção de WS-Trust para ping e adicionar suporte para novas instâncias do Azure 
- Modificar regras de sincronização de grupo para o Flow samAccountName, DomainNetbios e DomainFQDN para a nuvem-necessário para declarações 
- Manipulação de regra de sincronização padrão modificada – Leia mais [aqui](how-to-connect-fix-default-rules.md).
- Adicionado um novo agente em execução como um serviço do Windows. Esse agente, chamado "agente de administração", permite um diagnóstico remoto mais profundo do Azure AD Connect Server para ajudar os engenheiros da Microsoft a solucionar problemas ao abrir um caso de suporte. Esse agente não é instalado e habilitado por padrão.  Para obter mais informações sobre como instalar e habilitar o agente, consulte o [que é o agente de administração do Azure ad Connect?](whatis-aadc-admin-agent.md). 
- Contrato de licença de usuário final atualizado (EULA) 
- Adicionado suporte de atualização automática para implantações que usam AD FS como seu tipo de logon.  Isso também removeu a necessidade de atualizar o AD FS confiança de terceira parte confiável do Azure AD como parte do processo de atualização. 
- Adicionada uma tarefa de gerenciamento de confiança do Azure AD que fornece duas opções: analisar/atualizar confiança e redefinir confiança. 
- Alterou o AD FS comportamento de confiança de terceira parte confiável do Azure AD para que ele sempre use a opção-SupportMultipleDomain (inclui confiança e atualizações de domínio do Azure AD). 
- Foi alterado o comportamento instalar novo farm de AD FS para que ele exija um certificado. pfx removendo a opção de usar um certificado pré-instalado.
- Atualizado o fluxo de trabalho instalar novo AD FS farm para que ele permita apenas a implantação de 1 AD FS e um servidor WAP.  Todos os servidores adicionais serão feitos após a instalação inicial. 

### <a name="fixed-issues"></a>Problemas corrigidos 

- Corrigir a lógica de reconexão do SQL para o serviço ADSync 
- Correção para permitir a instalação limpa usando um SQL AOA DB vazio 
- Corrigir o script de permissões do PS para refinar as permissões GWB 
- Corrigir erros do VSS com o LocalDB  
- Corrigir mensagem de erro enganosa quando o tipo de objeto não está no escopo 
- Correção de um problema em que a instalação do PowerShell do Azure AD em um servidor poderia potencialmente causar um conflito de assembly com Azure AD Connect. 
- Corrigido o bug PHS no servidor de preparo quando as credenciais do conector são atualizadas na interface do usuário antiga. 
- Correção de alguns vazamentos de memória 
- Correções de atualização automática diversas 
- Correções diversas para exportar e não confirmado o processamento de importação 
- Correção de um bug com tratamento de uma barra invertida no domínio e filtragem de UO 
- Corrigido um problema em que o serviço ADSync leva mais de 2 minutos para parar e causa um problema no momento da atualização. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Status da versão

12/18/2018: Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos

Esse Build atualiza os conectores não padrão (por exemplo, conector LDAP genérico e conector do SQL genérico) fornecidos com Azure AD Connect. Para obter mais informações sobre os conectores aplicáveis, consulte a versão 1.1.911.0 no [histórico de lançamento de versão do conector](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Status da versão
12/11/2018: Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos
Essa compilação de hotfix permite que o usuário selecione um domínio de destino, dentro da floresta especificada, para o contêiner RegisteredDevices ao habilitar o Write-back de dispositivo.  Nas versões anteriores que contêm a nova funcionalidade de opções de dispositivo (1.1.819.0 – 1.2.68.0), o local do contêiner RegisteredDevices era limitado à raiz da floresta e não permitia domínios filho.  Essa limitação se manifesta apenas em novas implantações – as atualizações in-loco não foram afetadas.  

Se qualquer compilação contendo a funcionalidade atualizada de opções de dispositivo tiver sido implantada em um novo servidor e o Write-back de dispositivo tiver sido habilitado, você precisará especificar manualmente o local do contêiner se não quiser fazê-lo na raiz da floresta.  Para fazer isso, você precisa desabilitar o Write-back do dispositivo e habilitá-lo novamente, o que permitirá que você especifique o local do contêiner na página "floresta de write-back".



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Status da versão 

11/30/2018:  Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos

Essa compilação de hotfix corrige um conflito em que pode ocorrer um erro de autenticação devido à presença independente do módulo de Galeria do PowerShell MSOnline no servidor de sincronização.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Status da versão 

11/19/2018:  Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos

Essa compilação de hotfix corrige uma regressão na compilação anterior em que o Write-back de senha falha ao usar um controlador de domínio adicionado no Windows Server 2008/R2.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Status da versão 

10/25/2018: liberado para download

 
### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 


- Alteração da funcionalidade do write-back de atributo para garantir que o email de voz hospedado esteja funcionando conforme o esperado.  Em determinados cenários, o Azure AD estava substituindo o atributo msExchUcVoicemailSettings durante o Write-back com um valor nulo.  O Azure AD agora não limpará mais o valor local desse atributo se o valor de nuvem não estiver definido.
- Diagnósticos adicionados no assistente de Azure AD Connect para investigar e identificar problemas de conectividade com o Azure AD. Esses mesmos diagnósticos também podem ser executados diretamente por meio do PowerShell usando o cmdlet Test-AdSyncAzureServiceConnectivity. 
- Foram adicionados diagnósticos no assistente de Azure AD Connect para investigar e identificar problemas de conectividade com o AD. Esses mesmos diagnósticos também podem ser executados diretamente por meio do PowerShell usando a função start-ConnectivityValidation no módulo ADConnectivityTools PowerShell.  Para obter mais informações, consulte [o que é o módulo do PowerShell do ADConnectivityTool?](how-to-connect-adconnectivitytools.md)
- Adicionada uma verificação de versão do esquema do AD para União híbrida Azure Active Directory e Write-back do dispositivo 
- Alteração da pesquisa de atributo de página de extensão de diretório para não diferenciar maiúsculas de minúsculas.
-   Adicionado suporte completo para TLS 1,2. Esta versão dá suporte a todos os outros protocolos que estão sendo desabilitados e somente o TLS 1,2 está habilitado no computador em que o Azure AD Connect está instalado.  Para obter mais informações, consulte [imposição de TLS 1,2 para Azure ad Connect](reference-connect-tls-enforcement.md)

 

### <a name="fixed-issues"></a>Problemas corrigidos   

- Corrigido um bug em que Azure AD Connect atualização falharia se o SQL Always On estava sendo usado. 
- Correção de um bug para analisar corretamente nomes de UO que contêm uma barra. 
- Corrigido um problema em que a autenticação de passagem seria desabilitada para uma instalação limpa no modo de preparo. 
- Correção de um bug que impedia o carregamento do módulo do PowerShell ao executar as ferramentas de solução de problemas 
- Correção de um bug que impediria os clientes de usar valores numéricos no primeiro caractere de um nome de host. 
- Correção de um bug em que Azure AD Connect permitiria partições inválidas e seleção de contêiner 
- Correção da mensagem de erro "senha inválida" quando o SSO da área de trabalho está habilitado. 
- Várias correções de bugs para AD FS gerenciamento de confiança  
- Ao configurar o Write-back de dispositivo-corrigido a verificação de esquema para procurar a classe de objeto msDs-DeviceContainer (introduzida no WS2012 R2)

 
## <a name="118820"></a>1.1.882.0  

9/7/2018: liberado para download, não será liberado para atualização automática 

### <a name="fixed-issues"></a>Problemas corrigidos  

A atualização do Azure AD Connect falhará se a disponibilidade do SQL Always On estiver configurada para o ADSync DB. Esse hotfix aborda esse problema e permite que a atualização seja realizada com sucesso. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Status da versão

8/21/2018: Liberado para download e atualização automática. 

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

- A integração de agrupamento de ping no Azure AD Connect agora está disponível para disponibilidade geral. [Saiba mais sobre como federar o Azure AD com o ping Federate](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- O Azure AD Connect agora cria o backup de confiança do Azure AD em AD FS sempre que uma atualização é feita e a armazena em um arquivo separado para facilitar a restauração, se necessário. [Saiba mais sobre a nova funcionalidade e o gerenciamento de confiança do Azure AD em Azure ad Connect](https://aka.ms/fedtrustinaadconnect).
- Novas ferramentas de solução de problemas ajudam a solucionar problemas de alteração de endereço de email primário e ocultar a conta da lista de endereços global
- Azure AD Connect foi atualizado para incluir o último cliente nativo do SQL Server 2012
- Quando você alterna a entrada do usuário para a sincronização de hash de senha ou autenticação de passagem na tarefa "alterar entrada do usuário", a caixa de seleção logon único contínuo é habilitada por padrão.
- Suporte adicionado para o Windows Server Essentials 2019
- O agente de Azure AD Connect Health foi atualizado para a versão mais recente 3.1.7.0
- Durante uma atualização, se o instalador detectar alterações nas regras de sincronização padrão, será solicitado que o administrador receba um aviso antes de substituir as regras modificadas. Isso permitirá que o usuário execute ações corretivas e retome-as posteriormente. Comportamento antigo: Se houvesse alguma regra pronta para uso modificada, a atualização manual estava substituindo essas regras sem fornecer nenhum aviso ao usuário e o Agendador de sincronização foi desabilitado sem informar o usuário. Novo comportamento: O usuário receberá uma solicitação com um aviso antes de substituir as regras de sincronização prontas para uso. O usuário terá a opção de interromper o processo de atualização e continuar mais tarde depois de tomar uma ação corretiva.
- Forneça uma melhor manipulação de um problema de conformidade com FIPS, fornecendo uma mensagem de erro para geração de hash MD5 em um ambiente compatível com FIPS e um link para a documentação que fornece uma solução alternativa para esse problema.
- Atualização da interface do usuário para melhorar as tarefas de Federação no assistente, que agora estão em um subgrupo separado para Federação. 
- Todas as tarefas adicionais da Federação agora estão agrupadas em um único submenu para facilitar o uso.
- Um novo módulo ADSyncConfig Posh remodelado (AdSyncConfig. psm1) com novas funções de permissões de AD movidas do antigo ADSyncPrep. psm1 (que pode ser preterido em breve)

### <a name="fixed-issues"></a>Problemas corrigidos 

- Correção de um bug em que o servidor do AAD Connect mostraria um alto uso da CPU após a atualização para o .NET 4.7.2
- Correção de um bug que produziria intermitentemente uma mensagem de erro para um problema de deadlock do SQL resolvido automaticamente
- Correção de vários problemas de acessibilidade para o editor de regras de sincronização e a Service Manager de sincronização  
- Correção de um bug em que Azure AD Connect não pode obter informações de configuração do registro
- Correção de um bug que criou problemas quando o usuário avança/volta no assistente
- Correção de um bug para evitar um erro ocorrido devido a uma entrega incorreta de vários threads no assistente
- Quando a página de filtragem de sincronização de grupo encontra um erro LDAP ao resolver grupos de segurança, Azure AD Connect agora retorna a exceção com fidelidade total.  A causa raiz da exceção de referência ainda é desconhecida e será resolvida por um bug diferente.
-  Correção de um bug em que as permissões para as chaves STK e NGC (atributo ms-DS-KeyCredentialLink em objetos de usuário/dispositivo para WHfB) não foram definidas corretamente.     
- Correção de um bug em que ' Set-ADSyncRestrictedPermissions ' não foi chamado corretamente
-  Adicionando suporte para permissão de concessão no grupo write-back no assistente de instalação do AADConnect
- Ao alterar o método de entrada da sincronização de hash de senha para AD FS, a sincronização de hash de senha não foi desabilitada.
- Adicionada a verificação para endereços IPv6 na configuração do AD FS
- A mensagem de notificação foi atualizada para informar que existe uma configuração existente.
- Falha do write-back do dispositivo ao detectar o contêiner na floresta não confiável. Isso foi atualizado para fornecer uma mensagem de erro melhor e um link para a documentação apropriada
- Desmarcar uma UO e, em seguida, a sincronização/write-back correspondente a essa UO fornece um erro de sincronização genérico. Isso foi alterado para criar uma mensagem de erro mais compreensível.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Status da versão

5/14/2018: Lançado para atualização automática e download.

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

Novos recursos e aprimoramentos

- Esta versão inclui a visualização pública da integração do PingFederate no Azure AD Connect. Com esta versão, os clientes podem facilmente e configurar de forma confiável seu ambiente de Azure Active Directory para aproveitar o PingFederate como seu provedor de Federação. Para saber mais sobre como usar esse novo recurso, visite nossa [documentação online](plan-connect-user-signin.md#federation-with-pingfederate). 
- Atualizado o utilitário de solução de problemas do assistente de Azure AD Connect, em que agora ele analisa mais cenários de erro, como caixas de correio vinculadas e grupos dinâmicos do AD. Leia mais sobre o utilitário de solução de problemas [aqui](tshoot-connect-objectsync.md).
- A configuração de write-back de dispositivo agora é gerenciada somente no assistente de Azure AD Connect.
- Um novo módulo do PowerShell chamado ADSyncTools. psm1 é adicionado e pode ser usado para solucionar problemas de conectividade do SQL e vários outros utilitários de solução de problemas. Leia mais sobre o módulo ADSyncTools [aqui](tshoot-connect-tshoot-sql-connectivity.md). 
- Uma nova tarefa adicional "configurar opções de dispositivo" foi adicionada. Você pode usar a tarefa para configurar as duas operações a seguir: 
  - **Ingresso no Azure ad híbrido**: Se o seu ambiente tiver uma infraestrutura do AD no local e também quiser tirar partido das capacidades que o Azure Active Directory oferece, pode implementar dispositivos híbridos associados ao Azure AD. Estes são dispositivos associados tanto ao Active Directory no local, como ao Azure Active Directory.
  - **Write-back do dispositivo**: O Write-back do dispositivo é usado para habilitar o acesso condicional com base em dispositivos para AD FS (2012 R2 ou superior) dispositivos protegidos

    >[!NOTE] 
    > - A opção para habilitar o Write-back de dispositivo de personalizar opções de sincronização ficará esmaecida. 
    > -  O módulo do PowerShell para ADPrep foi preterido nesta versão.



### <a name="fixed-issues"></a>Problemas corrigidos 

- Esta versão atualiza a instalação do SQL Server Express para SQL Server 2012 SP4, que, entre outros, fornece correções para várias vulnerabilidades de segurança.  Consulte [aqui](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) para obter mais informações sobre o SQL Server 2012 SP4.
- Processamento de regra de sincronização: as regras de sincronização de junção de saída sem nenhuma condição de junção devem ser reaplicadas se a regra de sincronização pai não for mais aplicável
- Várias correções de acessibilidade foram aplicadas à interface do usuário do Synchronization Service Manager e ao editor de regras de sincronização
- Assistente de Azure AD Connect: Erro ao criar a conta do conector do AD quando Azure AD Connect está em um grupo de trabalho
- Assistente de Azure AD Connect: Na página de entrada do Azure AD, exiba a caixa de seleção de verificação sempre que houver incompatibilidade nos domínios do AD e nos domínios verificados do Azure AD
- Atualização automática do PowerShell Fix para definir o estado de atualização automática corretamente em determinados casos após a tentativa de atualização automática.
- Assistente de Azure AD Connect: Telemetria atualizada para capturar informações ausentes anteriormente
- Assistente de Azure AD Connect: As seguintes alterações foram feitas quando você usa a tarefa **alterar entrada do usuário** para mudar de AD FS para autenticação de passagem:
    - O agente de autenticação de passagem está instalado no servidor de Azure AD Connect e o recurso de autenticação de passagem está habilitado, antes de converter domínios de federado para gerenciado.
    - Os usuários não são mais convertidos de federado para gerenciado. Somente domínio (s) são convertidos.
- Assistente de Azure AD Connect: AD FS Regex de vários domínios não está correto quando o UPN do usuário tem a atualização de Regex de caractere especial para dar suporte a caracteres especiais
- Assistente de Azure AD Connect: Remover a mensagem "configurar atributo de âncora de origem" falsa quando nenhuma alteração 
- Assistente de Azure AD Connect: Suporte AD FS para o cenário de Federação dupla
- Assistente de Azure AD Connect: AD FS declarações não são atualizadas para o domínio adicionado ao converter um domínio gerenciado para federado
- Assistente de Azure AD Connect: Durante a detecção de pacotes instalados, encontramos produtos relacionados ao DirSync/Azure AD Sync/Azure AD Connect. Agora, tentaremos desinstalar os produtos obsoletos.
- Assistente de Azure AD Connect: Corrigir o mapeamento de mensagem de erro quando a instalação do agente de autenticação de passagem falhar
- Assistente de Azure AD Connect: Contêiner de "configuração" removido da página de filtragem da UO do domínio
- Instalação do mecanismo de sincronização: Remova a lógica herdada desnecessária que ocasionalmente falhou no MSI de instalação do mecanismo de sincronização
- Assistente de Azure AD Connect: Corrigir o texto de ajuda pop-up na página de recursos opcionais para sincronização de hash de senha
- Tempo de execução do mecanismo de sincronização: Corrija o cenário em que um objeto CS tem uma exclusão importada e regras de sincronização tentam reprovisionar o objeto.
- Tempo de execução do mecanismo de sincronização: Adicionar link de ajuda para o guia de solução de problemas de conectividade online ao log de eventos para obter um erro de importação
- Tempo de execução do mecanismo de sincronização: Redução do uso de memória do Agendador de sincronização ao enumerar conectores
- Assistente de Azure AD Connect: Corrigir um problema ao resolver uma conta de serviço de sincronização personalizada que não tem privilégios de leitura do AD
- Assistente de Azure AD Connect: Melhorar o log de seleções de filtragem de domínio e de UO
- Assistente de Azure AD Connect: AD FS adicionar declarações padrão à confiança de Federação criada para o cenário de MFA
- Assistente de Azure AD Connect: AD FS implantar WAP: A adição do servidor falha ao usar o novo certificado
- Assistente de Azure AD Connect: Exceção de DSSO quando onPremCredentials não é inicializado para um domínio 
- Flua preferencialmente o atributo distinguishedName do AD do objeto de usuário ativo.
- Um bug superficial foi corrigido. a precedência da primeira regra de sincronização OOB foi definida como 99 em vez de 100



## <a name="117510"></a>1.1.751.0
Status 4/12/2018: Liberado somente para download

>[!NOTE]
>Esta versão é um hotfix para Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Sincronização Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
Corrigido um problema foi a descoberta automática da instância do Azure para locatários da China ocasionalmente falhava.  

### <a name="ad-fs-management"></a>Gerenciamento de AD FS
#### <a name="fixed-issues"></a>Problemas corrigidos

Houve um problema na lógica de repetição de configuração que resultaria em uma ArgumentException informando "um item com a mesma chave já foi adicionado".  Isso faria com que todas as operações de repetição falhassem.

## <a name="117500"></a>1.1.750.0
Status 3/22/2018: Liberado para atualização e download automáticos.
>[!NOTE]
>Quando a atualização para essa nova versão for concluída, ela disparará automaticamente uma sincronização completa e uma importação completa para o Azure AD Connector e uma sincronização completa para o AD Connector. Como isso pode levar algum tempo, dependendo do tamanho do seu ambiente de Azure AD Connect, verifique se você executou as etapas necessárias para dar suporte a isso ou aguarde a atualização até encontrar um momento conveniente para fazer isso.

>[!NOTE]
>"A funcionalidade de atualização automática foi desabilitada incorretamente para alguns locatários que implantaram compilações posteriores a 1.1.524.0. Para garantir que sua instância de Azure AD Connect ainda esteja qualificada para atualização autoatualizada, execute o seguinte cmdlet do PowerShell: "Set-ADSyncAutoUpgrade-autoupgradestate habilitado"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos

* O cmdlet Set-ADSyncAutoUpgrade bloquearia a atualização automática anteriormente se o estado de atualização automático estiver definido como suspenso. Essa funcionalidade foi alterada para que não bloqueie a atualização autoatualizada de compilações futuras.
* Alterada a opção de página de **entrada do usuário** "sincronização de senha" para "sincronização de hash de senha".  Azure AD Connect sincroniza hashes de senha, não senhas, portanto, isso se alinha com o que está realmente ocorrendo.  Para obter mais informações, consulte [implementar a sincronização de hash de senha com a sincronização de Azure ad Connect](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Estado Liberado para selecionar clientes

>[!NOTE]
>Quando a atualização para essa nova versão for concluída, ela disparará automaticamente uma sincronização completa e uma importação completa para o Azure AD Connector e uma sincronização completa para o AD Connector. Como isso pode levar algum tempo, dependendo do tamanho do seu ambiente de Azure AD Connect, verifique se você executou as etapas necessárias para dar suporte a isso ou aguarde a atualização até encontrar um momento conveniente para fazer isso.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigi a janela de tempo em tarefas em segundo plano para a página de filtragem de partições quando mudar para a página seguinte.

* Foi corrigido um erro que provocou a violação de acesso durante a ação personalizada ConfigDB.

* Correção de um bug para recuperar do tempo limite da conexão SQL.

* Corrigido um bug em que os certificados com curingas de SAN falharam em uma verificação de pré-requisito.

* Correção de um bug que faz com que o MIIServer. exe falhe durante uma exportação do Azure AD Connector.

* Foi corrigido um bug cuja senha incorreta tentou fazer logon no DC ao executar o assistente de Azure AD Connect para alterar a configuração.


#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Adicionar configurações de privacidade para o Regulamento Geral sobre a Proteção de Dados (GDPR).  Para obter mais informações, consulte o artigo [aqui](reference-connect-user-privacy.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* telemetria de aplicativos-o administrador pode alternar entre/desativar essa classe de dados às

* Dados de integridade do Azure AD-o administrador deve visitar o portal de integridade para controlar suas configurações de integridade.
   Assim que a política do serviço tiver sido alterada, os agentes serão ler e impor-lo.

* Adição de ações de configuração de write-back do dispositivo e uma barra de progresso para inicialização de página

* Diagnóstico geral aprimorado com relatório HTML e coleta de dados completa em um relatório de texto ZIP/HTML

* Melhoria da confiabilidade da atualização automática e adição de telemetria adicional para garantir que a integridade do servidor possa ser determinada

* Restringir permissões disponíveis para contas com privilégios na conta do conector do AD

  * Para novas instalações, o assistente restringirá as permissões que as contas privilegiadas têm na conta MSOL depois de criar a conta MSOL.

As alterações se encarregarão do seguinte:
1. Instalações expressas
2. Instalações personalizadas com conta de criação automática
3. O instalador foi alterado para que ele não exija privilégio de SA na instalação limpa do Azure AD Connect

* Foi adicionado um novo utilitário para solucionar problemas de sincronização de um objeto específico. Ele está disponível na opção "solucionar problemas de sincronização de objetos" do assistente de Azure AD Connect solucionar problemas de tarefa adicional. Atualmente, o utilitário verifica o seguinte:

  * Erro de correspondência de UserPrincipalName entre a conta de utilizador no inquilino do Azure AD e o objeto de utilizador sincronizado.
  * Se o objeto é filtrado em sincronização devido a filtragem de domínio
  * Se o objeto é filtrado em sincronização devido a unidade organizacional (UO), filtragem

* Foi adicionado um novo utilitário para sincronizar o hash de senha atual armazenado no Active Directory local para uma conta de usuário específica.

O utilitário não requer uma alteração de palavra-passe. Ele está disponível na opção ' solucionar a sincronização de hash de senha ' do assistente de Azure AD Connect solucionar problemas de tarefa adicional.






## <a name="116540"></a>1.1.654.0
Estado 12 de dezembro de 2017

>[!NOTE]
>Esta versão é um hotfix relacionado à segurança para Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
Foi adicionado um aperfeiçoamento à Azure AD Connect versão 1.1.654.0 (e posterior) para garantir que as alterações de permissão recomendadas descritas em seção [bloquear o acesso à conta de AD DS](#lock) sejam aplicadas automaticamente quando Azure ad Connect criar o AD DS considerar. 

- Ao configurar Azure AD Connect, o administrador de instalação pode fornecer uma conta de AD DS existente ou permitir que Azure AD Connect crie automaticamente a conta. As alterações de permissão são aplicadas automaticamente à conta de AD DS que é criada pelo Azure AD Connect durante a instalação. Eles não são aplicados à conta de AD DS existente fornecida pelo administrador de instalação.
- Para os clientes que atualizaram de uma versão mais antiga do Azure AD Connect para 1.1.654.0 (ou posterior), as alterações de permissão não serão retroativamente aplicadas às contas existentes do AD DS criadas antes da atualização. Eles serão aplicados somente a novas contas de AD DS criadas após a atualização. Isso ocorre quando você está adicionando novas florestas do AD para serem sincronizadas com o Azure AD.

>[!NOTE]
>Esta versão remove apenas a vulnerabilidade para novas instalações do Azure AD Connect em que a conta de serviço é criada pelo processo de instalação. Para instalações existentes, ou em casos em que você mesmo fornece a conta, certifique-se de que essa vulnerabilidade não existe.

#### <a name="lock"></a>Bloquear o acesso à conta de AD DS
Bloqueie o acesso à conta de AD DS implementando as seguintes alterações de permissão no AD local:  

*   Desabilitar herança no objeto especificado
*   Remova todas as ACEs no objeto específico, exceto ACEs específicas para si mesma. Queremos manter as permissões padrão intactas quando se trata de si mesma.
*   Atribua estas permissões específicas:

Type     | Name                          | Aceder               | Aplica-se A
---------|-------------------------------|----------------------|--------------|
Allow    | SISTEMA                        | Controlo Total         | Este objeto  |
Allow    | Administradores corporativos             | Controlo Total         | Este objeto  |
Allow    | Administradores de domínio                 | Controlo Total         | Este objeto  |
Allow    | Administradores                | Controlo Total         | Este objeto  |
Allow    | Controladores de domínio da empresa | Listar conteúdo        | Este objeto  |
Allow    | Controladores de domínio da empresa | Ler todas as propriedades  | Este objeto  |
Allow    | Controladores de domínio da empresa | Permissões de Leitura     | Este objeto  |
Allow    | Utilizadores Autenticados           | Listar conteúdo        | Este objeto  |
Allow    | Utilizadores Autenticados           | Ler todas as propriedades  | Este objeto  |
Allow    | Utilizadores Autenticados           | Permissões de Leitura     | Este objeto  |

Para restringir as configurações para a conta de AD DS, você pode executar [este script do PowerShell](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). O script do PowerShell atribuirá as permissões mencionadas acima à conta de AD DS.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Script do PowerShell para apertar uma conta de serviço pré-existente

Para usar o script do PowerShell, para aplicar essas configurações, a uma conta de AD DS pré-existente (ether fornecida pela sua organização ou criada por uma instalação anterior do Azure AD Connect, baixe o script do link fornecido acima.

##### <a name="usage"></a>Utilização:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Onde 

**$ObjectDN** = a conta de Active Directory cujas permissões precisam ser reforçadas.

**$Credential** = credencial de administrador que tem os privilégios necessários para restringir as permissões na conta de $ObjectDN. Esses privilégios normalmente são mantidos pelo administrador corporativo ou de domínio. Use o nome de domínio totalmente qualificado da conta de administrador para evitar falhas de pesquisa de conta. Exemplo: contoso. com\admin.

>[!NOTE] 
>$credential. O nome de usuário deve estar no formato FQDN\username. Exemplo: contoso. com\admin 

##### <a name="example"></a>Exemplo:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Essa vulnerabilidade foi usada para obter acesso não autorizado?

Para ver se essa vulnerabilidade foi usada para comprometer sua configuração de Azure AD Connect, você deve verificar a data da última redefinição de senha da conta de serviço.  Se o carimbo de data/hora for inesperado, uma investigação adicional, por meio do log de eventos, para esse evento de redefinição de senha, deverá ser executada.

Para obter mais informações, consulte [Microsoft Security Advisory 4056318](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Estado Outubro de 27 2017

>[!NOTE]
>Essa compilação não está disponível para clientes por meio do recurso de atualização automática Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Correção do problema
* Correção de um problema de compatibilidade de versão entre Azure AD Connect e Azure AD Connect Health Agent (para sincronização). Esse problema afeta os clientes que estão executando Azure AD Connect atualização in-loco para a versão 1.1.647.0, mas atualmente tem o agente de integridade versão 3.0.127.0. Após a atualização, o agente de integridade não poderá mais enviar dados de integridade sobre Azure AD Connect serviço de sincronização ao Azure AD Serviço de Integridade. Com essa correção, a versão do agente de integridade 3.0.129.0 é instalada durante Azure AD Connect atualização in-loco. A versão do agente de integridade 3.0.129.0 não tem problema de compatibilidade com Azure AD Connect versão 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Estado Outubro de 19 2017

> [!IMPORTANT]
> Há um problema de compatibilidade conhecido entre Azure AD Connect versão 1.1.647.0 e a versão do Azure AD Connect Health Agent (para sincronização) 3.0.127.0. Esse problema impede que o agente de integridade envie dados de integridade sobre o serviço de sincronização Azure AD Connect (incluindo erros de sincronização de objetos e dados de histórico de execução) para o Azure AD Serviço de Integridade. Antes de atualizar manualmente sua implantação do Azure AD Connect para a versão 1.1.647.0, verifique a versão atual do agente Azure AD Connect Health instalado no servidor Azure AD Connect. Você pode fazer isso acessando o *painel de controle → adicionar remover programas* e procurar o aplicativo *Microsoft Azure ad Connect Health Agent para sincronização*. Se sua versão for 3.0.127.0, é recomendável aguardar até que a próxima versão de Azure AD Connect esteja disponível antes da atualização. Se a versão do agente de integridade não for 3.0.127.0, é bom continuar com a atualização manual in-loco. Observe que esse problema não afeta a atualização do Swing ou os clientes que estão executando uma nova instalação do Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
* Foi corrigido um problema com a tarefa *alterar entrada de usuário* no assistente de Azure ad Connect:

  * O problema ocorre quando você tem uma implantação de Azure AD Connect existente com a sincronização de senha **habilitada**e está tentando definir o método de entrada do usuário como *autenticação de passagem*. Antes que a alteração seja aplicada, o assistente mostra incorretamente o prompt "*desabilitar sincronização de senha*". No entanto, a sincronização de senha permanece habilitada depois que a alteração é aplicada. Com essa correção, o assistente não mostra mais o prompt.

  * Por design, o assistente não desabilita a sincronização de senha quando você atualiza o método de entrada do usuário usando a tarefa *alterar entrada do usuário* . Isso é para evitar a interrupção para os clientes que desejam manter a sincronização de senha, mesmo que estejam habilitando a autenticação de passagem ou a Federação como seu método de entrada do usuário primário.
  
  * Se desejar desabilitar a sincronização de senha após atualizar o método de entrada do usuário, você deverá executar a tarefa *Personalizar configuração de sincronização* no assistente. Ao navegar até a página *recursos opcionais* , desmarque a opção *sincronização de senha* .
  
  * Observe que o mesmo problema também ocorrerá se você tentar habilitar/desabilitar o logon único contínuo. Especificamente, você tem uma implantação de Azure AD Connect existente com a sincronização de senha habilitada e o método de entrada do usuário já está configurado como *autenticação de passagem*. Usando a tarefa *alterar entrada de usuário* , você tenta marcar/desmarcar a opção *habilitar logon único contínuo* enquanto o método de entrada do usuário permanece configurado como "autenticação de passagem". Antes que a alteração seja aplicada, o assistente mostra incorretamente o prompt "*desabilitar sincronização de senha*". No entanto, a sincronização de senha permanece habilitada depois que a alteração é aplicada. Com essa correção, o assistente não mostra mais o prompt.

* Foi corrigido um problema com a tarefa *alterar entrada de usuário* no assistente de Azure ad Connect:

  * O problema ocorre quando você tem uma implantação de Azure AD Connect existente com a sincronização de senha **desabilitada**e está tentando definir o método de entrada do usuário como *autenticação de passagem*. Quando a alteração é aplicada, o assistente habilita a autenticação de passagem e a sincronização de senha. Com essa correção, o assistente não habilita mais a sincronização de senha.

  * Anteriormente, a sincronização de senha era um pré-requisito para habilitar a autenticação de passagem. Quando você define o método de entrada do usuário como *autenticação de passagem*, o assistente habilitará a autenticação de passagem e a sincronização de senha. Recentemente, a sincronização de senha foi removida como um pré-requisito. Como parte do Azure AD Connect versão 1.1.557.0, foi feita uma alteração para Azure AD Connect não habilitar a sincronização de senha quando você define o método de entrada do usuário como *autenticação de passagem*. No entanto, a alteração só foi aplicada à instalação Azure AD Connect. Com essa correção, a mesma alteração também é aplicada à tarefa *alterar entrada do usuário* .
  
  * Observe que o mesmo problema também ocorrerá se você tentar habilitar/desabilitar o logon único contínuo. Especificamente, você tem uma implantação de Azure AD Connect existente com a sincronização de senha desabilitada e o método de entrada do usuário já está configurado como *autenticação de passagem*. Usando a tarefa *alterar entrada de usuário* , você tenta marcar/desmarcar a opção *habilitar logon único contínuo* enquanto o método de entrada do usuário permanece configurado como "autenticação de passagem". Quando a alteração é aplicada, o assistente habilita a sincronização de senha. Com essa correção, o assistente não habilita mais a sincronização de senha. 

* Corrigido um problema que fazia com que Azure AD Connect atualização falhasse com o erro "*não foi possível atualizar o serviço de sincronização*". Além disso, o serviço de sincronização não pode mais iniciar com o erro de evento "*o serviço não pôde ser iniciado porque a versão do banco de dados é mais recente do que a versão dos binários instalados*". O problema ocorre quando o administrador que executa a atualização não tem o privilégio sysadmin para o SQL Server que está sendo usado pelo Azure AD Connect. Com essa correção, Azure AD Connect requer apenas que o administrador tenha o privilégio db_owner para o banco de dados ADSync durante a atualização.

* Corrigido um problema de atualização de Azure AD Connect que afetou os clientes que habilitaram o [logon único contínuo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Depois que Azure AD Connect for atualizado, o logon único contínuo será exibido incorretamente como desabilitado no assistente de Azure AD Connect, mesmo que o recurso permaneça habilitado e totalmente funcional. Com essa correção, o recurso agora aparece corretamente como habilitado no assistente.

* Correção de um problema que fez com que Azure AD Connect Wizard sempre mostrasse o prompt "*Configurar âncora de origem*" na página *pronto para configurar* , mesmo que nenhuma alteração relacionada à âncora de origem fosse feita.

* Ao executar a atualização manual in-loco do Azure AD Connect, o cliente precisa fornecer as credenciais de administrador global do locatário do Azure AD correspondente. Anteriormente, a atualização poderia continuar embora as credenciais do administrador global pertenciam a um locatário do Azure AD diferente. Embora a atualização pareça ser concluída com êxito, determinadas configurações não são mantidas corretamente com a atualização. Com essa alteração, o assistente impedirá que a atualização continue se as credenciais fornecidas não corresponderem ao locatário do Azure AD.

* Foi removida a lógica redundante que reiniciou innecessariamente Azure AD Connect Health serviço no início de uma atualização manual.


#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* Lógica adicionada para simplificar as etapas necessárias para configurar Azure AD Connect com a nuvem do Microsoft Alemanha. Anteriormente, você precisa atualizar as chaves específicas do registro no Azure AD Connect Server para que ele funcione corretamente com a nuvem do Microsoft Alemanha, conforme descrito neste artigo. Agora, Azure AD Connect pode detectar automaticamente se seu locatário está na nuvem do Microsoft Alemanha com base nas credenciais de administrador global fornecidas durante a instalação.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
> [!NOTE]
> Nota: O serviço de sincronização tem uma interface WMI que permite desenvolver seu próprio Agendador personalizado. Esta interface agora foi preterida e será removida das versões futuras do Azure AD Connect enviadas após 30 de junho de 2018. Os clientes que desejam personalizar a agenda de sincronização devem usar o [Agendador interno](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Problemas corrigidos
* Quando Azure AD Connect assistente cria a conta do AD Connector necessária para sincronizar as alterações do Active Directory local, ele não atribui corretamente à conta a permissão necessária para ler objetos PublicFolder. Esse problema afeta a instalação expressa e a instalação personalizada. Essa alteração corrige o problema.

* Correção de um problema que fazia com que a página de solução de problemas do assistente de Azure AD Connect não fosse renderizada corretamente para os administradores que executam o Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* Ao solucionar problemas de sincronização de senha usando a página de solução de problemas do assistente de Azure AD Connect, a página de solução de problemas agora retorna o status específico do domínio.

* Anteriormente, se você tentar habilitar a sincronização de hash de senha, Azure AD Connect não verificará se a conta do AD Connector tem as permissões necessárias para sincronizar os hashes de senha do AD local. Agora, Azure AD Connect assistente irá verificar e avisar se a conta do conector do AD não tiver permissões suficientes.

### <a name="ad-fs-management"></a>Gerenciamento de AD FS
#### <a name="fixed-issue"></a>Correção do problema
* Corrigido um problema relacionado ao uso do [MS-DS-ConsistencyGuid como](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) recurso de âncora de origem. Esse problema afeta os clientes que configuraram a *Federação com AD FS* como o método de entrada do usuário. Quando você executa a tarefa *Configurar âncora de origem* no assistente, Azure ad Connect alterna para o uso de * MS-DS-ConsistencyGuid como atributo de origem para imutávelid. Como parte dessa alteração, Azure AD Connect tenta atualizar as regras de declaração para imutávelid em AD FS. No entanto, essa etapa falhou porque Azure AD Connect não tinha as credenciais de administrador necessárias para configurar AD FS. Com essa correção, Azure AD Connect agora solicita que você insira as credenciais de administrador para AD FS ao executar a tarefa *Configurar âncora de origem* .



## <a name="116140"></a>1.1.614.0
Estado Setembro de 05 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Problemas conhecidos
* Há um problema conhecido que está fazendo com que Azure AD Connect atualização falhe com o erro "*não é possível atualizar o serviço de sincronização*". Além disso, o serviço de sincronização não pode mais iniciar com o erro de evento "*o serviço não pôde ser iniciado porque a versão do banco de dados é mais recente do que a versão dos binários instalados*". O problema ocorre quando o administrador que executa a atualização não tem o privilégio sysadmin para o SQL Server que está sendo usado pelo Azure AD Connect. As permissões de dbo não são suficientes.

* Há um problema conhecido com a atualização Azure AD Connect que está afetando os clientes que habilitaram o [logon único contínuo](how-to-connect-sso.md). Depois que Azure AD Connect for atualizado, o recurso aparecerá como desabilitado no assistente, mesmo que o recurso permaneça habilitado. Uma correção para esse problema será fornecida em uma versão futura. Os clientes que se preocupam com esse problema de exibição podem corrigi-lo manualmente habilitando o logon único contínuo no assistente.

#### <a name="fixed-issues"></a>Problemas corrigidos
* Correção de um problema que impedia Azure AD Connect de atualizar as regras de declarações no AD FS local ao habilitar o recurso [MS-DS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) . O problema ocorrerá se você tentar habilitar o recurso para uma implantação de Azure AD Connect existente que tenha AD FS configurada como o método de entrada. O problema ocorre porque o assistente não solicita credenciais do ADFS antes de tentar atualizar as regras de declarações no AD FS.
* Corrigido um problema que fazia com que Azure AD Connect falhasse na instalação se a floresta do AD local tiver o NTLM desabilitado. O problema é devido ao assistente de Azure AD Connect não fornecer credenciais totalmente qualificadas ao criar os contextos de segurança necessários para a autenticação Kerberos. Isso faz com que a autenticação Kerberos falhe e Azure AD Connect assistente volte a usar o NTLM.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigido um problema em que a nova regra de sincronização não pode ser criada se o atributo de marca não estiver preenchido.
* Correção de um problema que fazia Azure AD Connect se conectar ao AD local para a sincronização de senha usando NTLM, embora o Kerberos esteja disponível. Esse problema ocorre se a topologia do AD local tiver um ou mais controladores de domínio que foram restaurados de um backup.
* Correção de um problema que fazia com que as etapas de sincronização completa ocorressem desnecessariamente após a atualização. Em geral, a execução de etapas de sincronização completa é necessária após a atualização se houver alterações nas regras de sincronização prontas para uso. O problema ocorreu devido a um erro na lógica de detecção de alteração que detectou incorretamente uma alteração ao encontrar a expressão de regra de sincronização com caracteres de nova linha. Os caracteres de nova linha são inseridos na expressão de regra de sincronização para melhorar a legibilidade.
* Correção de um problema que pode fazer com que o servidor de Azure AD Connect não funcione corretamente após a atualização automática. Esse problema afeta os servidores Azure AD Connect com a versão 1.1.443.0 (ou anterior). Para obter detalhes sobre o problema, consulte o artigo [Azure ad Connect não está funcionando corretamente após uma atualização automática](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Corrigido um problema que pode fazer com que a atualização automática seja repetida a cada 5 minutos quando forem encontrados erros. Com a correção, a atualização automática tenta novamente com retirada exponencial quando são encontrados erros.
* Corrigido um problema em que o evento 611 de sincronização de senha é mostrado incorretamente em logs de eventos de aplicativo do Windows como **informativo** , em vez de **erro**. O evento 611 é gerado sempre que a sincronização de senha encontra um problema. 
* Foi corrigido um problema no assistente de Azure AD Connect que permite que o recurso de write-back de grupo seja habilitado sem selecionar uma UO necessária para o Write-back de grupo.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* Adicionada uma tarefa de solução de problemas ao assistente de Azure AD Connect em tarefas adicionais. Os clientes podem aproveitar essa tarefa para solucionar problemas relacionados à sincronização de senha e coletar diagnósticos gerais. No futuro, a tarefa de solução de problemas será estendida para incluir outros problemas relacionados à sincronização de diretórios.
* O Azure AD Connect agora dá suporte a um novo modo de instalação chamado **usar banco de dados existente**. Esse modo de instalação permite que os clientes instalem Azure AD Connect que especifica um banco de dados ADSync existente. Para obter mais informações sobre esse recurso, consulte o artigo [usar um banco de dados existente](how-to-connect-install-existing-database.md).
* Para maior segurança, Azure AD Connect agora usa o TLS 1.2 para se conectar ao Azure AD para sincronização de diretório. Anteriormente, o padrão era o TLS 1.0.
* Quando Azure AD Connect agente de sincronização de senha é iniciado, ele tenta se conectar ao ponto de extremidade bem conhecido do Azure AD para sincronização de senha. Após a conexão bem-sucedida, ela é redirecionada para um ponto de extremidade específico da região. Anteriormente, o agente de sincronização de senha armazena em cache o ponto de extremidade específico da região até que ele seja reiniciado. Agora, o agente limpa o cache e tenta novamente com o ponto de extremidade conhecido se encontra um problema de conexão com o ponto de extremidade específico da região. Essa alteração garante que a sincronização de senha possa fazer failover para um ponto de extremidade específico da região diferente quando o ponto de extremidade específico da região armazenada em cache não estiver mais disponível.
* Para sincronizar alterações de uma floresta do AD local, precisa de uma conta do AD DS. Você pode (i) criar a conta de AD DS você mesmo e fornecer suas credenciais para Azure AD Connect ou (II) fornecer as credenciais de um administrador corporativo e permitir que Azure AD Connect crie a conta de AD DS para você. Anteriormente, (i) é a opção padrão no assistente de Azure AD Connect. Agora, (II) é a opção padrão.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* Adicionado suporte para Microsoft Azure Governamental Cloud e Microsoft Cloud Alemanha.

### <a name="ad-fs-management"></a>Gerenciamento de AD FS
#### <a name="fixed-issues"></a>Problemas corrigidos
* O cmdlet Initialize-ADSyncNGCKeysWriteBack no módulo do PowerShell do AD Prep estava aplicando incorretamente ACLs ao contêiner de registro do dispositivo e, portanto, herdaria apenas as permissões existentes.  Isso foi atualizado para que a conta de serviço de sincronização tenha as permissões corretas.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* A tarefa verificar logon do ADFS do AAD Connect foi atualizada para que ela Verifique os logons no Microsoft Online e não apenas a recuperação de token do ADFS.
* Ao configurar um novo farm do ADFS usando o AAD Connect, a página solicitando credenciais do ADFS foi movida para que agora ocorra antes que o usuário seja solicitado a fornecer servidores ADFS e WAP.  Isso permite que o AAD Connect Verifique se a conta especificada tem as permissões corretas.
* Durante a atualização do AAD Connect, não haverá mais falha em uma atualização se a confiança AAD do ADFS não for atualizada.  Se isso acontecer, o usuário receberá uma mensagem de aviso apropriada e deverá continuar a redefinir a relação de confiança por meio da tarefa adicional do AAD Connect.

### <a name="seamless-single-sign-on"></a>Logon único contínuo
#### <a name="fixed-issues"></a>Problemas corrigidos
* Correção de um problema que fez com que Azure AD Connect assistente retornasse um erro se você tentasse habilitar o [logon único contínuo](how-to-connect-sso.md). A mensagem de erro é *"falha na configuração do agente de autenticação do Microsoft Azure ad Connect".* Esse problema afeta os clientes existentes que atualizaram manualmente a versão prévia dos agentes de autenticação para [autenticação de passagem](how-to-connect-sso.md) com base nas etapas descritas neste [artigo](how-to-connect-pta-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Estado Julho de 23 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Correção do problema

* Corrigido um problema que fazia com que a regra de sincronização integrada "out para AD-User imutável" fosse removida:

  * O problema ocorre quando Azure AD Connect é atualizado ou quando a opção de tarefa *Atualizar configuração de sincronização* no assistente de Azure ad Connect é usada para atualizar Azure ad Connect configuração de sincronização.
  
  * Essa regra de sincronização é aplicável aos clientes que habilitaram o [MS-DS-ConsistencyGuid como recurso de âncora de origem](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Esse recurso foi introduzido na versão 1.1.524.0 e posteriores. Quando a regra de sincronização for removida, Azure AD Connect não poderá mais popular o atributo AD ms-DS-ConsistencyGuid local com o valor do atributo objectGUID. Ele não impede que novos usuários sejam provisionados no Azure AD.
  
  * A correção garante que a regra de sincronização não será mais removida durante a atualização ou durante a alteração da configuração, desde que o recurso esteja habilitado. Para clientes existentes que foram afetados por esse problema, a correção também garante que a regra de sincronização seja adicionada novamente após a atualização para esta versão do Azure AD Connect.

* Correção de um problema que faz com que as regras de sincronização integradas tenham um valor de precedência inferior a 100:

  * Em geral, os valores de precedência 0-99 são reservados para regras de sincronização personalizadas. Durante a atualização, os valores de precedência para regras de sincronização integradas são atualizados para acomodar as alterações da regra de sincronização. Devido a esse problema, as regras de sincronização integradas podem ser atribuídas ao valor de precedência inferior a 100.
  
  * A correção impede que o problema ocorra durante a atualização. No entanto, ele não restaura os valores de precedência para os clientes existentes que foram afetados pelo problema. Uma correção separada será fornecida no futuro para ajudar na restauração.

* Foi corrigido um problema em que a [tela de filtragem de domínio e UO](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente de Azure ad Connect está mostrando a opção *sincronizar todos os domínios e UOs* como selecionada, mesmo que a filtragem baseada em UO esteja habilitada.

*   Corrigido um problema que causou a [tela configurar partições de diretório](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) no Synchronization Service Manager para retornar um erro se o botão *Atualizar* for clicado. A mensagem de erro *é "foi encontrado um erro durante a atualização de domínios: Não é possível converter o objeto do tipo ' System. Collections. ArrayList ' para o tipo ' Microsoft. DirectoryServices. timedirectoryservices. UI. PropertySheetBase. MaPropertyPages. Particionobject. "* O erro ocorre quando um novo domínio do AD é adicionado a uma floresta existente do AD e você está tentando atualizar Azure AD Connect usando o botão atualizar.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* O [recurso de atualização automática](how-to-connect-install-automatic-upgrade.md) foi expandido para dar suporte a clientes com as seguintes configurações:
  * Você habilitou o recurso de write-back de dispositivo.
  * Você habilitou o recurso de write-back de grupo.
  * A instalação não é uma configuração expressa ou uma atualização do DirSync.
  * Você tem mais de 100.000 objetos no metaverso.
  * Você está se conectando a mais de uma floresta. A instalação expressa se conecta somente a uma floresta.
  * A conta do conector do AD não é mais a conta padrão do MSOL_.
  * O servidor está definido como no modo de preparo.
  * Você habilitou o recurso de write-back de usuário.
  
  >[!NOTE]
  >A expansão do escopo do recurso de atualização automática afeta os clientes com Azure AD Connect compilar 1.1.105.0 e posterior. Se você não quiser que seu servidor de Azure AD Connect seja atualizado automaticamente, execute o seguinte cmdlet em seu servidor de Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Para obter mais informações sobre como habilitar/desabilitar a atualização automática, consulte [o artigo Azure ad Connect: Automatic upgrade](how-to-connect-install-automatic-upgrade.md) (Azure AD Connect: Atualização automática).

## <a name="115580"></a>1.1.558.0
Estado Não será liberado. As alterações nesta compilação estão incluídas na versão 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Correção do problema

* Corrigido um problema que fazia com que a regra de sincronização pronta para uso "out para AD-User imutável" fosse removida quando a configuração de filtragem baseada em UO é atualizada. Essa regra de sincronização é necessária para o [recurso ms-DS-ConsistencyGuid como âncora de origem](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Foi corrigido um problema em que a [tela de filtragem de domínio e UO](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente de Azure ad Connect está mostrando a opção *sincronizar todos os domínios e UOs* como selecionada, mesmo que a filtragem baseada em UO esteja habilitada.

*   Corrigido um problema que causou a [tela configurar partições de diretório](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) no Synchronization Service Manager para retornar um erro se o botão *Atualizar* for clicado. A mensagem de erro *é "foi encontrado um erro durante a atualização de domínios: Não é possível converter o objeto do tipo ' System. Collections. ArrayList ' para o tipo ' Microsoft. DirectoryServices. timedirectoryservices. UI. PropertySheetBase. MaPropertyPages. Particionobject. "* O erro ocorre quando um novo domínio do AD é adicionado a uma floresta existente do AD e você está tentando atualizar Azure AD Connect usando o botão atualizar.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* O [recurso de atualização automática](how-to-connect-install-automatic-upgrade.md) foi expandido para dar suporte a clientes com as seguintes configurações:
  * Você habilitou o recurso de write-back de dispositivo.
  * Você habilitou o recurso de write-back de grupo.
  * A instalação não é uma configuração expressa ou uma atualização do DirSync.
  * Você tem mais de 100.000 objetos no metaverso.
  * Você está se conectando a mais de uma floresta. A instalação expressa se conecta somente a uma floresta.
  * A conta do conector do AD não é mais a conta padrão do MSOL_.
  * O servidor está definido como no modo de preparo.
  * Você habilitou o recurso de write-back de usuário.
  
  >[!NOTE]
  >A expansão do escopo do recurso de atualização automática afeta os clientes com Azure AD Connect compilar 1.1.105.0 e posterior. Se você não quiser que seu servidor de Azure AD Connect seja atualizado automaticamente, execute o seguinte cmdlet em seu servidor de Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Para obter mais informações sobre como habilitar/desabilitar a atualização automática, consulte [o artigo Azure ad Connect: Automatic upgrade](how-to-connect-install-automatic-upgrade.md) (Azure AD Connect: Atualização automática).

## <a name="115570"></a>1.1.557.0
Estado Julho de 2017

>[!NOTE]
>Essa compilação não está disponível para clientes por meio do recurso de atualização automática Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Correção do problema
* Foi corrigido um problema com o cmdlet Initialize-ADSyncDomainJoinedComputerSync que fazia com que o domínio verificado configurado no objeto de ponto de conexão de serviço existente fosse alterado, mesmo que ainda seja um domínio válido. Esse problema ocorre quando seu locatário do Azure AD tem mais de um domínio verificado que pode ser usado para configurar o ponto de conexão de serviço.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* O Write-back de senha agora está disponível para visualização com Microsoft Azure Governamental Cloud e Microsoft Cloud Alemanha. Para obter mais informações sobre Azure ad Connect suporte para as diferentes instâncias de serviço, consulte [o artigo Azure ad Connect: Considerações especiais para instâncias](reference-connect-instances.md).

* O cmdlet Initialize-ADSyncDomainJoinedComputerSync agora tem um novo parâmetro opcional chamado AzureADDomain. Esse parâmetro permite especificar qual domínio verificado será usado para configurar o ponto de conexão de serviço.

### <a name="pass-through-authentication"></a>Autenticação pass-through

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* O nome do agente necessário para a autenticação de passagem foi alterado de *Microsoft Azure ad conector de proxy de aplicativo* para *Microsoft Azure ad agente de autenticação de conexão*.

* A habilitação da autenticação de passagem não permite mais a sincronização de hash de senha por padrão.


## <a name="115530"></a>1.1.553.0
Estado Junho de 2017

> [!IMPORTANT]
> Há alterações de esquema e regra de sincronização introduzidas nesta compilação. Azure AD Connect serviço de sincronização irá disparar as etapas de importação completa e sincronização completa após a atualização. Os detalhes das alterações são descritos abaixo. Para adiar temporariamente as etapas de importação completa e de sincronização completa após a atualização, consulte [o artigo como adiar a sincronização completa após a atualização](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Problema conhecido
* Há um problema que afeta os clientes que estão usando a [filtragem baseada em UO](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) com Azure ad Connect sincronização. Quando você navega para a [página filtragem de domínio e UO](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente de Azure ad Connect, o seguinte comportamento é esperado:
  * Se a filtragem baseada em UO estiver habilitada, a opção **sincronizar domínios e UOs selecionados** será selecionada.
  * Caso contrário, a opção **sincronizar todos os domínios e UOs** será selecionada.

O problema que surge é que a **opção sincronizar todos os domínios e UOs** sempre é selecionada quando você executa o assistente.  Isso ocorre mesmo que a filtragem baseada em UO tenha sido configurada anteriormente. Antes de salvar as alterações de configuração do AAD Connect, verifique se a **opção sincronizar domínios e UOs selecionados está selecionada** e confirme se todas as UOs que precisam ser sincronizadas estão habilitadas novamente. Caso contrário, a filtragem baseada em UO será desabilitada.

#### <a name="fixed-issues"></a>Problemas corrigidos

* Corrigido um problema com o Write-back de senha que permite que um administrador do Azure AD redefina a senha de uma conta de usuário com privilégios do AD local. O problema ocorre quando Azure AD Connect recebe a permissão Redefinir senha sobre a conta privilegiada. O problema é abordado nesta versão do Azure AD Connect não permitindo que um administrador do Azure AD redefina a senha de uma conta de usuário privilegiada do AD local arbitrário, a menos que o administrador seja o proprietário dessa conta. Para obter mais informações, consulte [consultoria de segurança 4033453](https://technet.microsoft.com/library/security/4033453).

* Corrigido um problema relacionado ao [MS-DS-ConsistencyGuid como o recurso de âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) em que Azure ad Connect não faz write-back para o atributo ad ms-DS-ConsistencyGuid local. O problema ocorre quando há várias florestas do AD locais adicionadas ao Azure AD Connect e a *opção as identidades de usuário em vários diretórios* está selecionada. Quando essa configuração é usada, as regras de sincronização resultantes não preenchem o atributo sourceAnchorBinary no metaverso. O atributo sourceAnchorBinary é usado como o atributo de origem para o atributo ms-DS-ConsistencyGuid. Como resultado, o Write-back para o atributo ms-DSConsistencyGuid não ocorre. Para corrigir o problema, as seguintes regras de sincronização foram atualizadas para garantir que o atributo sourceAnchorBinary no metaverso seja sempre populado:
  * Entrada do AD-InetOrgPerson AccountEnabled. xml
  * Entrada do AD-InetOrgPerson Common. xml
  * Entrada do AD – usuário AccountEnabled. xml
  * Entrada do AD-User Common. xml
  * Entrada do AD-User Join SOAInAAD. xml

* Anteriormente, mesmo que o recurso [MS-DS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) não esteja habilitado, a regra de sincronização "out to ad – User imutávelid" ainda será adicionada ao Azure ad Connect. O efeito é benigno e não faz com que o Write-back do atributo ms-DS-ConsistencyGuid ocorra. Para evitar confusão, a lógica foi adicionada para garantir que a regra de sincronização só seja adicionada quando o recurso estiver habilitado.

* Corrigido um problema que causou a falha da sincronização de hash de senha com o evento de erro 611. Esse problema ocorre depois que um ou mais controladores de domínio foram removidos do AD local. Ao final de cada ciclo de sincronização de senha, o cookie de sincronização emitido pelo AD local contém IDs de invocação dos controladores de domínio removidos com o valor de USN (número de sequência de atualização) de 0. O Gerenciador de sincronização de senha não pode persistir o cookie de sincronização que contém o valor de USN 0 e falha com o evento de erro 611. Durante o próximo ciclo de sincronização, o Gerenciador de sincronização de senha reutiliza o último cookie de sincronização persistente que não contém o valor de USN 0. Isso faz com que as mesmas alterações de senha sejam ressincronizadas. Com essa correção, o Gerenciador de sincronização de senha persiste corretamente o cookie de sincronização.

* Anteriormente, mesmo que a atualização automática tenha sido desabilitada usando o cmdlet Set-ADSyncAutoUpgrade, o processo de atualização automática continua verificando a atualização periodicamente e se baseia no instalador baixado para honrar a desabilitação. Com essa correção, o processo de atualização automática não verifica mais periodicamente a atualização. A correção é aplicada automaticamente quando o instalador de atualização para este Azure AD Connect versão é executado uma vez.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Anteriormente, o recurso [MS-DS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) estava disponível apenas para novas implantações. Agora, ele está disponível para implantações existentes. Mais especificamente:
  * Para acessar o recurso, inicie o assistente de Azure AD Connect e escolha a opção *Atualizar âncora de origem* .
  * Essa opção só é visível para implantações existentes que estão usando objectGUID como atributo sourceAnchor.
  * Ao configurar a opção, o assistente valida o estado do atributo ms-DS-ConsistencyGuid no local Active Directory. Se o atributo não estiver configurado em nenhum objeto de usuário no diretório, o assistente usará o MS-DS-ConsistencyGuid como o atributo sourceAnchor. Se o atributo estiver configurado em um ou mais objetos de usuário no diretório, o assistente concluirá que o atributo está sendo usado por outros aplicativos e não será adequado como o atributo sourceAnchor e não permitirá que a alteração da âncora de origem continue. Se você tiver certeza de que o atributo não é usado por aplicativos existentes, será necessário entrar em contato com o suporte para obter informações sobre como suprimir o erro.

* Específico ao atributo **userCertificate** em objetos de dispositivo, Azure ad Connect agora procura os valores de certificados necessários para [conectar os dispositivos ingressados no domínio ao Azure ad para a experiência do Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) e filtra o restante antes de sincronizar para o Azure AD. Para habilitar esse comportamento, a regra de sincronização pronta para uso "saída para o AAD-Device Join SOAInAD" foi atualizada.

* O Azure AD Connect agora dá suporte a Write-back do atributo **cloudPublicDelegates** do Exchange Online para o atributo **publicDelegates** do AD local. Isso habilita o cenário em que uma caixa de correio do Exchange Online pode receber direitos SendOnBehalfTo para usuários com caixa de correio do Exchange local. Para dar suporte a esse recurso, uma nova regra de sincronização pronta para uso "out to AD – User Exchange Hybrid PublicDelegates write-back" foi adicionada. Essa regra de sincronização só é adicionada ao Azure AD Connect quando o recurso híbrido do Exchange está habilitado.

* Azure AD Connect agora dá suporte à sincronização do atributo **altRecipient** do Azure AD. Para dar suporte a essa alteração, as seguintes regras de sincronização integradas foram atualizadas para incluir o fluxo de atributos necessário:
  * Entrada do AD – usuário Exchange
  * Saída para o AAD – ExchangeOnline do usuário
  
* O atributo **cloudSOAExchMailbox** no metaverso indica se um determinado usuário tem a caixa de correio do Exchange Online ou não. Sua definição foi atualizada para incluir RecipientDisplayTypes adicionais do Exchange Online como essas caixas de correio de equipamentos e salas de conferência. Para habilitar essa alteração, a definição do atributo cloudSOAExchMailbox, que é encontrada na regra de sincronização pronta para uso "in from AAD – usuário Exchange híbrido", foi atualizada de:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... para o seguinte:

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

* Adicionado o seguinte conjunto de funções compatíveis com X509Certificate2 para criar expressões de regra de sincronização para manipular valores de certificado no atributo userCertificate:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Seleccionar|
    |CertKeyAlgorithmParams|CertHashString|Onde|
    |||Com|

* As seguintes alterações de esquema foram introduzidas para permitir que os clientes criem regras de sincronização personalizadas para o Flow sAMAccountName, domainNetBios e domainFQDN para objetos de grupo, bem como distinguishedName para objetos de usuário:

  * Os seguintes atributos foram adicionados ao esquema MV:
    * Group Nome da Conta
    * Group: domainNetBios
    * Group: domainFQDN
    * Pessoa: distinguishedName

  * Os seguintes atributos foram adicionados ao esquema do Azure AD Connector:
    * Group OnPremisesSamAccountName
    * Group NetBiosName
    * Group DnsDomainName
    * Usuário OnPremisesDistinguishedName

* O script de cmdlet ADSyncDomainJoinedComputerSync agora tem um novo parâmetro opcional chamado AzureEnvironment. O parâmetro é usado para especificar em qual região o locatário de Azure Active Directory correspondente está hospedado. Valores válidos incluem:
  * AzureCloud (padrão)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* Atualizado o editor de regra de sincronização para usar Join (em vez de provisionar) como o valor padrão do tipo de link durante a criação da regra de sincronização.

### <a name="ad-fs-management"></a>Gerenciamento de AD FS

#### <a name="issues-fixed"></a>Problemas corrigidos

* As URLs a seguir são os novos pontos de extremidade do WS-Federation introduzidos pelo Azure AD para melhorar a resiliência contra a interrupção da autenticação e serão adicionados ao local AD FS configuração de confiança da parte de resposta:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Corrigido um problema que fazia AD FS gerar um valor de declaração incorreto para IssuerID. O problema ocorre se houver vários domínios verificados no locatário do Azure AD e o sufixo de domínio do atributo userPrincipalName usado para gerar a declaração IssuerID for de pelo menos 3 níveis de profundidade (por exemplo johndoe@us.contoso.com,). O problema é resolvido atualizando o Regex usado pelas regras de declaração.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* Anteriormente, o recurso de gerenciamento de certificados do ADFS fornecido pelo Azure AD Connect só pode ser usado com farms do ADFS gerenciados por meio de Azure AD Connect. Agora, você pode usar o recurso com farms do ADFS que não são gerenciados usando Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Liberado Maio de 2017

> [!IMPORTANT]
> Há alterações de esquema e regra de sincronização introduzidas nesta compilação. Azure AD Connect serviço de sincronização irá disparar as etapas de importação completa e sincronização completa após a atualização. Os detalhes das alterações são descritos abaixo.
>
>

**Problemas corrigidos:**

Sincronização Azure AD Connect

* Corrigido um problema que faz com que a atualização automática ocorra no servidor de Azure AD Connect, mesmo que o cliente tenha desabilitado o recurso usando o cmdlet Set-ADSyncAutoUpgrade. Com essa correção, o processo de atualização automática no servidor ainda verifica a atualização periodicamente, mas o instalador baixado honra a configuração de atualização automática.
* Durante a atualização in-loco do DirSync, Azure AD Connect cria uma conta de serviço do Azure AD a ser usada pelo Azure AD Connector para sincronizar com o Azure AD. Depois que a conta é criada, o Azure AD Connect é autenticado com o Azure AD usando a conta. Às vezes, a autenticação falha devido a problemas transitórios, o que, por sua vez, faz com que a *atualização in-loco do DirSync falhe com o erro "ocorreu um erro ao executar a tarefa configurar AAD Sync: AADSTS50034: Para entrar neste aplicativo, a conta deve ser adicionada ao diretório xxx.onmicrosoft.com ".* Para melhorar a resiliência da atualização do DirSync, Azure AD Connect agora repete a etapa de autenticação.
* Ocorreu um problema com a compilação 443 que faz com que a atualização in-loco do DirSync tenha êxito, mas os perfis de execução necessários para a sincronização de diretório não são criados. A lógica de reparo está incluída neste Build de Azure AD Connect. Quando o cliente atualiza para essa compilação, Azure AD Connect detecta perfis de execução ausentes e os cria.
* Corrigido um problema que faz com que o processo de sincronização de senha falhe ao iniciar com a ID de evento 6900 e *o erro "um item com a mesma chave já foi adicionado"* . Esse problema ocorre se você atualizar a configuração de filtragem de UO para incluir a partição de configuração do AD. Para corrigir esse problema, o processo de sincronização de senha agora sincroniza somente as alterações de senha de partições de domínio do AD. As partições que não são de domínio, como a partição de configuração, são ignoradas.
* Durante a instalação expressa, o Azure AD Connect cria uma conta de AD DS local a ser usada pelo AD Connector para se comunicar com o AD local. Anteriormente, a conta é criada com o sinalizador PASSWD_NOTREQD definido no atributo de controle de conta de usuário e uma senha aleatória é definida na conta. Agora, Azure AD Connect remove explicitamente o sinalizador PASSWD_NOTREQD depois que a senha é definida na conta.
* Corrigido um problema que faz com que a atualização do DirSync falhe com *o erro "ocorreu um deadlock no SQL Server que está tentando adquirir um bloqueio de aplicativo"* quando o atributo mailNickname é encontrado no esquema do AD local, mas não está associado à classe de objeto de usuário do AD.
* Correção de um problema que faz com que o recurso de write-back de dispositivo seja desabilitado automaticamente quando um administrador está atualizando Azure AD Connect configuração de sincronização usando Azure AD Connect assistente. Esse problema é causado pelo assistente que executa uma verificação de pré-requisito para a configuração de write-back do dispositivo existente no AD local e a verificação falha. A correção é ignorar a verificação se o Write-back do dispositivo já estiver habilitado anteriormente.
* Para configurar a filtragem de UO, você pode usar o assistente de Azure AD Connect ou o Synchronization Service Manager. Anteriormente, se você usar o assistente de Azure AD Connect para configurar a filtragem de UO, novas UOs criadas posteriormente serão incluídas para a sincronização de diretórios. Se não quiser que novas UOs sejam incluídas, você deverá configurar a filtragem de UO usando o Synchronization Service Manager. Agora, você pode obter o mesmo comportamento usando Azure AD Connect assistente.
* Corrigido um problema que faz com que os procedimentos armazenados exigidos pelo Azure AD Connect sejam criados no esquema da instalação do administrador, em vez de no esquema dbo.
* Corrigido um problema que faz com que o atributo TrackingID retornado pelo Azure AD fosse omitido nos logs de eventos do servidor do AAD Connect. O problema ocorre se Azure AD Connect receber uma mensagem de redirecionamento do Azure AD e Azure AD Connect não puder se conectar ao ponto de extremidade fornecido. O TrackingID é usado por engenheiros de suporte para correlacionar com os logs do lado do serviço durante a solução de problemas.
* Quando Azure ad Connect recebe o erro largeobject do Azure AD, Azure ad Connect gera um evento com EventID 6941 e *Message "o objeto provisionado é muito grande. Corte o número de valores de atributo neste objeto. "* Ao mesmo tempo, Azure ad Connect também gera um evento enganoso com EventID 6900 e a mensagem *"Microsoft. online. coexistência. ProvisionRetryException: Não é possível se comunicar com o serviço de Azure Active Directory do Windows. "* Para minimizar a confusão, Azure AD Connect não gera mais o último evento quando o erro Largeobject é recebido.
* Corrigido um problema que faz com que o Synchronization Service Manager não responda ao tentar atualizar a configuração para o conector LDAP genérico.

**Novos recursos/aprimoramentos:**

Sincronização Azure AD Connect
* Alterações de regra de sincronização – as seguintes alterações de regra de sincronização foram implementadas:
  * Atualizado o conjunto de regras de sincronização padrão para não exportar atributos **userCertificate** e **userSMIMECertificate** se os atributos tiverem mais de 15 valores.
  * Os atributos do AD **EmployeeID** e **msExchBypassModerationLink** agora estão incluídos no conjunto de regras de sincronização padrão.
  * A **foto** de atributo do AD foi removida do conjunto de regras de sincronização padrão.
  * Adicionado **preferredDataLocation** ao esquema do metaverso e ao esquema do conector do AAD. Os clientes que desejam atualizar os atributos no Azure AD podem implementar regras de sincronização personalizadas para fazer isso. 
  * Adicionado **UserType** ao esquema de metaverso e ao esquema do conector do AAD. Os clientes que desejam atualizar os atributos no Azure AD podem implementar regras de sincronização personalizadas para fazer isso.

* Azure AD Connect agora habilita automaticamente o uso do atributo ConsistencyGuid como o atributo de âncora de origem para objetos do AD local. Além disso, Azure AD Connect popula o atributo ConsistencyGuid com o valor do atributo objectGUID se ele estiver vazio. Esse recurso é aplicável somente a novas implantações. Para saber mais sobre esse recurso, consulte a seção [do artigo Azure ad Connect: Conceitos de design-usando MS-DS-ConsistencyGuid como](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)sourceAnchor.
* O novo cmdlet de solução de problemas Invoke-ADSyncDiagnostics foi adicionado para ajudar a diagnosticar problemas relacionados à sincronização de hash de senha. Para obter informações sobre como usar o cmdlet, consulte o artigo [solucionar problemas de sincronização de hash de senha com Azure ad Connect sincronização](tshoot-connect-password-hash-synchronization.md).
* Azure AD Connect agora dá suporte à sincronização de objetos de pasta pública habilitados para email do AD local para o Azure AD. Você pode habilitar o recurso usando Azure AD Connect assistente em recursos opcionais. Para saber mais sobre esse recurso, consulte o artigo [suporte ao bloqueio de borda baseado em diretório do Office 365 para pastas públicas habilitadas para email local](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Azure AD Connect requer uma conta de AD DS para sincronizar do AD local. Anteriormente, se você instalou Azure AD Connect usando o modo expresso, poderá fornecer as credenciais de uma conta de administrador corporativo e Azure AD Connect criaria a conta de AD DS necessária. No entanto, para uma instalação personalizada e a adição de florestas a uma implantação existente, era necessário fornecer a conta de AD DS em vez disso. Agora, você também tem a opção de fornecer as credenciais de uma conta de administrador corporativo durante uma instalação personalizada e permitir que Azure AD Connect crie a conta de AD DS necessária.
* Azure AD Connect agora dá suporte ao SQL AOA. Você deve habilitar o SQL AOA antes de instalar Azure AD Connect. Durante a instalação, Azure AD Connect detecta se a instância do SQL fornecida está habilitada para SQL AOA ou não. Se o SQL AOA estiver habilitado, Azure AD Connect outras figuras se o SQL AOA estiver configurado para usar replicação síncrona ou replicação assíncrona. Ao configurar o ouvinte do grupo de disponibilidade, é recomendável que você defina a propriedade RegisterAllProvidersIP como 0. Essa recomendação é porque Azure AD Connect atualmente usa SQL Native Client para se conectar ao SQL e o SQL Native Client não oferece suporte ao uso da propriedade MultiSubNetFailover.
* Se você estiver usando o LocalDB como o banco de dados para seu servidor de Azure AD Connect e tiver atingido seu limite de tamanho de 10 GB, o serviço de sincronização não será mais iniciado. Anteriormente, você precisa executar a operação ShrinkDatabase no LocalDB para recuperar espaço de BD suficiente para que o serviço de sincronização seja iniciado. Depois de, você pode usar o Synchronization Service Manager para excluir o histórico de execução para recuperar mais espaço do banco de informações. Agora, você pode usar o cmdlet Start-ADSyncPurgeRunHistory para limpar os dados do histórico de execução do LocalDB para recuperar o espaço do banco de dados. Além disso, esse cmdlet dá suporte a um modo offline (especificando o parâmetro-offline) que pode ser usado quando o serviço de sincronização não está em execução. Nota: O modo offline só poderá ser usado se o serviço de sincronização não estiver em execução e o banco de dados usado for LocalDB.
* Para reduzir a quantidade de espaço de armazenamento necessária, Azure AD Connect agora compacta os detalhes do erro de sincronização antes de armazená-los em bancos de dados LocalDB/SQL. Ao atualizar de uma versão mais antiga do Azure AD Connect para essa versão, Azure AD Connect executa uma compactação única nos detalhes do erro de sincronização existente.
* Anteriormente, depois de atualizar a configuração de filtragem de UO, você deve executar manualmente a importação completa para garantir que os objetos existentes sejam incluídos/excluídos corretamente da sincronização de diretório. Agora, Azure AD Connect automaticamente dispara a importação completa durante o próximo ciclo de sincronização. Além disso, a importação completa só será aplicada aos conectores do AD afetados pela atualização. Observação: essa melhoria é aplicável a atualizações de filtragem de UO feitas usando o assistente de Azure AD Connect apenas. Ele não é aplicável à atualização de filtragem de UO feita usando o Synchronization Service Manager.
* Anteriormente, a filtragem baseada em grupo dá suporte apenas a usuários, grupos e objetos de contato. Agora, a filtragem baseada em grupo também dá suporte a objetos de computador.
* Anteriormente, você pode excluir os dados de espaço do conector sem desabilitar Azure AD Connect o Agendador de sincronização. Agora, o Synchronization Service Manager bloqueará a exclusão de dados de espaço do conector se detectar que o Agendador está habilitado. Além disso, um aviso é retornado para informar os clientes sobre potencial perda de dados se os dados do espaço do conector forem excluídos.
* Anteriormente, você deve desabilitar a transcrição do PowerShell para que o assistente de Azure AD Connect seja executado corretamente. Esse problema foi parcialmente resolvido. Você pode habilitar a transcrição do PowerShell se estiver usando Azure AD Connect assistente para gerenciar a configuração de sincronização. Você deve desabilitar a transcrição do PowerShell se estiver usando Azure AD Connect assistente para gerenciar a configuração do ADFS.



## <a name="114860"></a>1.1.486.0
Liberado Abril de 2017

**Problemas corrigidos:**
* Correção do problema em que o Azure AD Connect não será instalado com êxito na versão localizada do Windows Server.

## <a name="114840"></a>1.1.484.0
Liberado Abril de 2017

**Problemas conhecidos:**

* Esta versão do Azure AD Connect não será instalada com êxito se as seguintes condições forem todas verdadeiras:
   1. Você está executando a atualização in-loco do DirSync ou a nova instalação do Azure AD Connect.
   2. Você está usando uma versão localizada do Windows Server em que o nome do grupo de administradores interno no servidor não é "administradores".
   3. Você está usando o padrão SQL Server 2012 Express LocalDB instalado com Azure AD Connect em vez de fornecer seu próprio SQL completo.

**Problemas corrigidos:**

Sincronização Azure AD Connect
* Corrigido um problema em que o Agendador de sincronização ignora a etapa de sincronização inteira se um ou mais conectores não tiverem um perfil de execução para essa etapa de sincronização. Por exemplo, você adicionou manualmente um conector usando o Synchronization Service Manager sem criar um perfil de execução de importação Delta para ele. Essa correção garante que o Agendador de sincronização continue a executar a importação Delta para outros conectores.
* Corrigido um problema em que o serviço de sincronização interrompe imediatamente o processamento de um perfil de execução quando encontra um problema com uma das etapas de execução. Essa correção garante que o serviço de sincronização ignore essa etapa de execução e continue a processar o restante. Por exemplo, você tem um perfil de execução de importação Delta para seu conector do AD com várias etapas de execução (uma para cada domínio do AD local). O serviço de sincronização executará a importação Delta com os outros domínios do AD, mesmo se um deles tiver problemas de conectividade de rede.
* Corrigido um problema que faz com que a atualização do conector do Azure AD seja ignorada durante a atualização automática.
* Corrigido um problema que faz com que o Azure AD Connect determine incorretamente se o servidor é um controlador de domínio durante a instalação, o que, por sua vez, faz com que a atualização do DirSync falhe.
* Correção de um problema que faz com que a atualização in-loco do DirSync não crie nenhum perfil de execução para o conector do AD do Azure.
* Corrigido um problema em que a interface do usuário Synchronization Service Manager não responde ao tentar configurar o conector LDAP genérico.

Gerenciamento de AD FS
* Corrigido um problema em que o assistente de Azure AD Connect falhará se o nó primário AD FS tiver sido movido para outro servidor.

SSO de desktop
* Correção de um problema no assistente de Azure AD Connect em que a tela de entrada não permite habilitar o recurso de SSO de área de trabalho se você escolher a sincronização de senha como sua opção de entrada durante a nova instalação.

**Novos recursos/aprimoramentos:**

Sincronização Azure AD Connect
* Azure AD Connect sincronização agora dá suporte ao uso de conta de serviço virtual, conta de serviço gerenciado e conta de serviço gerenciado de grupo como sua conta de serviço. Isso se aplica somente à nova instalação do Azure AD Connect. Ao instalar o Azure AD Connect:
    * Por padrão, Azure AD Connect assistente criará uma conta de serviço virtual e a usará como sua conta de serviço.
    * Se você estiver instalando o em um controlador de domínio, Azure AD Connect retornará ao comportamento anterior, em que ele criará uma conta de usuário de domínio e a usará como sua conta de serviço.
    * Você pode substituir o comportamento padrão fornecendo um dos seguintes:
      * Uma conta de serviço gerenciado de grupo
      * Uma conta de serviço gerenciado
      * Uma conta de usuário de domínio
      * Uma conta de usuário local
* Anteriormente, se você atualizar para uma nova compilação de Azure AD Connect que contenham os conectores atualização ou alterações de regra de sincronização, Azure AD Connect disparará um ciclo de sincronização completo. Agora, Azure AD Connect aciona seletivamente a etapa de importação completa somente para conectores com atualização e a etapa de sincronização completa somente para conectores com alterações de regra de sincronização.
* Anteriormente, o limite de exclusão de exportação só se aplica a exportações disparadas por meio do Agendador de sincronização. Agora, o recurso é estendido para incluir exportações disparadas manualmente pelo cliente usando o Synchronization Service Manager.
* Em seu locatário do Azure AD, há uma configuração de serviço que indica se o recurso de sincronização de senha está habilitado para seu locatário ou não. Anteriormente, é fácil para a configuração de serviço ser configurada incorretamente por Azure AD Connect quando você tem um servidor ativo e de preparo. Agora, Azure AD Connect tentará manter a configuração de serviço consistente com o Active Azure AD Connect Server somente.
* Azure AD Connect assistente agora detecta e retorna um aviso se o AD local não tiver a lixeira do AD habilitada.
* Anteriormente, a exportação para o Azure AD atinge o tempo limite e falha se o tamanho combinado dos objetos no lote exceder determinado limite. Agora, o serviço de sincronização tentará reenviar os objetos em lotes menores e separados se o problema for encontrado.
* O aplicativo de gerenciamento de chaves do serviço de sincronização foi removido do menu Iniciar do Windows. O gerenciamento da chave de criptografia continuará a ter suporte por meio da interface de linha de comando usando miiskmu. exe. Para obter informações sobre como gerenciar a chave de criptografia, consulte [o artigo abandonar a chave de criptografia de sincronização Azure ad Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-adsync-service-account-encryption-key).
* Anteriormente, se você alterar a senha da conta do serviço de sincronização Azure AD Connect, o serviço de sincronização não será capaz de iniciar corretamente até que você tenha abandonado a chave de criptografia e reinicializado a senha da conta do serviço de sincronização de Azure AD Connect. Agora, esse processo não é mais necessário.

SSO de desktop

* O assistente de Azure AD Connect não exige mais que a porta 9090 seja aberta na rede ao configurar a autenticação de passagem e o SSO de área de trabalho. Somente a porta 443 é necessária. 

## <a name="114430"></a>1.1.443.0
Liberado Março de 2017

**Problemas corrigidos:**

Sincronização Azure AD Connect
* Correção de um problema que faz com que Azure AD Connect assistente falhe se o nome de exibição do conector do AD do Azure não contiver o domínio onmicrosoft.com inicial atribuído ao locatário do Azure AD.
* Correção de um problema que faz com que Azure AD Connect assistente falhe ao estabelecer a conexão com o banco de dados SQL quando a senha da conta do serviço de sincronização contiver caracteres especiais, como apóstrofo, dois-pontos e espaço.
* Corrigido um problema que causa o erro "a imagem tem uma âncora diferente da imagem" para ocorrer em um servidor de Azure AD Connect no modo de preparo, depois que você excluiu temporariamente um objeto do AD local da sincronização e o incluiu novamente para sincronização.
* Corrigido um problema que causa o erro "o objeto localizado por DN é um fantasma" para ocorrer em um servidor de Azure AD Connect no modo de preparo, depois que você excluiu temporariamente um objeto do AD local da sincronização e o incluiu novamente para sincronização.

Gerenciamento de AD FS
* Corrigido um problema em que Azure AD Connect assistente não atualiza AD FS configuração e definiu as declarações certas no confiança da terceira parte confiável após a configuração da ID de logon alternativa.
* Corrigido um problema em que Azure AD Connect assistente não consegue lidar corretamente AD FS servidores cujas contas de serviço são configuradas usando o formato userPrincipalName em vez do formato sAMAccountName.

Autenticação pass-through
* Corrigido um problema que faz com que Azure AD Connect assistente falhe se a autenticação de passagem estiver selecionada, mas o registro de seu conector falhar.
* Correção de um problema que faz com que Azure AD Connect assistente ignore verificações de validação no método de entrada selecionado quando o recurso de SSO da área de trabalho está habilitado.

Reposição de Palavra-passe
* Correção de um problema que pode fazer com que o servidor do Azure AAD Connect não tente se conectar novamente se a conexão foi eliminada por um firewall ou proxy.

**Novos recursos/aprimoramentos:**

Sincronização Azure AD Connect
* O cmdlet Get-ADSyncScheduler agora retorna uma nova propriedade booliana chamada SyncCycleInProgress. Se o valor retornado for true, significa que há um ciclo de sincronização agendado em andamento.
* A pasta de destino para armazenar Azure AD Connect instalação e logs de instalação foi movida de%localappdata%\AADConnect para%programdata%\AADConnect para melhorar a acessibilidade para os arquivos de log.

Gerenciamento de AD FS
* Adicionado suporte para atualizar o certificado SSL do farm de AD FS.
* Adicionado suporte para gerenciar o AD FS 2016.
* Agora você pode especificar gMSA existentes (conta de serviço gerenciado de grupo) durante a instalação AD FS.
* Agora você pode configurar o SHA-256 como o algoritmo de hash de assinatura para a relação de confiança de terceira parte confiável do Azure AD.

Reposição de Palavra-passe
* Introduziu melhorias para permitir que o produto funcione em ambientes com regras de firewall mais rígidas.
* Confiabilidade de conexão aprimorada para o barramento de serviço do Azure.

## <a name="113800"></a>1.1.380.0
Liberado Dezembro de 2016

**Problema corrigido:**

* Corrigido o problema em que a regra de declaração issuerid para Serviços de Federação do Active Directory (AD FS) (AD FS) está faltando nesta compilação.

>[!NOTE]
>Essa compilação não está disponível para clientes por meio do recurso de atualização automática Azure AD Connect.

## <a name="113710"></a>1.1.371.0
Liberado Dezembro de 2016

**Problema conhecido:**

* A regra de declaração issuerid para AD FS está ausente nesta compilação. A regra de declaração issuerid será necessária se você estiver Federando vários domínios com Azure Active Directory (Azure AD). Se você estiver usando Azure AD Connect para gerenciar sua implantação de AD FS local, a atualização para essa compilação removerá a regra de declaração issuerid existente da sua configuração de AD FS. Você pode contornar o problema adicionando a regra de declaração issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar a regra de declaração issuerid, consulte este artigo sobre [suporte a vários domínios para federação com o Azure ad](how-to-connect-install-multiple-domains.md).

**Problema corrigido:**

* Se a porta 9090 não estiver aberta para a conexão de saída, a instalação do Azure AD Connect ou a atualização falhará.

>[!NOTE]
>Essa compilação não está disponível para clientes por meio do recurso de atualização automática Azure AD Connect.

## <a name="113700"></a>1.1.370.0
Liberado Dezembro de 2016

**Problemas conhecidos:**

* A regra de declaração issuerid para AD FS está ausente nesta compilação. A regra de declaração issuerid será necessária se você estiver Federando vários domínios com o Azure AD. Se você estiver usando Azure AD Connect para gerenciar sua implantação de AD FS local, a atualização para essa compilação removerá a regra de declaração issuerid existente da sua configuração de AD FS. Você pode contornar o problema adicionando a regra de declaração issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar a regra de declaração issuerid, consulte este artigo sobre [suporte a vários domínios para federação com o Azure ad](how-to-connect-install-multiple-domains.md).
* A porta 9090 deve estar aberta de saída para concluir a instalação.

**Novas funcionalidades:**

* Autenticação de passagem (versão prévia).

>[!NOTE]
>Essa compilação não está disponível para clientes por meio do recurso de atualização automática Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Liberado Novembro de 2016

**Problema conhecido:**

* A regra de declaração issuerid para AD FS está ausente nesta compilação. A regra de declaração issuerid será necessária se você estiver Federando vários domínios com o Azure AD. Se você estiver usando Azure AD Connect para gerenciar sua implantação de AD FS local, a atualização para essa compilação removerá a regra de declaração issuerid existente da sua configuração de AD FS. Você pode contornar o problema adicionando a regra de declaração issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar a regra de declaração issuerid, consulte este artigo sobre [suporte a vários domínios para federação com o Azure ad](how-to-connect-install-multiple-domains.md).

**Problemas corrigidos:**

* Às vezes, a instalação do Azure AD Connect falha porque não é possível criar uma conta de serviço local cuja senha atenda ao nível de complexidade especificado pela política de senha da organização.
* Corrigido um problema em que as regras de junção não são reavaliadas quando um objeto no espaço do conector simultaneamente fica fora do escopo de uma regra de junção e se torna dentro do escopo para outro. Isso pode acontecer se você tiver duas ou mais regras de junção cujas condições de junção são mutuamente exclusivas.
* Corrigido um problema em que as regras de sincronização de entrada (do Azure AD), que não contêm regras de junção, não serão processadas se tiverem valores de precedência mais baixos do que aquelas que contêm regras de junção.

**Melhorias:**

* Suporte adicionado para instalar o Azure AD Connect no Windows Server 2016 Standard ou superior.
* Adicionado suporte para usar o SQL Server 2016 como o banco de dados remoto para Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Liberado Agosto de 2016

**Problemas corrigidos:**

* As alterações no intervalo de sincronização não ocorrem até que o próximo ciclo de sincronização seja concluído.
* Azure AD Connect assistente não aceita uma conta do Azure AD cujo nome de usuário começa com um sublinhado (\_).
* Azure AD Connect assistente não autenticará a conta do Azure AD se a senha da conta contiver muitos caracteres especiais. Mensagem de erro "não é possível validar as credenciais. Ocorreu um erro inesperado. " é retornado.
* A desinstalação do servidor de preparo desabilita a sincronização de senha no locatário do Azure AD e faz com que a sincronização de senha falhe com o servidor ativo.
* A sincronização de senha falha em casos incomuns quando não há hash de senha armazenado no usuário.
* Quando Azure AD Connect Server está habilitado para o modo de preparo, o Write-back de senha não é desabilitado temporariamente.
* Azure AD Connect assistente não mostra a sincronização de senha real e a configuração de write-back de senha quando o servidor está no modo de preparo. Ele sempre os mostra como desabilitado.
* As alterações de configuração na sincronização de senha e no write-back de senha não são persistidas pelo assistente de Azure AD Connect quando o servidor está no modo de preparo.

**Melhorias:**

* Atualizado o cmdlet Start-Adsyncsynccycle adicionado para indicar se ele consegue iniciar um novo ciclo de sincronização ou não com êxito.
* Adicionado o cmdlet Stop-Adsyncsynccycle adicionado para encerrar o ciclo de sincronização e a operação, que estão em andamento no momento.
* O cmdlet Stop-ADSyncScheduler foi atualizado para terminar o ciclo de sincronização e a operação, que estão em andamento no momento.
* Ao configurar [extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) no assistente de Azure ad Connect, o atributo do Azure AD do tipo "cadeia de caracteres Teletex" agora pode ser selecionado.

## <a name="111890"></a>1.1.189.0
Liberado Junho de 2016

**Problemas corrigidos e aprimoramentos:**

* Agora, o Azure AD Connect pode ser instalado em um servidor compatível com FIPS.
  * Para sincronização de senha, consulte [sincronização de hash de senha e FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Corrigido um problema em que um nome NetBIOS não pôde ser resolvido para o FQDN no conector de Active Directory.

## <a name="111800"></a>1.1.180.0
Liberado Maio de 2016

**Novas funcionalidades:**

* Avisa e ajuda a verificar domínios se você não o fez antes de executar o Azure AD Connect.
* Adicionado suporte para [Microsoft Cloud Alemanha](reference-connect-instances.md#microsoft-cloud-germany).
* Suporte adicionado para a mais recente infraestrutura de [nuvem Microsoft Azure governamental](reference-connect-instances.md#microsoft-azure-government) com novos requisitos de URL.

**Problemas corrigidos e aprimoramentos:**

* Adição de filtragem ao editor de regras de sincronização para facilitar a localização de regras de sincronização.
* Melhor desempenho ao excluir um espaço do conector.
* Corrigido um problema quando o mesmo objeto foi excluído e adicionado na mesma execução (chamado excluir/adicionar).
* Uma regra de sincronização desabilitada não reabilita mais os objetos e atributos incluídos na atualização ou atualização do esquema de diretório.

## <a name="111300"></a>1.1.130.0
Liberado Abril de 2016

**Novas funcionalidades:**

* Adicionado suporte para atributos de valores múltiplos para [extensões de diretório](how-to-connect-sync-feature-directory-extensions.md).
* Suporte adicionado para mais variações de configuração para que a [atualização automática](how-to-connect-install-automatic-upgrade.md) seja considerada qualificada para atualização.
* Adicionados alguns cmdlets para o [Agendador personalizado](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Liberado Março de 2016

**Problemas corrigidos:**

* Certifique-se de que a instalação expressa não possa ser usada no Windows Server 2008 (pré-R2) porque não há suporte para a sincronização de senha neste sistema operacional.
* A atualização do DirSync com uma configuração de filtro personalizada não funcionou conforme o esperado.
* Ao atualizar para uma versão mais recente e não há nenhuma alteração na configuração, uma importação/sincronização completa não deve ser agendada.

## <a name="111100"></a>1.1.110.0
Liberado Fevereiro de 2016

**Problemas corrigidos:**

* A atualização de versões anteriores não funcionará se a instalação não estiver na pasta C:\Program Files padrão.
* Se você instalar e desmarcar **iniciar o processo de sincronização** no final do assistente de instalação, a execução do assistente de instalação uma segunda vez não habilitará o Agendador.
* O Agendador não funciona como esperado em servidores nos quais o formato de data/hora de US-EN não é usado. Ele também será bloqueado `Get-ADSyncScheduler` para retornar horários corretos.
* Se você instalou uma versão anterior do Azure AD Connect com AD FS como a opção de entrada e a atualização, não será possível executar o assistente de instalação novamente.

## <a name="111050"></a>1.1.105.0
Liberado Fevereiro de 2016

**Novas funcionalidades:**

* Recurso de [atualização automática](how-to-connect-install-automatic-upgrade.md) para clientes de configurações expressas.
* Suporte para o administrador global usando a autenticação multifator do Azure e Privileged Identity Management no assistente de instalação.
  * Você precisa permitir que o proxy também permita o tráfego se https://secure.aadcdn.microsoftonline-p.com usar a autenticação multifator.
  * Você precisa adicionar https://secure.aadcdn.microsoftonline-p.com à sua lista de sites confiáveis para que a autenticação multifator funcione corretamente.
* Permitir a alteração do método de entrada do usuário após a instalação inicial.
* Permitir [filtragem de domínio e UO](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente de instalação. Isso também permite a conexão a florestas em que nem todos os domínios estão disponíveis.
* O [Agendador](how-to-connect-sync-feature-scheduler.md) é integrado ao mecanismo de sincronização.

**Recursos promovidos da visualização para GA:**

* [Write-back do dispositivo](how-to-connect-device-writeback.md).
* [Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md).

**Novos recursos de visualização:**

* O novo intervalo de ciclo de sincronização padrão é de 30 minutos. Costumava ser três horas para todas as versões anteriores. Adiciona suporte para alterar o comportamento do [Agendador](how-to-connect-sync-feature-scheduler.md) .

**Problemas corrigidos:**

* A página verificar domínios DNS nem sempre reconheceu os domínios.
* Solicita credenciais de administrador de domínio ao configurar AD FS.
* As contas do AD local não são reconhecidas pelo assistente de instalação se estiverem localizadas em um domínio com uma árvore DNS diferente do domínio raiz.

## <a name="1091310"></a>1.0.9131.0
Liberado Dezembro de 2015

**Problemas corrigidos:**

* A sincronização de senha pode não funcionar quando você altera senhas no Active Directory Domain Services (AD DS), mas funciona quando você define uma senha.
* Quando você tiver um servidor proxy, a autenticação do Azure AD poderá falhar durante a instalação ou se uma atualização for cancelada na página de configuração.
* A atualização de uma versão anterior do Azure AD Connect com uma instância de SQL Server completa falhará se você não for um administrador de sistema do SQL Server (SA).
* A atualização de uma versão anterior do Azure AD Connect com um SQL Server remoto mostra o erro "não é possível acessar o banco de dados SQL do ADSync".

## <a name="1091250"></a>1.0.9125.0
Liberado Novembro de 2015

**Novas funcionalidades:**

* Pode reconfigurar AD FS para a relação de confiança do Azure AD.
* Pode atualizar o esquema de Active Directory e regenerar as regras de sincronização.
* Pode desabilitar uma regra de sincronização.
* Pode definir "AuthoritativeNull" como um novo literal em uma regra de sincronização.

**Novos recursos de visualização:**

* [Azure ad Connect Health para sincronização](how-to-connect-health-sync.md).
* Suporte para sincronização de senha do [Azure AD Domain Services](../user-help/active-directory-passwords-update-your-own-password.md) .

**Novo cenário com suporte:**

* Dá suporte a várias organizações do Exchange locais. Para obter mais informações, consulte [implantações híbridas com várias florestas Active Directory](https://technet.microsoft.com/library/jj873754.aspx).

**Problemas corrigidos:**

* Problemas de sincronização de senha:
  * Um objeto movido de fora do escopo para dentro do escopo não terá sua senha sincronizada. Isso inclui a filtragem de UO e de atributos.
  * A seleção de uma nova UO a ser incluída na sincronização não requer uma sincronização de senha completa.
  * Quando um usuário desabilitado estiver habilitado, a senha não será sincronizada.
  * A fila de repetição de senha é infinita e o limite anterior de 5.000 objetos a serem desativados foi removido.
* Não é possível se conectar a Active Directory com o nível funcional de floresta do Windows Server 2016.
* Não é possível alterar o grupo usado para filtragem de grupo após a instalação inicial.
* Não cria mais um novo perfil de usuário no servidor de Azure AD Connect para cada usuário que faz uma alteração de senha com o Write-back de senha habilitado.
* Não é possível usar valores inteiros longos em escopos de regras de sincronização.
* A caixa de seleção "Write-back de dispositivo" permanecerá desabilitada se houver controladores de domínio inacessíveis.

## <a name="1086670"></a>1.0.8667.0
Liberado Agosto de 2015

**Novas funcionalidades:**

* O assistente de instalação do Azure AD Connect agora está localizado em todos os idiomas do Windows Server.
* Suporte adicionado para desbloqueio de conta ao usar o gerenciamento de senhas do Azure AD.

**Problemas corrigidos:**

* Azure AD Connect assistente de instalação falhará se outro usuário continuar a instalação, e não a pessoa que iniciou a instalação pela primeira vez.
* Se uma desinstalação anterior do Azure AD Connect falhar ao desinstalar Azure AD Connect sincronização limpa, não será possível reinstalar.
* Não será possível instalar Azure AD Connect usando a instalação expressa se o usuário não estiver no domínio raiz da floresta ou se uma versão diferente do inglês do Active Directory for usada.
* Se o FQDN da conta de usuário do Active Directory não puder ser resolvido, uma mensagem de erro enganosa "falha ao confirmar o esquema" será mostrada.
* Se a conta usada no conector de Active Directory for alterada fora do assistente, o assistente falhará nas execuções subsequentes.
* Às vezes, o Azure AD Connect não é instalado em um controlador de domínio.
* Não é possível habilitar e desabilitar o "modo de preparo" se os atributos de extensão tiverem sido adicionados.
* O Write-back de senha falha em algumas configurações devido a uma senha inadequada no conector de Active Directory.
* O DirSync não poderá ser atualizado se um DN (nome diferenciado) for usado na filtragem de atributos.
* Uso excessivo da CPU ao usar a redefinição de senha.

**Recursos de visualização removidos:**

* O recurso de visualização [write-back de usuário](how-to-connect-preview.md#user-writeback) foi temporariamente removido com base nos comentários de nossos clientes de visualização. Ele será adicionado novamente mais tarde depois de solucionarmos os comentários fornecidos.

## <a name="1086410"></a>1.0.8641.0
Liberado Junho de 2015

**Versão inicial do Azure AD Connect.**

Nome alterado de Azure AD Sync para Azure AD Connect.

**Novas funcionalidades:**

* Instalação de [configurações expressas](how-to-connect-install-express.md)
* Pode [configurar AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* Pode [Atualizar do DirSync](how-to-dirsync-upgrade-get-started.md)
* [Impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Introduziu o [modo de preparo](how-to-connect-sync-staging-server.md)

**Novos recursos de visualização:**

* [Write-back do usuário](how-to-connect-preview.md#user-writeback)
* [Write-back de grupo](how-to-connect-preview.md#group-writeback)
* [Write-back do dispositivo](how-to-connect-device-writeback.md)
* [Extensões de diretórios](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Liberado Maio de 2015

**Novo requisito:**

* Agora Azure AD Sync requer que o .NET Framework versão 4.5.1 seja instalado.

**Problemas corrigidos:**

* O Write-back de senha do Azure AD está falhando com um erro de conectividade do barramento de serviço do Azure.

## <a name="104910413"></a>1.0.491.0413
Liberado Abril de 2015

**Problemas corrigidos e aprimoramentos:**

* O conector de Active Directory não processará as exclusões corretamente se a lixeira estiver habilitada e houver vários domínios na floresta.
* O desempenho das operações de importação foi aprimorado para o conector de Azure Active Directory.
* Quando um grupo excedeu o limite de associação (por padrão, o limite é definido como 50.000 objetos), o grupo foi excluído no Azure Active Directory. Com o novo comportamento, o grupo não é excluído, um erro é gerado e novas alterações de associação não são exportadas.
* Um novo objeto não poderá ser provisionado se uma exclusão em etapas com o mesmo DN já estiver presente no espaço do conector.
* Alguns objetos são marcados para sincronização durante uma sincronização Delta, embora não haja nenhuma alteração preparada no objeto.
* Forçar uma sincronização de senha também remove a lista de controladores de domínio preferenciais.
* CSExportAnalyzer tem problemas com alguns Estados de objetos.

**Novas funcionalidades:**

* Uma junção agora pode se conectar ao tipo de objeto "ANY" no MV.

## <a name="104850222"></a>1.0.485.0222
Liberado Fevereiro de 2015

**Melhorias:**

* Melhor desempenho de importação.

**Problemas corrigidos:**

* A sincronização de senha honra o atributo cloudFiltered usado pela filtragem de atributos. Os objetos filtrados não estão mais no escopo para a sincronização de senha.
* Em raras situações em que a topologia tinha muitos controladores de domínio, a sincronização de senha não funciona.
* "Parado-servidor" ao importar do conector do AD do Azure após a habilitação do gerenciamento de dispositivos no Azure AD/Intune.
* Ingressar FSPs (entidades de segurança externas) de vários domínios na mesma floresta causa um erro de junção ambígua.

## <a name="104751202"></a>1.0.475.1202
Liberado Dezembro de 2014

**Novas funcionalidades:**

* Agora há suporte para a sincronização de senha com filtragem baseada em atributo. Para obter mais informações, consulte [sincronização de senha com filtragem](how-to-connect-sync-configure-filtering.md).
* O atributo ms-DS-ExternalDirectoryObjectID é gravado novamente em Active Directory. Este recurso adiciona suporte para aplicativos do Office 365. Ele usa o OAuth2 para acessar caixas de correio online e locais em uma implantação híbrida do Exchange.

**Problemas de atualização corrigidos:**

* Uma versão mais recente do assistente de conexão está disponível no servidor.
* Foi usado um caminho de instalação personalizado para instalar o Azure AD Sync.
* Um critério de junção personalizado inválido bloqueia a atualização.

**Outras correções:**

* Corrigidos os modelos do Office Pro Plus.
* Correção de problemas de instalação causados por nomes de usuário que começam com um traço.
* Corrigida a perda da configuração sourceAnchor ao executar o assistente de instalação uma segunda vez.
* Corrigido o rastreamento de ETW para sincronização de senha.

## <a name="104701023"></a>1.0.470.1023
Liberado Outubro de 2014

**Novas funcionalidades:**

* Sincronização de senha de vários Active Directory locais para o Azure AD.
* Interface do usuário de instalação localizada em todos os idiomas do Windows Server.

**Atualizando do AADSync 1,0 GA**

Se você já tiver Azure AD Sync instalado, haverá uma etapa adicional que terá de ser executada caso você tenha alterado qualquer uma das regras de sincronização prontas para uso. Depois de ter atualizado para a versão do 1.0.470.1023, as regras de sincronização que você modificou serão duplicadas. Para cada regra de sincronização modificada, faça o seguinte:

1. Localize a regra de sincronização que você modificou e anote as alterações.
1. Exclua a regra de sincronização.
1. Localize a nova regra de sincronização criada por Azure AD Sync e reaplique as alterações.

**Permissões para a conta de Active Directory**

A conta de Active Directory deve receber permissões adicionais para poder ler os hashes de senha do Active Directory. As permissões a serem concedidas são chamadas "Replicando alterações de diretório" e "replicando todas as alterações de diretório". Ambas as permissões são necessárias para poder ler os hashes de senha.

## <a name="104190911"></a>1.0.419.0911
Liberado setembro de 2014

**Versão inicial do Azure AD Sync.**

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
