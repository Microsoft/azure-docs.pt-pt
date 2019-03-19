---
title: 'Azure AD Connect: Histórico de versões | Documentos da Microsoft'
description: Este artigo apresenta uma lista de todas as versões do Azure AD Connect e Azure AD Sync
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
ms.date: 12/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 840ea818c7c2e197f1ab65f4bd61067bf5e51283
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57836985"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Histórico de versões
A equipa do Azure Active Directory (Azure AD) atualiza regularmente o Azure AD Connect com novos recursos e funcionalidades. Nem todas as adições são aplicáveis a todos os públicos.


Este artigo destina-se para o ajudar a manter o controle das versões que tenham sido publicadas e para compreender quais são as alterações na versão mais recente.

Esta tabela é uma lista de tópicos relacionados:

Tópico |  Detalhes
--------- | --------- |
Passos para atualizar a partir do Azure AD Connect | Métodos diferentes para [atualizar de uma versão anterior para a versão mais recente](how-to-upgrade-previous-version.md) versão do Azure AD Connect.
Permissões obrigatórias | Para as permissões necessárias para aplicar uma atualização, consulte [contas e permissões](reference-connect-accounts-permissions.md#upgrade).

Transferir | [Transferir o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Nem todas as versões do Azure AD Connect serão disponibilizadas para atualização automática. O estado da versão irá indicar se uma versão for disponibilizada para atualização automática, ou apenas para download. Se a atualização automática foi ativada no seu servidor do Azure AD Connect, em seguida, esse servidor irão atualizar automaticamente para a versão mais recente do Azure AD Connect, que é fornecido para atualização automática. Tenha em atenção que nem todas as configurações do Azure AD Connect são elegíveis para atualização automática. Siga esta ligação para saber mais sobre [automaticamente a atualização](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)

## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Estado da versão

12/18/2018: Lançado para download

### <a name="fixed-issues"></a>Problemas corrigidos

Esta compilação atualiza os conectores não padrão (por exemplo, o conector LDAP genérico e o conector do SQL genérico) fornecidos com o Azure AD Connect. Para obter mais informações sobre conectores aplicável, consulte versão 1.1.911.0 na [histórico de versões do conector](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Estado da versão
12/11/2018: Lançado para download

### <a name="fixed-issues"></a>Problemas corrigidos
Esta correção de compilação permite ao utilizador selecionar um domínio de destino, dentro da floresta especificado, para o contentor de RegisteredDevices ao ativar a repetição de escrita do dispositivo.  Nas versões anteriores que contêm a nova funcionalidade de opções de dispositivo (1.1.819.0 – 1.2.68.0), a localização do contentor de RegisteredDevices estava limitada a raiz da floresta e não permitia que filho domínios.  Esta limitação apenas se manifesta em novas implementações – atualizações no local foram afetadas.  

Se qualquer compilação que contém a funcionalidade de opções do dispositivo atualizada foi implementada para um novo servidor e a repetição de escrita do dispositivo foi ativada, terá de especificar manualmente a localização do contentor, se não pretendê-lo na raiz da floresta.  Para tal, terá de desativar a repetição de escrita do dispositivo e volte a ativar, o que permitirá que especifique a localização do contentor na página "Floresta de repetição de escrita".



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Estado da versão 

11/30/2018:  Lançado para download

### <a name="fixed-issues"></a>Problemas corrigidos

Esta correção de compilação corrige um conflito de onde poderá ocorrer um erro de autenticação devido à presença independente do módulo MSOnline galeria do PowerShell no servidor de sincronização.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Estado da versão 

11/19/2018:  Lançado para download

### <a name="fixed-issues"></a>Problemas corrigidos

Esta correção de compilação corrige uma regressão na compilação anterior em que a repetição de escrita de palavra-passe falhar ao utilizar um controlador de domínio adiciona no Windows Server 2008/R2.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Estado da versão 

10/25/2018: lançado para download

 
### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos 


- Mudou a funcionalidade do atributo de write-back para garantir o correio de voz alojado está a funcionar conforme esperado.  Em determinados cenários, do Azure AD foi substituir o atributo msExchUcVoicemailSettings durante a repetição de escrita com um valor nulo.  O Azure AD agora limpará já não é o valor deste atributo no local se o valor na cloud que não está definido.
- Adicionado o diagnóstico no Assistente do Azure AD Connect para investigar e identificar problemas de conectividade para o Azure AD. Esse mesmo diagnóstico também pode ser executado diretamente através do Powershell com o Cmdlet Test - AdSyncAzureServiceConnectivity. 
- Adicionado o diagnóstico no Assistente do Azure AD Connect para investigar e identificar problemas de conectividade para o AD. Esse mesmo diagnóstico também pode ser executado diretamente através do Powershell usando a função de início ConnectivityValidation no módulo do ADConnectivityTools Powershell.  Para obter mais informações consulte [o que é o módulo do PowerShell ADConnectivityTool?](how-to-connect-adconnectivitytools.md)
- Adicionada uma verificação prévia da versão do esquema AD para híbrida do Azure Active Directory Join e repetição de escrita do dispositivo 
- Alterar a procura de atributo de página de extensão de diretório para ser o caso de não sensíveis.
-   Foi adicionado suporte completo para o TLS 1.2. Esta versão suporta todos os outros protocolos a ser desativados e apenas TLS 1.2 seja ativado na máquina onde o Azure AD Connect está instalado.  Para obter mais informações consulte [imposição de TLS 1.2 para o Azure AD Connect](reference-connect-tls-enforcement.md)

 

### <a name="fixed-issues"></a>Problemas corrigidos   

- Foi corrigido um erro em que o Azure AD Connect atualizar falharia se o SQL Always On estava a ser utilizada. 
- Foi corrigido um erro ao analisar corretamente os nomes de UO que contenham uma barra. 
- Foi corrigido um problema em que seria possível desativar a autenticação pass-through para uma instalação limpa no modo de teste. 
- Foi corrigido um erro que impediu o módulo do PowerShell para ser carregado quando executar as ferramentas de resolução de problemas 
- Foi corrigido um erro que seria impedir que os clientes com valores numéricos no primeiro caráter de um nome de anfitrião. 
- Foi corrigido um erro em que o Azure AD Connect permitiria partições inválidas e seleção de contentor 
- Corrigida a mensagem de erro "Palavra-passe inválida" quando o SSO de ambiente de trabalho está ativado. 
- Várias correções de erros para a gestão de confiança do AD FS  
- Quando configurar a repetição de escrita do dispositivo - fixo a verificação de esquema para procurar a classe de objeto msDs-DeviceContainer (introduzida no WS2012 R2)

 
## <a name="118820"></a>1.1.882.0  

9/7/2018: lançado para download, não será a versão de atualização automática 

### <a name="fixed-issues"></a>Problemas corrigidos  

O Azure AD Connect atualizar falhará se sempre na disponibilidade do SQL está configurada para a BD do ADSync. Esta correção resolve esse problema e permite a atualização com êxito. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Estado da versão

8/21/2018: Lançado para download e a atualização automática. 

### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos

- A integração de federar o Ping de mensagens em fila no Azure AD Connect está agora disponível para disponibilidade geral. [Saiba mais sobre como federado do Azure AD com Ping federar](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- O Azure AD Connect cria agora a cópia de segurança de confiança do Azure AD no AD FS, sempre que uma atualização é efetuada e armazena-a num arquivo separado para o fácil restauro, se necessário. [Saiba mais sobre a nova funcionalidade e o Azure AD confiam gestão no Azure AD Connect](https://aka.ms/fedtrustinaadconnect).
- Novas ferramentas de resolução de problemas ajuda a resolver problemas relacionados com a alteração de endereço de e-mail principal e ocultando a conta na lista de endereços global
- O Azure AD Connect foi atualizado para incluir o SQL Server 2012 Native Client mais recente
- Quando mudar para a sincronização de Hash de palavra-passe ou autenticação pass-through utilizador inicie sessão na tarefa de "Alteração utilizador início de sessão", a caixa de verificação simples início de sessão único está ativada por predefinição.
- Foi adicionado suporte para Windows Server Essentials 2019
- O agente do Azure AD Connect Health foi atualizado para a versão mais recente 3.1.7.0
- Durante uma atualização, se o instalador Deteta alterações às regras de sincronização padrão, o administrador é pedido com um aviso antes de substituir as regras de modificação. Isso permitirá que o usuário tome medidas corretivas e retomar mais tarde. Comportamento antigo: Se tiver ocorrido qualquer regra de out-of-box modificada, em seguida, manual atualização foi substituir essas regras sem dar qualquer aviso ao utilizador e o agendador de sincronização foi desativado sem informar o utilizador. Novo comportamento: Será pedido ao utilizador com o aviso antes de substituir as regras de sincronização de out-of-box modificado. Usuário terá a opção para parar o processo de atualização e retomar mais tarde depois de tomar medidas corretivas necessárias.
- Fornece um melhor processamento de um problema de conformidade FIPS, fornecendo uma mensagem de erro para a geração de hashes MD5 num ambiente compatível com FIPS e uma ligação para a documentação que fornece uma solução para este problema.
- Atualizar a interface do Usuário para melhorar as tarefas de Federação no assistente, que agora estão num grupo de sub-rotina separado para a Federação. 
- Todas as tarefas adicionais de Federação agora são agrupadas num único submenu de Facilidade de utilização.
- Um novo módulo de Posh ADSyncConfig (AdSyncConfig.psm1) renovado com novas funções de permissões do AD movidas do ADSyncPrep.psm1 antigo (o que pode ser preterido em breve)

### <a name="fixed-issues"></a>Problemas corrigidos 

- Foi corrigido um erro em que o servidor do AAD Connect mostraria elevada utilização da CPU após a atualização para o .NET 4.7.2
- Foi corrigido um erro que intermitentemente produziria uma mensagem de erro para um problema de deadlock SQL resolvido automaticamente
- Corrigidos vários problemas de acessibilidade para Editor de regras de sincronização e o Gestor do serviço de sincronização  
- Foi corrigido um erro em que o Azure AD Connect não é possível obter as informações de definição de registo
- Foi corrigido um erro que criar problemas quando o utilizador acede a partir de agora/voltar no Assistente
- Foi corrigido um erro para impedir que um erro ocorre devido a thread multi incorreto no Assistente de processamento
- Quando a página de filtragem de sincronização de grupo encontra um erro LDAP ao resolver a grupos de segurança, o Azure AD Connect, agora, retorna a exceção com total fidelidade.  A causa de raiz para a exceção de referência é ainda desconhecida e será resolvida por um bug diferente.
-  Foi corrigido um erro em que as permissões para chaves STK e NGC (ms-DS-KeyCredentialLink atributo em objetos de dispositivo/utilizador para WHfB) não foram corretamente definidas.     
- Foi corrigido um erro em que 'Set-ADSyncRestrictedPermissions' não foi chamado corretamente
-  A adicionar suporte para a permissão que concede no grupo de repetição de escrita no Assistente de instalação do AADConnect
- Quando alterar método de início de sessão de sincronização de Hash de palavra-passe para o AD FS, a sincronização de Hash de palavra-passe não foi desativada.
- Verificação adicional para endereços IPv6 na configuração do AD FS
- Atualizado a mensagem de notificação a informar de que existe uma configuração existente.
- Repetição de escrita do dispositivo não consegue detetar o contentor na floresta não fidedigna. Isso foi atualizado para fornecer uma mensagem de erro melhor e uma ligação para a documentação apropriada
- Desmarcando uma UO e, em seguida, a sincronização/repetição de escrita correspondente a UO oferece a um erro genérico de sincronização. Isso foi alterado para criar uma mensagem de erro mais compreensível.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Estado da versão

5/14/2018: Lançado para atualização automática e a transferência.

### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos

Novos recursos e aperfeiçoamentos

- Esta versão inclui a pré-visualização pública a integração do PingFederate no Azure AD Connect. Com esta versão, os clientes podem facilmente e com confiança, configure seu ambiente do Azure Active Directory para tirar partido do PingFederate como respetivo fornecedor de Federação. Para saber mais sobre como utilizar esta nova funcionalidade, visite nosso [documentação online](plan-connect-user-signin.md#federation-with-pingfederate). 
- Atualizado o Azure AD ligar assistente resolução de problemas de utilitário, onde agora o assistente analisa mais cenário de erro, como caixas de correio ligada e grupos dinâmicos do AD. Saiba mais sobre o utilitário de resolução de problemas [aqui](tshoot-connect-objectsync.md).
- Configuração de repetição de escrita do dispositivo é agora gerida unicamente no Assistente do Azure para ligar AD.
- Um novo módulo do PowerShell chamado ADSyncTools.psm1 é adicionado que pode ser utilizado para resolver problemas de conectividade de SQL e vários outros utilitários de resolução de problemas. Saiba mais sobre o módulo de ADSyncTools [aqui](tshoot-connect-tshoot-sql-connectivity.md). 
- Foi adicionada uma nova tarefa adicional "Configurar opções de dispositivo". Pode utilizar a tarefa para configurar as seguintes duas operações: 
  - **Associação ao Azure AD híbrido**: Se o seu ambiente tiver uma infraestrutura do AD no local e também quiser tirar partido das capacidades que o Azure Active Directory oferece, pode implementar dispositivos híbridos associados ao Azure AD. Estes são dispositivos associados tanto ao Active Directory no local, como ao Azure Active Directory.
  - **Repetição de escrita do dispositivo**: Repetição de escrita do dispositivo é utilizada para ativar o acesso condicional com base em dispositivos para o AD FS (2012 R2 ou superior) protegidos de dispositivos

    >[!NOTE] 
    > - A opção de ativar a repetição de escrita do dispositivo de personalizar as opções de sincronização estarão esbatida. 
    > -  O módulo do PowerShell para ADPrep foi preterido com esta versão.



### <a name="fixed-issues"></a>Problemas corrigidos 

- Esta versão atualiza a instalação do SQL Server Express para SQL Server 2012 SP4, que, entre outros, fornece correções para várias vulnerabilidades de segurança.  Veja [aqui](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) para obter mais informações sobre o SQL Server 2012 SP4.
- Processamento da regra de sincronização: regras de sincronização de associação de saída sem condição de associação devem ser aplicadas anular se a regra de sincronização principal já não é aplicável
- Várias correções de acessibilidade foram aplicadas para a interface do Usuário do Synchronization Service Manager e o Editor de regras de sincronização
- Assistente do Azure AD Connect: Erro ao criar a conta de conector AD quando o Azure AD Connect é um grupo de trabalho
- Assistente do Azure AD Connect: Na página do Azure AD início de sessão a apresentar a caixa de verificação de verificação, sempre que houver qualquer erro de correspondência em domínios de AD e do Azure AD verificado
- Correção do PowerShell de atualização automática para definir o estado de atualização automática corretamente em determinados casos após a atualização automática tentada.
- Assistente do Azure AD Connect: Telemetria atualizada para capturar as informações anteriormente em falta
- Assistente do Azure AD Connect: As seguintes alterações foram feitas ao utilizar o **alterar utilizador inicie sessão** tarefas para mudar do AD FS para autenticação pass-through:
    - O agente de autenticação pass-through está instalado no servidor do Azure AD Connect e a funcionalidade de autenticação pass-through está ativada, antes que nós convertemo domínios de federado para o managed.
    - Os utilizadores já não são convertidos de federado para gerido. Apenas domínios são convertidos.
- Assistente do Azure AD Connect: Regex de domínio do AD FS múltipla não está correta quando tem de UPN de utilizador "a atualização do caráter especial Regex suporta carateres especiais
- Assistente do Azure AD Connect: Remover a mensagem de "Atributo de âncora de origem de configurar" falsa quando nenhuma alteração 
- Assistente do Azure AD Connect: Suporte do AD FS para o cenário de Federação dupla
- Assistente do Azure AD Connect: Afirmações do AD FS não são atualizadas para o domínio foi adicionado ao converter um domínio gerido federado
- Assistente do Azure AD Connect: Durante a deteção de pacotes instalados, encontramos obsoleto Dirsync/Azure AD Sync/do Azure AD Connect produtos relacionados. Vamos agora tentar desinstalar os produtos obsoletos.
- Assistente do Azure AD Connect: Corrija o mapeamento da mensagem de erro quando ocorre uma falha de instalação do agente de autenticação pass-through
- Assistente do Azure AD Connect: Contentor de "Configuração" foi removido da página de filtragem de UO do domínio
- Instalar o motor de sincronização: Remover desnecessária lógica herdada que, ocasionalmente, a falha do motor de sincronização instalação msi
- Assistente do Azure AD Connect: Corrigir o texto de ajuda do pop-up na página de funcionalidades opcionais para sincronização de Hash de palavra-passe
- Runtime do motor de sincronização: Correção de eliminar o cenário em que um objeto de CS tem um importados e regras de sincronização tentarem aprovisionar novamente o objeto.
- Runtime do motor de sincronização: Adicionar ligação de ajuda para conectividade Online Guia para o registo de eventos de resolução de problemas para um erro de importação
- Runtime do motor de sincronização: Utilização de memória reduzida do agendador de sincronização ao enumerar os conectores
- Assistente do Azure AD Connect: Corrigir um problema de resolver uma conta de serviço personalizado de sincronização que tem sem privilégios de leitura do AD
- Assistente do Azure AD Connect: Melhorar o registo de domínio e selecione as opções de filtragem de UO
- Assistente do Azure AD Connect: A predefinição de adicionar FS AD afirma confiança da Federação criada para o cenário MFA
- Assistente do Azure AD Connect: Implementar o WAP do AD FS: A adição do servidor não consegue utilizar o novo certificado
- Assistente do Azure AD Connect: Exceção de DSSO quando onPremCredentials não são inicializados para um domínio 
- Fluxo preferencialmente o atributo distinguishedName do AD do objeto de utilizador do Active Directory.
- Foi corrigido um bug cosméticas foram a precedência da regra de sincronização de OOB primeiro foi definida para 99, em vez de 100



## <a name="117510"></a>1.1.751.0
Estado de 4/12/2018: Lançadas apenas para download

>[!NOTE]
>Esta versão é uma correção para o Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Sincronização do Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
Foi corrigido um problema foram deteção automática de instância do Azure para inquilinos da China, ocasionalmente, foi a falhar.  

### <a name="ad-fs-management"></a>Gestão do AD FS
#### <a name="fixed-issues"></a>Problemas corrigidos

Ocorreu um problema na lógica de repetição de configuração que resultaria numa ArgumentException que diz "já foi adicionado um item com a mesma chave."  Isso faria com que todas as operações de repetição efetuar a ativação.

## <a name="117500"></a>1.1.750.0
Estado 3/22/2018: Lançado para atualização automática e a transferência.
>[!NOTE]
>Quando tiver concluído a atualização para esta nova versão, irá acionar automaticamente uma sincronização completa e a importação completa para o conector do Azure AD e uma sincronização completa para o conector do AD. Uma vez que isto pode demorar algum tempo, dependendo do tamanho do seu ambiente do Azure AD Connect, certifique-se de que seguiu os passos necessários para oferecer suporte a isso ou adiar a atualização até encontrar um momento conveniente para fazer isso.

>[!NOTE]
>"Funcionalidade AutoUpgrade incorretamente foi desativada para alguns inquilinos que implementaram compilações posterior à 1.1.524.0. Para garantir que a sua instância do Azure AD Connect é continua a ser elegível para AutoUpgrade, execute o seguinte cmdlet do PowerShell: "Set-ADSyncAutoUpgrade - AutoupGradeState ativada"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos

* Cmdlet Set-ADSyncAutoUpgrade anteriormente bloquearia Autoupgrade se o estado de atualização automática está definido como suspenso. Esta funcionalidade foi alterado, de modo que não bloqueie AutoUpgrade das compilações futuras.
* Alterado de **sessão do utilizador** página a opção "Sincronização de palavra-passe" para "Sincronização de Hash de palavra-passe".  Azure AD Connect sincroniza os hashes de palavra-passe, não as palavras-passe, para que este está alinhado com o que realmente está ocorrendo.  Para obter mais informações consulte [implementar a sincronização de hash de palavra-passe com a sincronização do Azure AD Connect](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Estado: Lançado para selecionar clientes

>[!NOTE]
>Quando tiver concluído a atualização para esta nova versão, irá acionar automaticamente uma sincronização completa e a importação completa para o conector do Azure AD e uma sincronização completa para o conector do AD. Uma vez que isto pode demorar algum tempo, dependendo do tamanho do seu ambiente do Azure AD Connect, certifique-se de que seguiu os passos necessários para oferecer suporte a isso ou adiar a atualização até encontrar um momento conveniente para fazer isso.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigi a janela de tempo em tarefas em segundo plano para a página de filtragem de partições quando mudar para a página seguinte.

* Foi corrigido um erro que provocou a violação de acesso durante a ação personalizada ConfigDB.

* Foi corrigido um erro ao recuperar a partir de tempo limite da ligação de SQL.

* Foi corrigido um erro em que os certificados com carateres universais de SAN falharam uma verificação de pré-requisitos.

* Foi corrigido um erro que faz com que miiserver.exe falhem durante uma exportação de conector do Azure AD.

* Foi corrigido um erro que tentativa de palavra-passe incorreta com sessão iniciada no controlador de domínio ao executar o Assistente do Azure AD Connect para alterar a configuração.


#### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos

* A adicionar definições de privacidade para o Regulamento de proteção de dados gerais (GDPR).  Para obter mais informações, consulte o artigo [aqui](reference-connect-user-privacy.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* telemetria de aplicações - administrador pode mudar este tipo de dados/desativar à vontade

* Dados de estado de funcionamento do AD do Azure - o administrador tem de visitar o portal de estado de funcionamento para controlar as definições de estado de funcionamento.
   Assim que a política do serviço tiver sido alterada, os agentes serão ler e impor-lo.

* Dispositivo adicionado repetição de escrita de ações de configuração e uma barra de progresso para inicialização da página

* Aprimorada gerais de diagnóstico com o relatório em HTML e recolha de dados completo num texto ZIP / relatório em HTML

* A fiabilidade de atualização automática e telemetria adicional foi adicionada para garantir que o estado de funcionamento do servidor de pode ser determinado melhorada

* Restringir permissões disponíveis para contas com privilégios na conta de conector AD

  * Para instalações novas, o assistente irá restringir as permissões que contas privilegiadas existentes na conta MSOL depois de criar a conta MSOL.

As alterações encarrega-se do seguinte:
1. Express instalações
2. Instalações personalizadas com a criação de conta
3. Alterar o instalador para que ele não requer privilégios de SA numa instalação limpa do Azure AD Connect

* Adicionado um novo utilitário para resolver problemas de sincronização para um objeto específico. Está disponível na opção "Resolver problemas de sincronização de objetos" do Azure AD Connect assistente resolver problemas de tarefa adicional. Atualmente, o utilitário verifica o seguinte:

  * Erro de correspondência de UserPrincipalName entre a conta de utilizador no inquilino do Azure AD e o objeto de utilizador sincronizado.
  * Se o objeto é filtrado em sincronização devido a filtragem de domínio
  * Se o objeto é filtrado em sincronização devido a unidade organizacional (UO), filtragem

* Adicionado um novo utilitário para sincronizar o hash de palavra-passe atual armazenado no diretório do Active Directory no local para uma conta de utilizador específico.

O utilitário não requer uma alteração de palavra-passe. Está disponível na opção "Resolver problemas de palavra-passe a sincronização de Hash" do Azure AD Connect assistente resolver problemas de tarefa adicional.






## <a name="116540"></a>1.1.654.0
Estado: 12 de Dezembro de 2017

>[!NOTE]
>Esta versão é uma segurança correção relacionada do Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
Uma melhoria foi adicionada para o Azure AD Connect versão 1.1.654.0 (e depois) para se certificar de que a permissão recomendada alterações descrita na secção [bloquear o acesso à conta do AD DS](#lock) são aplicadas automaticamente quando o Azure AD Ligue-se cria a conta do AD DS. 

- Durante a configuração do Azure AD Connect, o administrador de instalação pode fornecer uma conta existente do AD DS ou permitir que o Azure AD Connect, criar automaticamente a conta. As alterações de permissão são aplicadas automaticamente à conta do AD DS que é criada pelo Azure AD Connect durante a configuração. Não são aplicadas a conta existente do AD DS fornecida pelo administrador instalar.
- Para os clientes que atualizaram de uma versão mais antiga do Azure AD Connect para 1.1.654.0 (ou depois), a permissão de alterações não serão retroativamente aplicadas a contas existentes do AD DS criadas antes da atualização. Só serão aplicadas a novas contas do AD DS criadas após a atualização. Isto ocorre quando estiver a adicionar novas florestas do AD sejam sincronizados com o Azure AD.

>[!NOTE]
>Esta versão apenas remove a vulnerabilidade para novas instalações do Azure AD Connect, onde a conta de serviço é criada pelo processo de instalação. Para as instalações existentes, ou nos casos em que forneça a conta por conta própria, deve garantir que essa vulnerabilidade não existe.

#### <a name="lock"></a> Bloquear o acesso à conta do AD DS
Bloquear acesso à conta do AD DS ao implementar as seguintes alterações de permissão no local AD:  

*   Desativar a herança do objeto especificado
*   Remova todas as ACEs de objeto específico, exceto ACEs específicos para o próprio. Queremos manter as permissões predefinidas intactos quando se trata-se para o próprio.
*   Atribua estas permissões específicas:

Type     | Name                          | Access               | Aplica-se A
---------|-------------------------------|----------------------|--------------|
Permitir    | SISTEMA                        | Controlo Total         | Este objeto  |
Permitir    | Administradores da empresa             | Controlo Total         | Este objeto  |
Permitir    | Admins do domínio                 | Controlo Total         | Este objeto  |
Permitir    | Administradores                | Controlo Total         | Este objeto  |
Permitir    | Controladores de domínio de empresa | Listar conteúdo        | Este objeto  |
Permitir    | Controladores de domínio de empresa | Ler todas as propriedades  | Este objeto  |
Permitir    | Controladores de domínio de empresa | Permissões de Leitura     | Este objeto  |
Permitir    | Utilizadores Autenticados           | Listar conteúdo        | Este objeto  |
Permitir    | Utilizadores Autenticados           | Ler todas as propriedades  | Este objeto  |
Permitir    | Utilizadores Autenticados           | Permissões de Leitura     | Este objeto  |

Para reforçar as definições para o AD DS conta que pode ser executado [este script do PowerShell](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). O script do PowerShell irá atribuir as permissões mencionadas acima para a conta do AD DS.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Script do PowerShell para reforçar a uma conta de serviço já existente

Para utilizar o script do PowerShell, para aplicar estas definições, para uma conta já existente do AD DS, (ether fornecidos pela sua organização ou criado por uma instalação anterior do Azure AD Connect, transfira o script da ligação fornecida acima.

##### <a name="usage"></a>Utilização:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Onde 

**$ObjectDN** = conta o Active Directory tem de ser fortalecida cujas permissões.

**$Credential** = as credenciais de administrador que tem os privilégios necessários para restringir as permissões na conta $ObjectDN. Esses privilégios são geralmente mantinham pelo administrador do domínio ou Enterprise. Utilize o nome de domínio completamente qualificado da conta de administrador para evitar falhas de pesquisa de conta. Exemplo: contoso.com\admin.

>[!NOTE] 
>$credential. Nome de utilizador deve estar no formato de FQDN\username. Exemplo: contoso.com\admin 

##### <a name="example"></a>Exemplo:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Essa vulnerabilidade foi utilizada para obter acesso não autorizado?

Para ver se essa vulnerabilidade foi utilizada para comprometer o Azure AD configuração Connect, deve verificar a última palavra-passe, repor a data da conta de serviço.  Se a timestamp no inesperado, uma análise mais aprofundada, através do log de eventos, para essa palavra-passe de reposição de evento, deve ser realizada uma.

Para obter mais informações, consulte [4056318 de consultoria de segurança do Microsoft](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Estado: 27 de Outubro de 2017

>[!NOTE]
>Esta compilação não está disponível para clientes através da funcionalidade do Azure AD Connect automática atualizar.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Foi corrigido o problema
* Foi corrigido um problema de compatibilidade de versão entre o Azure AD Connect e o agente do Azure AD Connect Health (para sincronização). Este problema afeta os clientes que estejam a executar o Azure AD Connect no local atualizar para versão 1.1.647.0, mas atualmente tem o agente de estado de funcionamento da versão 3.0.127.0. Após a atualização, o agente de estado de funcionamento não podem mais enviar dados de estado de funcionamento sobre o serviço do Azure AD Connect sincronização ao serviço de estado de funcionamento do Azure AD. Com essa correção, o agente de estado de funcionamento da versão 3.0.129.0 é instalado durante a atualização do Azure AD Connect no local. Agente de estado de funcionamento versão 3.0.129.0 não tem problema de compatibilidade com o Azure AD Connect versão 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Estado: 19 de Outubro de 2017

> [!IMPORTANT]
> Existe um problema de compatibilidade conhecidos entre o Azure AD Connect versão 1.1.647.0 e versão do agente do Azure AD Connect Health (para sincronização) 3.0.127.0. Este problema impede o agente de estado de funcionamento de envio de dados de estado de funcionamento sobre o serviço do Azure AD Connect sincronização (incluindo erros de sincronização de objetos e dados de histórico de execuções) para o serviço de estado de funcionamento do Azure AD. Antes de atualizar manualmente a sua implementação do Azure AD Connect para a versão 1.1.647.0, verifique se que a versão atual do agente do Azure AD Connect Health instalado no seu servidor do Azure AD Connect. Pode fazê-lo acedendo a *painel de controlo → Adicionar/remover programas* e procure a aplicação *Microsoft agente Azure AD Connect Health para sincronização*. Se a sua versão for 3.0.127.0, recomenda-se que aguardar a próxima versão do Azure AD Connect estar disponível antes da atualização. Se a versão do agente de estado de funcionamento não estiver 3.0.127.0, não há problema em continuar com a atualização manual, no local. Tenha em atenção que este problema não afeta a atualização de swing ou clientes que estejam a executar uma instalação nova do Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
* Foi corrigido um problema com o *alterar utilizador inicie sessão* tarefas no Assistente do Azure AD Connect:

  * O erro ocorre quando tiver uma implementação existente do Azure AD Connect com a sincronização de palavra-passe **habilitado**, e está a tentar definir o método de início de sessão do utilizador como *autenticação pass-through*. Antes da alteração é aplicada, o assistente incorretamente mostra a "*desativar a sincronização de palavra-passe*" linha de comandos. No entanto, a sincronização de palavra-passe permanece ativada após a alteração é aplicada. Com essa correção, o assistente já não apresenta a linha de comandos.

  * Por predefinição, o assistente não desativar a sincronização de palavra-passe quando atualizar o método de início de sessão de utilizador utilizando o *alterar utilizador inicie sessão* tarefas. Isso é para evitar a interrupção para os clientes que desejam manter a sincronização de palavra-passe, mesmo que eles ativar a autenticação pass-through ou federação como método de início de sessão de utilizador primário.
  
  * Se pretender desativar a sincronização de palavra-passe depois de atualizar o método de início de sessão do utilizador, tem de executar o *personalizar a configuração de sincronização* tarefas no assistente. Quando navegar para o *funcionalidades opcionais* página, desmarque a *sincronização de palavra-passe* opção.
  
  * Tenha em atenção que o mesmo problema também ocorre se tentar ativar/desativar de sessão único totalmente integrado. Especificamente, tem uma implementação existente do Azure AD Connect com a sincronização de palavra-passe ativada e o método de início de sessão do utilizador já está configurado como *autenticação pass-through*. Utilizar o *alterar utilizador inicie sessão* tarefa, tentar marcar/desmarcar o *ativar totalmente integrada Single Sign-On* opção enquanto o método de início de sessão do utilizador continua configurado como "Autenticação pass-through". Antes da alteração é aplicada, o assistente incorretamente mostra a "*desativar a sincronização de palavra-passe*" linha de comandos. No entanto, a sincronização de palavra-passe permanece ativada após a alteração é aplicada. Com essa correção, o assistente já não apresenta a linha de comandos.

* Foi corrigido um problema com o *alterar utilizador inicie sessão* tarefas no Assistente do Azure AD Connect:

  * O erro ocorre quando tiver uma implementação existente do Azure AD Connect com a sincronização de palavra-passe **desativada**, e está a tentar definir o método de início de sessão do utilizador como *autenticação pass-through*. Quando a alteração é aplicada, o assistente ativa a autenticação pass-through e sincronização de palavra-passe. Com essa correção, o assistente já não permite a sincronização de palavra-passe.

  * Anteriormente, a sincronização de palavra-passe era um pré-requisito para ativar a autenticação pass-through. Ao definir o método de início de sessão do utilizador como *autenticação pass-through*, o assistente seria ativar a autenticação pass-through e sincronização de palavra-passe. Recentemente, a sincronização de palavra-passe foi removida como um pré-requisito. Como parte do Azure AD Connect versão 1.1.557.0, foi efetuada uma alteração para o Azure AD Connect para não ativar a sincronização de palavra-passe, quando definir o método de início de sessão do utilizador como *autenticação pass-through*. No entanto, a alteração só foi aplicada para instalação do Azure AD Connect. Com essa correção, a mesma alteração também é aplicada para o *alterar utilizador inicie sessão* tarefas.
  
  * Tenha em atenção que o mesmo problema também ocorre se tentar ativar/desativar de sessão único totalmente integrado. Especificamente, tem uma implementação existente do Azure AD Connect com a sincronização de palavra-passe desativada e o método de início de sessão do utilizador já está configurado como *autenticação pass-through*. Utilizar o *alterar utilizador inicie sessão* tarefa, tentar marcar/desmarcar o *ativar totalmente integrada Single Sign-On* opção enquanto o método de início de sessão do utilizador continua configurado como "Autenticação pass-through". Quando a alteração é aplicada, o assistente permite a sincronização de palavra-passe. Com essa correção, o assistente já não permite a sincronização de palavra-passe. 

* Foi corrigido um problema que causou a atualização do Azure AD Connect efetuar a ativação com o erro "*não é possível atualizar o serviço de sincronização*". Além disso, o serviço de sincronização já não pode começar com o erro de evento "*o serviço não conseguiu iniciar porque a versão da base de dados é mais recente do que a versão dos binários do instalado*". O problema ocorre quando o administrador efetuar a atualização não tem o privilégio de administrador do sistema para o SQL server que está a ser utilizado pelo Azure AD Connect. Com essa correção, o Azure AD Connect requer apenas o administrador ter o privilégio de db_owner na base de dados ADSync durante a atualização.

* Foi corrigido um problema do Azure AD Connect atualizar que os clientes que tem ativado de afetados [totalmente integrada Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Depois de atualizar o Azure AD Connect, totalmente integrada Single Sign-On incorretamente é apresentada como desativadas no Assistente do Azure AD Connect, mesmo que a funcionalidade permaneça ativada e totalmente funcionais. Com essa correção, a funcionalidade é agora apresentado corretamente como ativada no assistente.

* Foi corrigido um problema que causou o Assistente do Azure AD Connect para sempre mostrar a "*configurar âncora de origem*" prompt no *pronto para configurar* página, mesmo que não relacionadas com a âncora de origem foram feitas alterações.

* Quando efetuar a atualização manual de no local do Azure AD Connect, o cliente deve fornecer as credenciais de Administrador Global do inquilino do Azure AD correspondente. Anteriormente, a atualização foi possível continuar, apesar das credenciais do Administrador Global pertenciam a um Azure diferente inquilino do AD. Enquanto a atualização é apresentado concluir com êxito, determinadas configurações não são mantidas corretamente com a atualização. Com esta alteração, o assistente impede que a atualização prosseguir se as credenciais fornecidas não corresponder ao inquilino do Azure AD.

* Removido com redundância de lógica que desnecessariamente reiniciar o serviço do Azure AD Connect Health no início de uma atualização manual.


#### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos
* Foi adicionada lógica para simplificar as etapas necessárias para configurar o Azure AD Connect com Microsoft Germany Cloud. Anteriormente, é necessário para atualizar as chaves de registo específicas no servidor do Azure AD Connect para que funcione corretamente com o Microsoft Cloud da Alemanha, conforme descrito neste artigo. Agora, o Azure AD Connect pode detetar automaticamente se o inquilino no Germany Cloud da Microsoft baseia-se nas credenciais de administrador global fornecidas durante a configuração.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
> [!NOTE]
> Nota: O serviço de sincronização tem uma interface WMI que permite desenvolver seu próprio agendador personalizado. Esta interface foi despromovido e será removida do futuro lançamento de versões do Azure AD Connect após 30 de Junho de 2018. Os clientes que pretendem personalizar agenda de sincronização devem utilizar o [agendador interno (https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Problemas corrigidos
* Quando o Assistente do Azure AD Connect cria a conta de conector AD necessária para sincronizar as alterações do Active Directory no local, ele não corretamente atribui a conta a permissão necessária para ler objetos de PublicFolder. Este problema afeta a instalação do Express e a instalação personalizada. Esta alteração corrige o problema.

* Foi corrigido um problema que causou a página de resolução de problemas do assistente Ligar do Azure AD para não ser apresentado corretamente para administradores que executarem a partir do Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos
* Quando a sincronização de palavra-passe utilizando o Assistente do Azure AD Connect, página de resolução de problemas de resolução de problemas, a página resolução de problemas agora devolve o estado de domínios específicos.

* Anteriormente, se tentou ativar a sincronização de Hash de palavra-passe, o Azure AD Connect não verifica se a conta de conector AD possui permissões necessárias para sincronizar os hashes de palavra-passe do AD no local. Agora, o Assistente do Azure AD Connect irá verificar e avisá-lo se a conta de conector AD não tem permissões suficientes.

### <a name="ad-fs-management"></a>Gestão do AD FS
#### <a name="fixed-issue"></a>Foi corrigido o problema
* Foi corrigido um problema relacionado com a utilização de [ms-DS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) funcionalidade. Este problema afeta os clientes que configuraram *federação com o AD FS* como o método de início de sessão do utilizador. Quando executa *configurar âncora de origem* muda de tarefas no assistente, o Azure AD Connect para utilizar * ms-DS-ConsistencyGuid como atributo de origem de immutableId. Como parte dessa alteração, o Azure AD Connect tenta atualizar as regras de afirmação de ImmutableId no AD FS. No entanto, este passo falhou porque o Azure AD Connect não tem as credenciais de administrador necessárias para configurar o AD FS. Com essa correção, o Azure AD Connect agora pede-lhe para introduzir as credenciais de administrador do AD FS quando executar o *configurar âncora de origem* tarefas.



## <a name="116140"></a>1.1.614.0
Estado: 05 de Setembro de 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Problemas conhecidos
* Existe um problema conhecido que está a causar o Azure AD Connect atualização falha com o erro "*não é possível atualizar o serviço de sincronização*". Além disso, o serviço de sincronização já não pode começar com o erro de evento "*o serviço não conseguiu iniciar porque a versão da base de dados é mais recente do que a versão dos binários do instalado*". O problema ocorre quando o administrador efetuar a atualização não tem o privilégio de administrador do sistema para o SQL server que está a ser utilizado pelo Azure AD Connect. Permissões de dbo não são suficientes.

* Existe um problema conhecido com o Azure AD Connect atualizar que está a afetar os clientes que tem ativado [totalmente integrada Single Sign-On](how-to-connect-sso.md). Depois de atualizar o Azure AD Connect, a funcionalidade é apresentado como desativadas no assistente, mesmo que a funcionalidade permaneça ativada. Uma correção para este problema será fornecido em futuras versões. Os clientes que estão preocupados com este problema de apresentação manualmente podem corrigi-lo ao ativar totalmente integrada início de sessão único no assistente.

#### <a name="fixed-issues"></a>Problemas corrigidos
* Foi corrigido um problema que impediu a atualizar as regras de afirmações no local do Azure AD Connect do AD FS, enquanto permite que o [ms-DS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) funcionalidade. O problema ocorre se tentar ativar a funcionalidade para uma implementação do Azure AD Connect existente que tenha configurado como método de início de sessão do AD FS. O problema ocorre porque o assistente não solicita credenciais de AD FS antes de tentar atualizar as regras de afirmações no AD FS.
* Foi corrigido um problema que causou o Azure AD Connect para falhar a instalação se no local tem de floresta do AD NTLM desativada. O problema é devido ao Assistente do Azure AD Connect não fornecer credenciais completamente qualificadas ao criar os contextos de segurança necessários para a autenticação Kerberos. Isso faz com que a autenticação Kerberos falhar e Assistente do Azure AD Connect para reverter para utilizando o NTLM.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Problemas corrigidos
* Foi corrigido um problema em que não é possível criar uma nova regra de sincronização se o atributo de marca não está preenchido.
* Foi corrigido um problema que causou o Azure AD Connect para ligar ao AD no local para a sincronização de palavra-passe utilizando o NTLM, apesar de Kerberos estiver disponível. Este problema ocorre se no local topologia do AD tem um ou mais controladores de domínio que foram restaurados a partir de uma cópia de segurança.
* Foi corrigido um problema que causou passos de sincronização completa para desnecessariamente ocorrer após a atualização. Em geral, executar passos de sincronização completa é necessária após a atualização se existirem alterações às regras de sincronização de out-of-box. O problema ocorreu devido a um erro na lógica de deteção de alteração incorretamente foi detetada uma alteração quando se deparar com a expressão da regra de sincronização com carateres de nova linha. Carateres de nova linha são inseridas na expressão da regra de sincronização para melhorar a legibilidade.
* Foi corrigido um problema que pode fazer com que o servidor do Azure AD Connect não funcione corretamente após a atualização automática. Este problema afeta os servidores do Azure AD Connect com a versão 1.1.443.0 (ou anterior). Para obter detalhes sobre o problema, consulte o artigo [do Azure AD Connect não está a funcionar corretamente após uma atualização automática](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Foi corrigido um problema que pode fazer com que a atualização automática seja repetida a cada 5 minutos quando forem encontrados erros. Com a correção, a atualização automática é repetida com término exponencial quando são encontrados erros.
* Foi corrigido um problema em que o evento de sincronização de palavra-passe 611 incorretamente é apresentado nos registos de eventos de aplicativo do Windows como **informativa** em vez de **erro**. Evento 611 é gerado sempre que a sincronização de palavra-passe encontra um problema. 
* Foi corrigido um problema no Assistente do Azure AD Connect que permite que a funcionalidade de repetição de escrita do grupo seja ativado sem selecionar um UO necessário para a repetição de escrita do grupo.

#### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos
* Adicionada uma tarefa de resolução de problemas ao Assistente do Azure AD Connect em tarefas adicionais. Os clientes podem aproveitar esta tarefa para resolver problemas relacionados com a sincronização de palavra-passe e recolher diagnóstico geral. No futuro, a tarefa de resolução de problemas será expandida para incluir outros problemas relacionados com a sincronização de diretório.
* O Azure AD Connect suporta de agora chamado de um novo modo de instalação **base de dados de utilização existente**. Este modo de instalação permite aos clientes instalar o Azure AD Connect, que especifica a base de dados ADSync existente. Para obter mais informações sobre esta funcionalidade, consulte o artigo [utilizar uma base de dados existente](how-to-connect-install-existing-database.md).
* Para obter mais segurança, o Azure AD Connect agora por predefinição, que utilizam o TLS1.2 para ligar ao Azure AD para a sincronização de diretórios. Anteriormente, a predefinição foi TLS 1.0.
* Quando o agente do Azure AD Connect palavra-passe de sincronização é iniciado, ele tenta estabelecer ligação ao ponto de final bem conhecido do Azure AD para a sincronização de palavra-passe. Após a ligação com êxito, é redirecionado para um ponto de extremidade específico da região. Anteriormente, o agente de sincronização de palavra-passe caches o ponto de extremidade específico da região de até ser reiniciado. Agora, o agente limpa o cache e tentativas com o ponto de final bem conhecido, se ele encontra o problema de ligação com o ponto de extremidade específico da região. Esta alteração garante que a sincronização de palavra-passe pode ativação pós-falha para um ponto de extremidade específico da região diferente quando o ponto de extremidade específico da região em cache já não está disponível.
* Para sincronizar alterações de uma floresta do AD local, precisa de uma conta do AD DS. Pode (i) criar o AD DS conta por conta própria e fornecem as suas credenciais para o Azure AD Connect ou (ii) fornecer credenciais de um administrador da empresa e permitir que o Azure AD Connect, criar a conta do AD DS para si. Anteriormente, (i) é a opção padrão no Assistente do Azure AD Connect. Agora, (ii) é a opção predefinida.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos
* Foi adicionado suporte para a Cloud do Microsoft Azure Government e do Microsoft Cloud Alemanha.

### <a name="ad-fs-management"></a>Gestão do AD FS
#### <a name="fixed-issues"></a>Problemas corrigidos
* O cmdlet Initialize ADSyncNGCKeysWriteBack no módulo do powershell de preparação do AD foi incorretamente a aplicar ACLs para o contentor de registo do dispositivo e, portanto, seria apenas herdar permissões existentes.  Isto foi atualizado para que a conta de serviço de sincronização tem as permissões corretas.

#### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos
* A tarefa do AAD Connect Certifique-se de AD FS início de sessão foi atualizada para que ele verifica os inícios de sessão no Microsoft Online e obtenção de token não apenas do ADFS.
* Quando configurar um novo farm do AD FS através do AAD Connect, a página onde as credenciais do AD FS foi movida para que ele ocorra agora antes do usuário é solicitado a fornecer servidores AD FS e WAP.  Isso permite que o AAD Connect verificar que a conta especificada tem as permissões corretas.
* Durante a atualização do AAD Connect, nós já não falhará uma atualização se a confiança do ADFS AAD não consegue atualizar.  Se isto acontecer, o usuário será apresentado uma mensagem de aviso apropriada e deve continuar para repor a fidedignidade através da tarefa de adicional do AAD Connect.

### <a name="seamless-single-sign-on"></a>Início de Sessão Único Totalmente Integrado
#### <a name="fixed-issues"></a>Problemas corrigidos
* Foi corrigido um problema que causou o Assistente do Azure AD Connect retornar um erro se tentar ativar [totalmente integrada Single Sign-On](how-to-connect-sso.md). A mensagem de erro é *"Configuração do Microsoft Azure AD Connect do agente de autenticação falhou."* Este problema afeta os clientes existentes que atualizaram manualmente os agentes de autenticação para a versão de pré-visualização [autenticação pass-through](how-to-connect-sso.md) com base nos passos descritos na [artigo](how-to-connect-pta-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Estado: 23 de Julho de 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Foi corrigido o problema

* Foi corrigido um problema que causou a regra de sincronização de out-of-box "" de saída para o AD - ImmutableId do utilizador seja removido:

  * O problema ocorre quando o Azure AD Connect é atualizado ou quando a opção de tarefa *atualizar configuração da sincronização* no Azure AD Connect assistente é utilizado para atualizar a configuração de sincronização do Azure AD Connect.
  
  * Esta regra de sincronização se aplica aos clientes que tem ativado o [ms-DS-ConsistencyGuid como funcionalidade de âncora de origem](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Esta funcionalidade foi introduzida na versão 1.1.524.0 e depois. Quando a regra de sincronização é removida, o Azure AD Connect já não pode preencher no local atributo ms-DS-ConsistencyGuid do AD com o valor de atributo de ObjectGuid. Ele não impede que novos usuários a ser aprovisionado com o Azure AD.
  
  * A correção garante que a regra de sincronização já não será removida durante a atualização, ou durante a alteração de configuração, desde que a funcionalidade está ativada. Para os clientes existentes que foram afetados por este problema, a correção também garante que a regra de sincronização é adicionada novamente após a atualização para esta versão do Azure AD Connect.

* Foi corrigido um problema que faz com que as regras de sincronização de out-of-box ter o valor de precedência que é menor do que 100:

  * Em geral, os valores de precedência 0 - 99 estão reservados para as regras de sincronização personalizado. Durante a atualização, os valores de precedência para regras de sincronização de out-of-box são atualizados para acomodar as alterações de regra de sincronização. Devido a este problema, regras de sincronização de out-of-box podem ser atribuídas ao valor de precedência que é inferior a 100.
  
  * A correção impede que o problema que ocorrem durante a atualização. No entanto, ele não restaura os valores de precedência para os clientes existentes que foram afetados pelo problema. Uma correção separada será fornecida no futuro para ajudar com o restauro.

* Foi corrigido um problema em que o [ecrã de domínio e a filtragem de UO](how-to-connect-install-custom.md#domain-and-ou-filtering) no Azure AD Connect está mostrando o assistente *sincronizar todos os domínios e UOs* opção selecionada, como, mesmo que a filtragem baseada em UO está ativada.

*   Foi corrigido um problema que causou a [ecrã Configurar partições de diretório](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) no Synchronization Service Manager para retornar um erro se o *atualizar* botão é clicado. A mensagem de erro é *"foi encontrado um erro ao atualizar domínios: Não é possível converter o objeto do tipo "ArrayList' no tipo ' Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* O erro ocorre quando o novo domínio do AD foi adicionado a uma floresta existente do AD e está a tentar atualizar o Azure AD Connect com o botão de atualização.

#### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos

* [Funcionalidade de atualização automática](how-to-connect-install-automatic-upgrade.md) foi expandido para suportar os clientes com as seguintes configurações:
  * Ativou a funcionalidade de repetição de escrita do dispositivo.
  * Ativou a funcionalidade de repetição de escrita do grupo.
  * A instalação não é um definições Express ou uma atualização do DirSync.
  * Tem mais de 100 000 objetos no metaverso.
  * Está a ligar mais do que uma floresta. Configuração rápida apenas se conecta a uma floresta.
  * A conta de conector AD já não é a conta de msol _ predefinida.
  * O servidor está definido para estar no modo de teste.
  * Ativou a funcionalidade de repetição de escrita do utilizador.
  
  >[!NOTE]
  >A expansão de âmbito da funcionalidade de atualização automática de afeta os clientes com o Azure AD Connect compilação 1.1.105.0 e depois. Se não pretender que o servidor do Azure AD Connect para ser atualizado automaticamente, tem de executar seguinte cmdlet no seu servidor do Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Para obter mais informações sobre a ativação/desativação a atualização automática, consulte o artigo [do Azure AD Connect: Automatic upgrade](how-to-connect-install-automatic-upgrade.md) (Azure AD Connect: Atualização automática).

## <a name="115580"></a>1.1.558.0
Estado: Não será lançado. Alterações nesta são criados estão incluídas na versão 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Foi corrigido o problema

* Foi corrigido um problema que causou a regra de sincronização de out-of-box "" de saída para o AD - ImmutableId do utilizador seja removido quando a configuração de filtragem baseada em UO é atualizada. Esta regra de sincronização é necessária para o [ms-DS-ConsistencyGuid como funcionalidade de âncora de origem](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Foi corrigido um problema em que o [ecrã de domínio e a filtragem de UO](how-to-connect-install-custom.md#domain-and-ou-filtering) no Azure AD Connect está mostrando o assistente *sincronizar todos os domínios e UOs* opção selecionada, como, mesmo que a filtragem baseada em UO está ativada.

*   Foi corrigido um problema que causou a [ecrã Configurar partições de diretório](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) no Synchronization Service Manager para retornar um erro se o *atualizar* botão é clicado. A mensagem de erro é *"foi encontrado um erro ao atualizar domínios: Não é possível converter o objeto do tipo "ArrayList' no tipo ' Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* O erro ocorre quando o novo domínio do AD foi adicionado a uma floresta existente do AD e está a tentar atualizar o Azure AD Connect com o botão de atualização.

#### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos

* [Funcionalidade de atualização automática](how-to-connect-install-automatic-upgrade.md) foi expandido para suportar os clientes com as seguintes configurações:
  * Ativou a funcionalidade de repetição de escrita do dispositivo.
  * Ativou a funcionalidade de repetição de escrita do grupo.
  * A instalação não é um definições Express ou uma atualização do DirSync.
  * Tem mais de 100 000 objetos no metaverso.
  * Está a ligar mais do que uma floresta. Configuração rápida apenas se conecta a uma floresta.
  * A conta de conector AD já não é a conta de msol _ predefinida.
  * O servidor está definido para estar no modo de teste.
  * Ativou a funcionalidade de repetição de escrita do utilizador.
  
  >[!NOTE]
  >A expansão de âmbito da funcionalidade de atualização automática de afeta os clientes com o Azure AD Connect compilação 1.1.105.0 e depois. Se não pretender que o servidor do Azure AD Connect para ser atualizado automaticamente, tem de executar seguinte cmdlet no seu servidor do Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Para obter mais informações sobre a ativação/desativação a atualização automática, consulte o artigo [do Azure AD Connect: Automatic upgrade](how-to-connect-install-automatic-upgrade.md) (Azure AD Connect: Atualização automática).

## <a name="115570"></a>1.1.557.0
Estado: Julho de 2017

>[!NOTE]
>Esta compilação não está disponível para clientes através da funcionalidade do Azure AD Connect automática atualizar.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Foi corrigido o problema
* Foi corrigido um problema com o cmdlet Initialize ADSyncDomainJoinedComputerSync que causou o domínio verificado configurado no objeto de ponto de ligação de serviço existente seja alterado, mesmo que ainda é um domínio válido. Este problema ocorre quando o inquilino do Azure AD tem mais do que um domínios verificados que podem ser utilizadas para configurar o ponto de ligação de serviço.

#### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos
* Repetição de escrita de palavra-passe está agora disponível para pré-visualização com a cloud do Microsoft Azure Government e do Microsoft Cloud Alemanha. Para obter mais informações sobre o suporte do Azure AD Connect para as instâncias de serviço diferente, consulte o artigo [do Azure AD Connect: Considerações especiais sobre instâncias](reference-connect-instances.md).

* O cmdlet Initialize ADSyncDomainJoinedComputerSync tem agora um novo parâmetro opcional com o nome AzureADDomain. Este parâmetro permite-lhe especificar que verificou o domínio a ser utilizado para configurar o ponto de ligação de serviço.

### <a name="pass-through-authentication"></a>Autenticação pass-through

#### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos
* O nome do agente necessário para a autenticação pass-through foi alterada de *conector de Proxy de aplicações do Microsoft Azure AD* ao *agente do Microsoft Azure AD Connect autenticação*.

* Ativar a autenticação pass-through já não permite a sincronização de Hash de palavra-passe por predefinição.


## <a name="115530"></a>1.1.553.0
Estado: Junho de 2017

> [!IMPORTANT]
> Existem alterações de regra de esquema e sincronização introduzidas nesta são criados. Serviço do Azure AD Connect sincronização irá acionar passos importação completa e a sincronização completa após a atualização. Detalhes das alterações são descritas abaixo. Para diferir temporariamente os passos de importação completa e a sincronização completa após a atualização, consulte o artigo [como diferir a sincronização completa após a atualização](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Problema conhecido
* Existe um problema que afeta os clientes que estão a utilizar [filtragem baseada em UO](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) com a sincronização do Azure AD Connect. Quando navegar para o [página do domínio e a filtragem de UO](how-to-connect-install-custom.md#domain-and-ou-filtering) no Assistente do Azure AD Connect, é esperado o seguinte comportamento:
  * Se a filtragem baseada em UO estiver ativada, o **sincronizar UOs e domínios selecionados** opção está selecionada.
  * Caso contrário, o **sincronizar todos os domínios e UOs** opção está selecionada.

O problema que surge é que o **sincronizar todos os domínios e UOs opção** está sempre selecionado quando executa o assistente.  Isto ocorre mesmo que a filtragem baseada em UO configurado anteriormente. Antes de guardar quaisquer alterações de configuração AAD Connect, certifique-se de que o **sincronizar domínios selecionados e estiver selecionada a opção de UOs** e confirme que todos os UOs que tem de sincronizar estão ativadas novamente. Caso contrário, a filtragem baseada em UO será desativada.

#### <a name="fixed-issues"></a>Problemas corrigidos

* Foi corrigido um problema com a repetição de escrita de palavra-passe que permite aos administradores do Azure AD para repor a palavra-passe de uma local do AD privilegiado a conta de utilizador. O problema ocorre quando o Azure AD Connect é concedido a permissão de reposição de palavra-passe sobre a conta com privilégios. O problema é resolvido nesta versão do Azure AD Connect ao não permitir que o administrador do Azure AD para repor a palavra-passe de uma arbitrário local do AD privilegiado a conta de utilizador, a menos que o administrador é o proprietário dessa conta. Para obter mais informações, consulte [4033453 de consultoria de segurança](https://technet.microsoft.com/library/security/4033453).

* Foi corrigido um problema relacionado com o [ms-DS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) funcionalidade qual o Azure AD Connect é não repetição de escrita no local atributo ms-DS-ConsistencyGuid do AD. O erro ocorre quando há vários locais florestas do AD adicionadas ao Azure AD Connect e o *as identidades dos utilizadores existem em toda a opção de diretórios vários* está selecionada. Quando tal configuração é usada, as regras de sincronização resultante não preencher o atributo de sourceAnchorBinary no Metaverso. O atributo sourceAnchorBinary é utilizado como o atributo de origem para o atributo ms-DS-ConsistencyGuid. Como resultado, a repetição de escrita para o atributo ms-DSConsistencyGuid não ocorre. Para corrigir o problema, seguintes regras de sincronização foram atualizadas para se certificar de que o atributo sourceAnchorBinary no Metaverso sempre é preenchido:
  * Do AD - InetOrgPerson AccountEnabled.xml
  * In from AD - InetOrgPerson Common.xml
  * Do AD - AccountEnabled.xml de utilizador
  * Do AD - Common.xml de utilizador
  * No utilizador associar as SOAInAAD.xml do AD-

* Anteriormente, mesmo que o [ms-DS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) funcionalidade não está ativada, a regra de sincronização "de saída para o AD – ImmutableId do utilizador" ainda é adicionada ao Azure AD Connect. O efeito é benigno e não faz com que a repetição de escrita de atributo ms-DS-ConsistencyGuid ocorra. Para evitar confusão, foi adicionada lógica para se certificar de que a regra de sincronização seja adicionada apenas quando a funcionalidade está ativada.

* Foi corrigido um problema que causou a sincronização de hash de palavra-passe efetuar a ativação com o evento de erro 611. Este problema ocorre depois de uma ou mais domínio controladores foram removidos do AD no local. No final de cada ciclo de sincronização de palavra-passe, o cookie de sincronização emitido por locais AD contém IDs de invocação dos controladores de domínio removidos com valor de USN (número de sequência de atualização) de 0. O Gestor de sincronização de palavra-passe não conseguiu manter a sincronização com USN valor do cookie de 0 e falha com o evento de erro 611. Durante o próximo ciclo de sincronização, o Gerenciador de sincronização de palavra-passe reutiliza o cookie de sincronização persistente último que não contém o valor de USN de 0. Isso faz com que as mesmas alterações de palavra-passe necessário ressincronizar. Com essa correção, o Gerenciador de sincronização de palavra-passe persistir o cookie de sincronização corretamente.

* Anteriormente, mesmo que a atualização automática foi desativada usando o cmdlet Set-ADSyncAutoUpgrade, a atualização automática do processo continuará a verificar para atualizar periodicamente e baseia-se no programa de instalação transferido para honrar a desabilitação. Com essa correção, o processo de atualização automática já não verifica a existência de atualização periodicamente. A correção é aplicada automaticamente quando o instalador de atualização para esta versão do Azure AD Connect é executado uma vez.

#### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos

* Anteriormente, o [ms-DS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) funcionalidade estava disponível para novas implementações apenas. Agora, está disponível para as implementações existentes. Mais especificamente:
  * Para acessar a funcionalidade, inicie o Assistente do Azure AD Connect e escolha o *âncora de origem de atualização* opção.
  * Esta opção só é visível para as implementações existentes que estão a utilizar o objectGuid como atributo sourceAnchor.
  * Ao configurar a opção, o assistente valida o estado do atributo ms-DS-ConsistencyGuid no Active Directory no local. Se o atributo não está configurado em qualquer objeto de utilizador no diretório, o assistente utiliza o ms-DS-ConsistencyGuid como o atributo sourceAnchor. Se o atributo é configurado num ou mais objetos de utilizador no diretório, o assistente conclui o atributo está a ser utilizado por outros aplicativos e não é adequado, como o atributo sourceAnchor e não permite que a alteração de âncora de origem para continuar. Se tiver a certeza de que o atributo não é utilizado por aplicações existentes, terá de contactar o suporte para obter informações sobre como suprimir o erro.

* Específicos **userCertificate** atributo em objetos de dispositivo, o Azure AD Connect agora se parece para os valores de certificados necessários para [ligar os dispositivos associados a um domínio ao Azure AD para a experiência do Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) e filtros descobrirão o restante antes de sincronizar com o Azure AD. Para habilitar esse comportamento, a regra de sincronização de out-of-box "Out ao AAD – dispositivo associar SOAInAD" foi atualizada.

* O Azure AD Connect agora suporta a repetição de escrita de Exchange Online **cloudPublicDelegates** atributo para AD no local **publicDelegates** atributo. Isto permite que o cenário em que uma caixa de correio do Exchange Online pode ser concedida SendOnBehalfTo direitos para os utilizadores com a caixa de correio do Exchange no local. Para suportar esta funcionalidade, uma nova regra de sincronização de out-of-box "" de saída para o AD – repetição de escrita do utilizador Exchange híbrido PublicDelegates foi adicionado. Esta regra de sincronização só é adicionada ao Azure AD Connect quando híbrida do Exchange funcionalidade está ativada.

* O Azure AD Connect agora suporta a sincronizar a **altRecipient** atributo do Azure AD. Para suportar esta alteração, seguintes regras de sincronização de out-of-box foram atualizadas para incluir o fluxo de atributo necessário:
  * Do AD – Exchange do utilizador
  * Expansão para AAD – ExchangeOnline de utilizador
  
* O **cloudSOAExchMailbox** atributo no Metaverso indica se um determinado usuário tem a caixa de correio do Exchange Online ou não. Sua definição foi atualizada para incluir adicionais Exchange Online RecipientDisplayTypes como essas caixas de correio de equipamentos e salas de conferência. Para ativar esta alteração, a definição do atributo cloudSOAExchMailbox, que se encontra na regra de sincronização de out-of-box "do AAD – utilizador híbrida do Exchange", foi atualizada de:

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

* Adicionado o seguinte conjunto de funções de X509Certificate2 compatível para a criação de expressões de regra para lidar com valores de certificado no atributo userCertificate de sincronização:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Selecione|
    |CertKeyAlgorithmParams|CertHashString|Onde|
    |||Com|

* As alterações de esquema seguintes foram introduzidas para permitir que os clientes criar regras de sincronização personalizado para o fluxo sAMAccountName, domainNetBios e domainFQDN para objetos de grupo, bem como distinguishedName para objetos de utilizador:

  * Foram adicionados ao esquema de MV seguintes atributos:
    * Grupo: Nome da Conta
    * Group: domainNetBios
    * Group: domainFQDN
    * Pessoa: distinguishedName

  * Os atributos seguintes foram adicionados ao esquema do conector do Azure AD:
    * Grupo: OnPremisesSamAccountName
    * Grupo: NetBiosName
    * Grupo: DnsDomainName
    * Utilizador: OnPremisesDistinguishedName

* O script de cmdlet ADSyncDomainJoinedComputerSync tem agora um novo parâmetro opcional com o nome AzureEnvironment. O parâmetro é utilizado para especificar qual o inquilino do Azure Active Directory correspondente está alojado na região. Valores válidos incluem:
  * AzureCloud (predefinição)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* Editor de regras de sincronização atualizada para utilizar aderir (em vez de aprovisionar) como o valor predefinido do tipo de ligação durante a criação de regra de sincronização.

### <a name="ad-fs-management"></a>Gestão do AD FS

#### <a name="issues-fixed"></a>Problemas corrigidos

* Seguintes URLs são novos pontos de extremidade WS-Federation introduzidos pelo Azure AD para melhorar a resiliência face a falha de autenticação e será adicionada para o local Configuração de fidedignidade de terceiros do AD FS respondida:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Foi corrigido um problema que causou o AD FS para gerar o valor de afirmação incorreta para IssuerID. O problema ocorre se existem vários domínios verificados do inquilino do Azure AD e o sufixo de domínio do atributo userPrincipalName utilizado para gerar a afirmação de IssuerID é, pelo menos, 3 níveis profunda (por exemplo, johndoe@us.contoso.com). O problema for resolvido, atualizando o regex utilizada pelas regras de afirmação.

#### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos
* Anteriormente, a funcionalidade de gestão de certificados do AD FS fornecida pelo Azure AD Connect só pode ser utilizada com farms de servidores do AD FS geridos através do Azure AD Connect. Agora, pode utilizar a funcionalidade com farms de servidores do AD FS que não são geridos com o Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Data da versão: Maio de 2017

> [!IMPORTANT]
> Existem alterações de regra de esquema e sincronização introduzidas nesta são criados. Serviço do Azure AD Connect sincronização irá acionar passos importação completa e uma sincronização completa após a atualização. Detalhes das alterações são descritas abaixo.
>
>

**Problemas fixos:**

Sincronização do Azure AD Connect

* Foi corrigido um problema que faz com que a atualização automática ocorrer no servidor do Azure AD Connect, mesmo que o cliente tiver desativado a funcionalidade com o cmdlet Set-ADSyncAutoUpgrade. Com essa correção, o processo de atualização automática de mensagens em fila no servidor ainda verifica a existência de atualização periodicamente, mas o instalador que transferiu honra a configuração de atualização automática.
* Durante a atualização do DirSync no local, o Azure AD Connect cria uma conta de serviço do Azure AD para ser utilizada pelo conector do Azure AD para a sincronização com o Azure AD. Depois da conta é criada, o Azure AD Connect efetua a autenticação com o Azure AD com a conta. Às vezes, a autenticação falha devido a problemas transitórios, que por sua vez faz com que o DirSync in-loco efetuar a ativação com o erro *"Erro ao executar tarefa de configurar AAD Sync: AADSTS50034: Para iniciar sessão nesta aplicação, a conta tem de ser adicionada para o diretório de xxx.onmicrosoft.com."* Para melhorar a resiliência da atualização do DirSync, Azure AD Connect agora repete o passo de autenticação.
* Ocorreu um problema com a compilação 443, que faz com que a atualização do DirSync no local com êxito, mas não são criados perfis de execução necessários para a sincronização de diretórios. Lógica de recuperação está incluído nesta criados do Azure AD Connect. Quando o cliente é atualizado para essa compilação, o Azure AD Connect Deteta a falta de perfis de execução e cria-los.
* Foi corrigido um problema que faz com que o processo de sincronização de palavra-passe falhar ao iniciar com 6900 de ID de evento e o erro *"já tenha sido adicionado um item com a mesma chave"*. Este problema ocorre se atualizar UO configuração de filtragem para incluir a partição de configuração do AD. Para corrigir este problema, o processo de sincronização de palavra-passe agora sincroniza as alterações de palavra-passe de apenas o partições de um domínio AD. Partições de domínio não como partição de configuração são ignoradas.
* Durante a instalação do Express, o Azure AD Connect cria uma local conta do AD DS para ser utilizada pelo conector do AD para comunicar com o AD no local. Anteriormente, a conta é criada com o sinalizador PASSWD_NOTREQD definido no atributo de controle de conta de utilizador e uma senha aleatória é definida na conta. Agora, o Azure AD Connect remove explicitamente o sinalizador PASSWD_NOTREQD depois da palavra-passe é definida na conta.
* Foi corrigido um problema que faz com que a atualização do DirSync efetuar a ativação com o erro *"Ocorreu um impasse no sql server que tentar adquirir um bloqueio de aplicação"* quando o atributo mailNickname está localizado no local esquema do AD, mas não é limitado para a classe de objeto de utilizador do AD.
* Foi corrigido um problema que faz com que a funcionalidade de repetição de escrita do dispositivo para ser automaticamente desativado quando um administrador está a atualizar a configuração de sincronização do Azure AD Connect utilizando o Assistente do Azure AD Connect. Este problema é causado pelo Assistente de efetuar uma verificação de pré-requisitos para a configuração de repetição de escrita do dispositivo existente no AD no local e a verificação de falha. A correção é ignorar a verificação, se a repetição de escrita do dispositivo já estiver ativada anteriormente.
* Para configurar a filtragem de UO, pode utilizar o Assistente do Azure AD Connect ou o Synchronization Service Manager. Anteriormente, se utilizar o Assistente do Azure AD Connect para configurar a filtragem de UO, as novas UOs criadas posteriormente estão incluídas para sincronização de diretórios. Se não pretender que as novas UOs para ser incluída, tem de configurar a filtragem de UO com o Synchronization Service Manager. Agora, pode conseguir o mesmo comportamento usando o Assistente do Azure AD Connect.
* Foi corrigido um problema que faz com que procedimentos armazenados necessários pelo Azure AD Connect para criadas sob o esquema do Centro de administração a instalação, em vez de em esquema dbo.
* Foi corrigido um problema que faz com que o atributo de TrackingId devolvido pelo Azure AD para ser omitido no AAD Connect servidor Logs de eventos. O problema ocorre se o Azure AD Connect recebe uma mensagem de redirecionamento do Azure AD e do Azure AD Connect não consegue ligar ao ponto final fornecido. O TrackingId é utilizado por engenheiros de suporte para correlacionar com os registos do lado do serviço durante a resolução de problemas.
* Quando o Azure AD Connect recebe o erro de LargeObject do Azure AD, o Azure AD Connect gera um evento com EventID 6941 e a mensagem *"o objeto aprovisionado é demasiado grande. Compacte o número de valores de atributo neste objeto."* Ao mesmo tempo, o Azure AD Connect também gera um evento enganoso com EventID 6900 e a mensagem *"Microsoft.Online.Coexistence.ProvisionRetryException: Não é possível comunicar com o serviço do Windows Azure Active Directory."* Para minimizar a confusão, o Azure AD Connect já não gera o último evento quando é recebido o erro de LargeObject.
* Foi corrigido um problema que faz com que o Gestor do serviço de sincronização para deixar de responder ao tentar atualizar a configuração para o conector LDAP genérico.

**Recursos/melhorias novas:**

Sincronização do Azure AD Connect
* Alterações de regra de sincronização – as seguintes alterações de regra de sincronização foram implementadas:
  * Regra de sincronização de predefinição atualizados definida como para não exportar atributos **userCertificate** e **userSMIMECertificate** se os atributos têm mais de 15 valores.
  * Atributos de AD **employeeID** e **msExchBypassModerationLink** agora estão incluídos no conjunto de regras de sincronização padrão.
  * Atributo de AD **fotos** foi removido do conjunto de regras de sincronização do padrão.
  * Adicionado **preferredDataLocation** para o esquema do Metaverso e o esquema do conector AAD. Os clientes que desejam atualizar qualquer um dos atributos no Azure AD podem implementar regras de sincronização personalizados para o fazer. 
  * Adicionado **userType** para o esquema do Metaverso e o esquema do conector AAD. Os clientes que desejam atualizar qualquer um dos atributos no Azure AD podem implementar regras de sincronização personalizados para o fazer.

* O Azure AD Connect agora automaticamente permite o uso do atributo ConsistencyGuid como o atributo de âncora de origem para locais objetos do AD. Além disso, o Azure AD Connect preenche o atributo de ConsistencyGuid com o valor do atributo de objectGuid se estiver vazia. Esta funcionalidade é aplicável a nova implementação apenas. Para obter mais informações sobre esta funcionalidade, consulte a secção do artigo [do Azure AD Connect: Conceitos - usando o ms-DS-ConsistencyGuid como sourceAnchor de design](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* Nova resolução de problemas de cmdlet Invoke-ADSyncDiagnostics foi adicionado ajudar a sincronização de Hash de palavra-passe de diagnosticar problemas relacionados com. Para obter informações sobre como utilizar o cmdlet, consulte o artigo [resolver problemas de sincronização de hash de palavra-passe com o Azure AD Connect sync](tshoot-connect-password-hash-synchronization.md).
* O Azure AD Connect, agora suporta objetos de pasta pública de com capacidade de correio sincronização do AD no local para o Azure AD. Pode ativar a funcionalidade com o Assistente do Azure AD Connect em funcionalidades opcionais. Para obter mais informações sobre esta funcionalidade, consulte o artigo [Office 365 Directory com base em bloqueio periférico suporte no local ativada pastas públicas de correio](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Azure AD Connect requer um anúncio para sincronizar a partir da conta do DS AD no local. Anteriormente, se tiver instalado o Azure AD Connect com o modo do Express, poderia fornecer que as credenciais de uma conta de administrador da empresa e do Azure AD Connect cria a conta do AD DS necessária. No entanto, para uma instalação personalizada e adicionar florestas para uma implementação existente, era necessário para fornecer a conta do AD DS em vez disso. Agora, tem também a opção de fornecer as credenciais de uma conta de administrador da empresa durante uma instalação personalizada e permitir que o Azure AD Connect, criar a conta do AD DS necessária.
* Azure AD Connect suporta agora AOA de SQL. Tem de ativar AOA de SQL antes de instalar o Azure AD Connect. Durante a instalação, o Azure AD Connect Deteta se a instância SQL fornecida está ativada para SQL AOA ou não. Se estiver ativada AOA de SQL, Azure AD Connect ainda mais descobre se o SQL AOA está configurado para utilizar a replicação síncrona ou replicação assíncrona. Quando configurar o serviço de escuta do grupo de disponibilidade, recomenda-se que defina a propriedade RegisterAllProvidersIP para 0. Esta recomendação é porque o Azure AD Connect utiliza atualmente o SQL Native Client para ligar ao SQL e SQL Native Client não suporta a utilização da propriedade MultiSubNetFailover.
* Se estiver a utilizar o LocalDB como a base de dados para o seu servidor do Azure AD Connect e atingiu o limite de tamanho de 10 GB, o serviço de sincronização já não for iniciado. Anteriormente, tem de efetuar a operação de ShrinkDatabase no LocalDB para recuperar espaço suficiente DB para o serviço de sincronização iniciar. Depois disso, pode utilizar o Synchronization Service Manager para eliminar o histórico de execução para recuperar o espaço de DB mais. Agora, pode utilizar o cmdlet de ADSyncPurgeRunHistory de início para dados de histórico de executar a remoção da LocalDB para recuperar espaço da base. Além disso, este cmdlet suporta o modo offline (especificando o parâmetro - offline) que pode ser utilizado quando o serviço de sincronização não está em execução. Nota: O modo offline só pode ser utilizado se o serviço de sincronização não está em execução e o banco de dados usado é LocalDB.
* Para reduzir a quantidade de espaço de armazenamento necessário, do Azure AD Connect compacta agora detalhes do erro de sincronização antes de armazená-las em bases de dados LocalDB/SQL. Ao atualizar a partir de uma versão mais antiga do Azure AD Connect para esta versão, o Azure AD Connect efetua uma única compactação nos detalhes do erro de sincronização existente.
* Anteriormente, depois de atualizar a configuração de filtragem de UO, deverá executar manualmente a importação completa para se certificar de objetos existentes corretamente incluído/excluído da sincronização de diretórios. Agora, o Azure AD Connect aciona automaticamente a importação completa durante a sincronização seguinte ciclo. Além disso, completa importação só é ser aplicada aos conectores AD afetados pela atualização. Nota: esta melhoria é aplicável às atualizações efetuadas utilizando o Assistente do Azure AD Connect apenas de filtragem de UO. Não é aplicável a atualização realizada com o Gestor de serviço de sincronização de filtragem de UO.
* Anteriormente, suporta a filtragem baseada em grupo de utilizadores, grupos e objetos de contato apenas. Agora, a filtragem baseada em grupo também oferece suporte a objetos de computador.
* Anteriormente, pode eliminar os dados de espaço conector sem desativar o agendador de sincronização do Azure AD Connect. Agora, o Synchronization Service Manager bloqueia a eliminação de dados de espaço conector se detetar que o scheduler está ativado. Além disso, um aviso é retornado para informar os clientes sobre potencial perda de dados se os dados de espaço conector são eliminados.
* Anteriormente, terá de desativar a transcrição do PowerShell para o Assistente do Azure AD Connect para serem executados corretamente. Este problema foi parcialmente resolvido. Pode ativar a transcrição do PowerShell se estiver a utilizar o Assistente do Azure AD Connect para gerir a configuração da sincronização. Tem de desativar a transcrição de PowerShell se estiver a utilizar o Assistente do Azure AD Connect para gerir a configuração do AD FS.



## <a name="114860"></a>1.1.486.0
Data da versão: Abril de 2017

**Problemas fixos:**
* Foi corrigido o problema em que o Azure AD Connect não serão instalados corretamente na versão localizada do Windows Server.

## <a name="114840"></a>1.1.484.0
Data da versão: Abril de 2017

**Problemas conhecidos:**

* Esta versão do Azure AD Connect não serão instalados corretamente se as seguintes condições sejam verificarem:
   1. Está a efetuar qualquer instalação de atualização ou atualizada no local de DirSync do Azure AD Connect.
   2. Está a utilizar uma versão localizada do Windows Server em que o nome do grupo de administrador interno no servidor não é "Administradores".
   3. Está a utilizar a predefinição do SQL Server 2012 Express LocalDB instalado com o Azure AD Connect, em vez de fornecer o seu SQL completo.

**Problemas fixos:**

Sincronização do Azure AD Connect
* Foi corrigido um problema em que o agendador de sincronização ignora o passo de sincronização completo se um ou mais conectores estão em falta para esse passo de sincronização do perfil de execução. Por exemplo, adicionasse manualmente um conector com o Gestor de serviço de sincronização sem criar um perfil de execução para a importação Delta. Esta correção garante que o agendador de sincronização continua a ser executada a importação Delta para outros conectores.
* Foi corrigido um problema em que o serviço de sincronização imediatamente interrompe o processamento um perfil de execução quando é encontra um problema com um dos passos de execução. Esta correção garante que o serviço de sincronização ignora que executam o passo e continua a processar o restante. Por exemplo, tem uma importação Delta perfil para o conector do AD de execução com diversas etapas de execução (um para cada local domínio AD). O serviço de sincronização será executada a importação Delta com outros domínios de AD, mesmo que uma delas tenha problemas de conectividade de rede.
* Foi corrigido um problema que faz com que a atualização do conector do Azure AD de ser ignorada durante a atualização automática.
* Foi corrigido um problema que faz com que o Azure AD Connect para incorretamente determinar se o servidor é um controlador de domínio durante a configuração, que em vez faz com que o atualização do DirSync efetuar a ativação.
* Foi corrigido um problema que faz com que a atualização no local de DirSync por não criar qualquer perfil de execução para o conector do Azure AD.
* Foi corrigido um problema em que a interface de utilizador do Synchronization Service Manager deixar de responder quando tentar configurar o conector LDAP genérico.

Gestão do AD FS
* Foi corrigido um problema em que o Assistente do Azure AD Connect falha se o nó principal do AD FS foi movido para outro servidor.

SSO de ambiente de trabalho
* Foi corrigido um problema no Assistente do Azure AD Connect em que o ecrã de início de sessão não permitem-lhe ativar a funcionalidade de SSO de ambiente de trabalho, se tiver escolhido a sincronização de palavra-passe como a opção de início de sessão durante a instalação de novo.

**Recursos/melhorias novas:**

Sincronização do Azure AD Connect
* Azure AD Connect Sync agora suporta a utilização de conta de serviço Virtual, conta de serviço gerida e conta de serviço gerida de grupo como sua conta de serviço. Isto aplica-se a nova instalação do Azure AD Connect apenas. Quando instalar o Azure AD Connect:
    * Por predefinição, o Assistente do Azure AD Connect irá criar uma conta de serviço Virtual e utiliza-o à sua conta de serviço.
    * Se estiver a instalar num controlador de domínio, o Azure AD Connect retrocede para comportamento anterior, onde irá criar uma conta de utilizador de domínio e utiliza-a como sua conta de serviço.
    * Pode substituir o comportamento padrão, fornecendo um dos seguintes:
      * Conta de serviço gerida de um grupo
      * Uma conta de serviço gerida
      * Uma conta de utilizador de domínio
      * Uma conta de utilizador local
* Anteriormente, se atualizar para uma nova compilação do Azure AD Connect que contém a atualização de conectores ou alterações de regra de sincronização, o Azure AD Connect irão acionar um ciclo de sincronização completa. Agora, o Azure AD Connect seletivamente aciona passo de importação completa apenas para os conectores com o update e o passo de sincronização completa apenas para os conectores com alterações de regra de sincronização.
* Anteriormente, o limiar de eliminação exportar só se aplica a exportações que são acionadas por meio do agendador de sincronização. Agora, a funcionalidade é expandida para incluir as exportações que acionou manualmente pelo cliente a utilizar o Synchronization Service Manager.
* No seu inquilino do Azure AD, existe uma configuração de serviço que indica se o recurso de sincronização de palavra-passe está ativado para o seu inquilino ou não. Anteriormente, é fácil para a configuração do serviço estar configurado incorretamente pelo Azure AD Connect quando tiver um Active Directory e um servidor de teste. Agora, o Azure AD Connect irá tentar manter a configuração de serviço consistente com o Active Directory apenas o servidor do Azure AD Connect.
* O Azure AD Connect, assistente agora detecta e retorna um aviso se locais AD não tem a Reciclagem do AD ativado.
* Anteriormente, exportar do Azure AD expire e falha, se o tamanho combinado de objetos no lote excede determinado limiar. Agora, o serviço de sincronização será repita a tentativa reenviar os objetos em lotes separados, mais pequenos, se o problema for encontrado.
* A aplicação de gestão de chaves do serviço de sincronização foi removida do Menu Iniciar do Windows. Gestão da chave de encriptação irá continuar a ser suportado por meio da interface de linha de comandos utilizando miiskmu.exe. Para obter informações sobre a gestão de chave de encriptação, consulte o artigo [abandonar a chave de encriptação do Azure AD Connect Sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key).
* Anteriormente, se alterar a senha de conta de serviço de sincronização do Azure AD Connect, o serviço de sincronização não será capaz de iniciar corretamente até ter abandonado a chave de encriptação e reinicializados a senha de conta de serviço de sincronização do Azure AD Connect. Agora, este processo já não é necessário.

SSO de ambiente de trabalho

* Assistente do Azure AD Connect já não necessita de porta 9090 sejam abertas na rede, ao configurar a autenticação pass-through e SSO de ambiente de trabalho. Só é necessária a porta 443. 

## <a name="114430"></a>1.1.443.0
Data da versão: Março de 2017

**Problemas fixos:**

Sincronização do Azure AD Connect
* Foi corrigido um problema que faz com que o Assistente do Azure AD Connect falhar caso o nome a apresentar do conector do Azure AD não contém o domínio onmicrosoft.com inicial atribuído ao inquilino do Azure AD.
* Foi corrigido um problema que faz com que o Assistente do Azure AD Connect falhar ao efetuar a ligação à base de dados SQL quando a palavra-passe da conta de serviço de sincronização contém carateres especiais, como o apóstrofe, dois pontos e espaço.
* Foi corrigido um problema que faz com que o erro o "a imagem tem uma âncora de que é diferente do que a imagem" ocorra num servidor do Azure AD Connect no modo de teste depois excluiu temporariamente uma local do AD de objeto de sincronização e, em seguida, incluído-a novamente para a sincronização.
* Foi corrigido um problema que faz o erro "o objeto localizado por DN é um fantasma" ocorra num servidor do Azure AD Connect no modo de teste depois excluiu temporariamente uma local do AD de objeto de sincronização e, em seguida, incluído-a novamente para a sincronização.

Gestão do AD FS
* Foi corrigido um problema em que o Assistente do Azure AD Connect não atualizar a configuração do AD FS e defina as declarações certas sobre a confiança de entidade confiadora após a configuração do ID de início de sessão alternativo.
* Foi corrigido um problema em que o Assistente do Azure AD Connect é conseguir processar corretamente os servidores do AD FS cujas contas de serviço estão configuradas ao utilizar o formato de userPrincipalName em vez do formato de sAMAccountName.

Autenticação pass-through
* Foi corrigido um problema que faz com que o Assistente do Azure AD Connect efetuar a ativação se passar por meio de autenticação está selecionada, mas falhar o registo do seu conector.
* Foi corrigido um problema que faz com que o Assistente do Azure AD Connect para ignorar a validação verifica no método de início de sessão selecionado quando a funcionalidade de SSO de ambiente de trabalho está ativada.

Reposição de Palavra-passe
* Foi corrigido um problema que pode fazer com que o servidor do Azure AAD Connect para não tentar conectar-se novamente se a ligação foi eliminada por uma firewall ou proxy.

**Recursos/melhorias novas:**

Sincronização do Azure AD Connect
* Agora, o cmdlet Get-ADSyncScheduler retorna uma nova propriedade booleana com o nome SyncCycleInProgress. Se o valor devolvido for true, significa que existe um ciclo de sincronização agendada está em curso.
* Pasta de destino para armazenar registos de configuração e de instalação do Azure AD Connect foi foi movida de %localappdata%\AADConnect para %programdata%\AADConnect para melhorar a acessibilidade para os ficheiros de registo.

Gestão do AD FS
* Foi adicionado suporte para atualizar o certificado de SSL do Farm do AD FS.
* Foi adicionado suporte para gerir o AD FS 2016.
* Agora pode especificar gMSA existente (grupo de conta de serviço gerida) durante a instalação do AD FS.
* Agora, pode configurar o SHA-256 como o algoritmo de hash de assinatura para confiança da entidade confiadora do Azure AD.

Reposição de Palavra-passe
* Introduziu melhorias para permitir que o produto para a função em ambientes com regras de firewall mais rigorosas.
* Fiabilidade de ligação melhorada para o Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Data da versão: Dezembro de 2016

**Foi corrigido o problema:**

* Foi corrigido o problema em que a regra de afirmação de issuerid para serviços de Federação do Active Directory (AD FS) está em falta dessa compilação.

>[!NOTE]
>Esta compilação não está disponível para clientes através da funcionalidade do Azure AD Connect automática atualizar.

## <a name="113710"></a>1.1.371.0
Data da versão: Dezembro de 2016

**Problema conhecido:**

* A regra de afirmação de issuerid para o AD FS está em falta dessa compilação. A regra de afirmação de issuerid é necessária se estiver a federar vários domínios com o Azure Active Directory (Azure AD). Se estiver a utilizar o Azure AD Connect para gerir no local implementação do AD FS, a atualização para esta compilação remove a regra de afirmação de issuerid existentes da sua configuração do AD FS. Pode contornar o problema ao adicionar a regra de afirmação de issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar o issuerid de regras de afirmações, consulte este artigo no [suporte para vários domínios para federação com o Azure AD](how-to-connect-install-multiple-domains.md).

**Foi corrigido o problema:**

* Se a porta 9090 não está aberta para a ligação de saída, a instalação do Azure AD Connect ou a atualização falha.

>[!NOTE]
>Esta compilação não está disponível para clientes através da funcionalidade do Azure AD Connect automática atualizar.

## <a name="113700"></a>1.1.370.0
Data da versão: Dezembro de 2016

**Problemas conhecidos:**

* A regra de afirmação de issuerid para o AD FS está em falta dessa compilação. A regra de afirmação de issuerid é necessária se estiver a federar vários domínios com o Azure AD. Se estiver a utilizar o Azure AD Connect para gerir no local implementação do AD FS, a atualização para esta compilação remove a regra de afirmação de issuerid existentes da sua configuração do AD FS. Pode contornar o problema ao adicionar a regra de afirmação de issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar issuerid de regras de afirmações, consulte este artigo no [suporte para vários domínios para federação com o Azure AD](how-to-connect-install-multiple-domains.md).
* Porta 9090 tem de ser abrir saída para concluir a instalação.

**Novas funcionalidades:**

* Autenticação pass-through (pré-visualização).

>[!NOTE]
>Esta compilação não está disponível para clientes através da funcionalidade do Azure AD Connect automática atualizar.

## <a name="113430"></a>1.1.343.0
Data da versão: Novembro de 2016

**Problema conhecido:**

* A regra de afirmação de issuerid para o AD FS está em falta dessa compilação. A regra de afirmação de issuerid é necessária se estiver a federar vários domínios com o Azure AD. Se estiver a utilizar o Azure AD Connect para gerir no local implementação do AD FS, a atualização para esta compilação remove a regra de afirmação de issuerid existentes da sua configuração do AD FS. Pode contornar o problema ao adicionar a regra de afirmação de issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar issuerid de regras de afirmações, consulte este artigo no [suporte para vários domínios para federação com o Azure AD](how-to-connect-install-multiple-domains.md).

**Problemas fixos:**

* Às vezes, instalar o Azure AD Connect falha porque não é possível criar uma conta de serviço local que palavra-passe cumpre o nível de complexidade especificada pela política de palavra-passe da organização.
* Foi corrigido um problema em que as regras de associação não são reavaliadas quando um objeto no espaço conector simultaneamente fica fora do escopo para uma regra de associação e tornam-se no âmbito para outro. Isto pode acontecer se tiver duas ou mais regras de associação cujas condições de associação são mutuamente exclusivas.
* Foi corrigido um problema em que regras de sincronização de entrada (a partir do Azure AD), que não contêm regras de associação, não são processadas se eles tiverem valores mais baixos de precedência que aquelas que contém as regras de associação.

**Melhorias:**

* Foi adicionado suporte para instalar o Azure AD Connect, no Windows Server 2016 Standard ou superior.
* Foi adicionado suporte para a utilização de SQL Server 2016 como a base de dados remoto do Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Data da versão: Agosto de 2016

**Problemas fixos:**

* As alterações para sincronizar o intervalo não ocorrer até depois que o próximo ciclo de sincronização estiver concluído.
* Assistente do Azure AD Connect não aceita uma conta do Azure AD, cujo nome de utilizador começa com um caráter de sublinhado (\_).
* Assistente do Azure AD Connect não consegue autenticar a conta do Azure AD, se a senha da conta contém demasiados carateres especiais. Mensagem de erro "não é possível validar as credenciais. Tiver ocorrido um erro inesperado." é devolvido.
* Desinstalar o servidor de teste, desativa a sincronização de palavra-passe no inquilino do Azure AD e faz com que a sincronização de palavra-passe efetuar a ativação com o servidor Active Directory.
* Sincronização de palavra-passe falhar em casos invulgares quando não existe nenhum hash de palavra-passe armazenado no utilizador.
* Quando o servidor do Azure AD Connect está ativado para o modo de teste, a repetição de escrita de palavra-passe não está temporariamente desativada.
* Assistente do Azure AD Connect não mostra a sincronização de palavra-passe real e a configuração de repetição de escrita de palavra-passe quando o servidor está no modo de teste. Elas sempre mostram como desativados.
* Alterações de configuração para a sincronização de palavra-passe e a repetição de escrita de palavra-passe não são mantidas pelo Assistente do Azure AD Connect, quando o servidor está no modo de teste.

**Melhorias:**

* Atualizado o cmdlet Start-ADSyncSyncCycle para indicar se é possível iniciar com êxito um novo ciclo de sincronização ou não.
* Adicionado o cmdlet Stop-ADSyncSyncCycle para terminar o ciclo de sincronização e operação, que estão atualmente em curso.
* Atualizado o cmdlet Stop-ADSyncScheduler para terminar o ciclo de sincronização e operação, que estão atualmente em curso.
* Quando configurar [extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) no Assistente do Azure AD Connect, o atributo de AD do Azure do tipo "Cadeia de caracteres Teletex" agora pode ser selecionado.

## <a name="111890"></a>1.1.189.0
Data da versão: Junho de 2016

**Problemas fixos e melhorias:**

* O Azure AD Connect pode agora ser instalado num servidor compatível com FIPS.
  * Para a sincronização de palavra-passe, consulte [sincronização de hash de palavra-passe e FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Foi corrigido um problema em que não foi possível resolver um nome de NetBIOS para o FQDN no conector do Active Directory.

## <a name="111800"></a>1.1.180.0
Data da versão: Maio de 2016

**Novas funcionalidades:**

* Avisa e ajuda-o a verificação de domínios se não tiver feito isso antes de executar o Azure AD Connect.
* Foi adicionado suporte para [Microsoft Cloud Alemanha](reference-connect-instances.md#microsoft-cloud-germany).
* Foi adicionado suporte para a versão mais recente [cloud do Microsoft Azure Government](reference-connect-instances.md#microsoft-azure-government-cloud) infraestrutura com novos requisitos de URL.

**Problemas fixos e melhorias:**

* Adicionado ao Editor regra de sincronização para que seja mais fácil encontrar as regras de sincronização de filtragem.
* Melhor desempenho quando a eliminação de um espaço conector.
* Foi corrigido um problema quando o mesmo objeto foi eliminado e adicionado no mesmo executar (chamado delete/adicionar).
* Uma sincronização desativada a regra já não reativa objetos incluídos e atualizar atributos no esquema de atualização ou diretório.

## <a name="111300"></a>1.1.130.0
Data da versão: Abril de 2016

**Novas funcionalidades:**

* Foi adicionado suporte para atributos com múltiplos valores para [extensões de diretório](how-to-connect-sync-feature-directory-extensions.md).
* Foi adicionado suporte para obter mais variações de configuração para [a atualização automática](how-to-connect-install-automatic-upgrade.md) para ser considerado elegível para atualização.
* Foram adicionados alguns cmdlets para [agendador personalizado](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Data da versão: Março de 2016

**Problemas fixos:**

* Tornou-se de que a instalação do Express não pode ser utilizada no Windows Server 2008 (pré-R2), uma vez que a sincronização de palavra-passe não é suportado neste sistema operativo.
* Atualização do DirSync com uma configuração de filtro personalizado não funcionavam conforme esperado.
* Ao atualizar para uma versão mais recente e não foram efetuadas alterações à configuração, não devem ser agendada uma importação/sincronização completa.

## <a name="111100"></a>1.1.110.0
Data da versão: Fevereiro de 2016

**Problemas fixos:**

* Atualização a partir de versões anteriores não funciona se a instalação não está na pasta C:\Program Files predefinida.
* Se instalar e desmarque **iniciar o processo de sincronização** no final do Assistente de instalação, o Assistente de instalação a executar uma segunda vez não permitirá que o scheduler.
* O agendador não funciona conforme esperado nos servidores em que não é utilizado o formato de data/hora de US-en. Também irá bloquear `Get-ADSyncScheduler` para retornar vezes corretos.
* Se instalou uma versão anterior do Azure AD Connect com o AD FS como a opção de início de sessão e a atualização, não é possível executar o Assistente de instalação novamente.

## <a name="111050"></a>1.1.105.0
Data da versão: Fevereiro de 2016

**Novas funcionalidades:**

* [A atualização automática](how-to-connect-install-automatic-upgrade.md) funcionalidade para os clientes de definições Express.
* Suporte para o administrador global com o Azure multi-factor Authentication e Privileged Identity Management no Assistente de instalação.
  * É necessário permitir que o proxy permitir o tráfego para https://secure.aadcdn.microsoftonline-p.com se utilizar o multi-factor Authentication.
  * Precisa adicionar https://secure.aadcdn.microsoftonline-p.com à sua lista de sites fidedignos do multi-factor Authentication funcionar corretamente.
* Permita a alteração do método de início de sessão do utilizador após a instalação inicial.
* Permitir [domínio e UO filtragem](how-to-connect-install-custom.md#domain-and-ou-filtering) no Assistente de instalação. Isto também permite ligar a florestas em que nem todos os domínios estão disponíveis.
* [Scheduler](how-to-connect-sync-feature-scheduler.md) incorporada no motor de sincronização.

**Recursos promovidos de pré-visualização para disponibilidade geral:**

* [Repetição de escrita do dispositivo](how-to-connect-device-writeback.md).
* [Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md).

**Novas funcionalidades de pré-visualização:**

* A nova predefinição sincronizar ciclo intervalo é de 30 minutos. Utilizado para ser três horas para todas as versões anteriores. Adiciona suporte para alterar o [agendador](how-to-connect-sync-feature-scheduler.md) comportamento.

**Problemas fixos:**

* A página de domínios DNS de verificar sempre não reconheceu os domínios.
* Pedidos de credenciais de administrador de domínio quando configurar o AD FS.
* No local contas AD não são reconhecidas pelo Assistente de instalação, se estiver localizado num domínio com uma árvore DNS diferente do que o domínio de raiz.

## <a name="1091310"></a>1.0.9131.0
Data da versão: Dezembro de 2015

**Problemas fixos:**

* Sincronização de palavra-passe não pode funcionar quando alterar palavras-passe no Active Directory Domain Services (AD DS), mas funciona quando definir uma palavra-passe.
* Quando tiver um servidor proxy, a autenticação para o Azure AD pode falhar durante a instalação, ou se uma atualização é cancelada na página de configuração.
* A atualizar a partir de uma versão anterior do Azure AD Connect com uma instância completa do SQL Server falha se não for um administrador de sistema (SA) do SQL Server.
* A atualizar a partir de uma versão anterior do Azure AD Connect com um servidor SQL remoto mostra o erro "Não é possível acessar o SQL do ADSync da base de dados".

## <a name="1091250"></a>1.0.9125.0
Data da versão: Novembro de 2015

**Novas funcionalidades:**

* Pode reconfigurar o AD FS de modo de confiança do Azure AD.
* Pode atualizar o esquema do Active Directory e voltar a gerar regras de sincronização.
* Pode desativar uma regra de sincronização.
* Pode definir "AuthoritativeNull" como um literal de novo numa regra de sincronização.

**Novas funcionalidades de pré-visualização:**

* [O Azure AD Connect Health para sincronização](how-to-connect-health-sync.md).
* Suporte para [Azure AD Domain Services](../user-help/active-directory-passwords-update-your-own-password.md) sincronização de palavra-passe.

**Novo cenário suportado:**

* Oferece suporte a várias organizações do Exchange no local. Para obter mais informações, consulte [implementações híbridas com várias florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx).

**Problemas fixos:**

* Problemas de sincronização de palavra-passe:
  * Um objeto movido de fora do escopo para dentro do âmbito não terão a respetiva palavra-passe sincronizada. Isto inclui a UO e filtragem de atributos.
  * Selecionar uma nova UO para incluir em sincronia não requer uma sincronização de palavras-passe completa.
  * Quando um utilizador desativado está ativado a palavra-passe não sincroniza.
  * A fila de repetição de palavras-passe é infinita e foi removido o limite anterior de 5.000 objetos serão descontinuados.
* Não é possível ligar ao Active Directory com o nível funcional de floresta do Windows Server 2016.
* Não é possível alterar o grupo que é utilizado para filtragem de grupos após a instalação inicial.
* Já não cria um novo perfil de utilizador no servidor do Azure AD Connect para cada usuário fazer uma alteração de palavra-passe com repetição de escrita de palavra-passe ativada.
* Não é possível utilizar o número inteiro longo valores de âmbitos de regras em sincronia.
* A caixa de verificação "repetição de escrita do dispositivo" continua a ser desativada se existirem controladores de domínio inacessível.

## <a name="1086670"></a>1.0.8667.0
Data da versão: Agosto de 2015

**Novas funcionalidades:**

* O Assistente de instalação do Azure AD Connect é agora localizado para todos os idiomas do Windows Server.
* Foi adicionado suporte para conta desbloquear quando utilizar a gestão de palavra-passe do Azure AD.

**Problemas fixos:**

* Assistente de instalação do Azure AD Connect falha se outro utilizador continuar a instalação, em vez da pessoa que iniciado pela primeira vez a instalação.
* Se uma desinstalação anterior do Azure AD Connect não conseguir desinstalar sem dificuldade do Azure AD Connect, não é possível reinstalar.
* Não é possível instalar o Azure AD Connect com a instalação do Express se o utilizador não estiver no domínio raiz da floresta, ou se for utilizada uma versão não inglesa do Active Directory.
* Se não conseguir resolver o FQDN de conta de utilizador do Active Directory, é apresentada uma mensagem de erro "Falha ao consolidar o esquema" enganoso.
* Se a conta utilizada no conector do Active Directory for alterada fora do assistente, o assistente falha nas execuções posteriores.
* O Azure AD Connect, às vezes, falha na instalação num controlador de domínio.
* Não é possível ativar e desativar "Modo de teste", se os atributos de extensão foram adicionados.
* Repetição de escrita de palavra-passe falhará em algumas configurações devido a uma palavra-passe incorreta no conector do Active Directory.
* Não é possível atualizar o DirSync, se um nome único (DN) é utilizado na filtragem de atributos.
* Utilização excessiva da CPU ao utilizar a reposição de palavra-passe.

**Funcionalidades de pré-visualização foi removido:**

* A funcionalidade de pré-visualização [repetição de escrita do utilizador](how-to-connect-preview.md#user-writeback) foi temporariamente removidos com base nos comentários de nossos clientes de pré-visualização. Vai ser adicionado novamente mais tarde depois podemos corrigir esses comentários fornecido.

## <a name="1086410"></a>1.0.8641.0
Data da versão: Junho de 2015

**Versão inicial do Azure AD Connect.**

Nome alterado do Azure AD Sync para o Azure AD Connect.

**Novas funcionalidades:**

* [Definições rápidas](how-to-connect-install-express.md) instalação
* Pode [configurar o AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* Pode [atualizar do DirSync](how-to-dirsync-upgrade-get-started.md)
* [Impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Introduzida [modo de teste](how-to-connect-sync-staging-server.md)

**Novas funcionalidades de pré-visualização:**

* [Repetição de escrita do utilizador](how-to-connect-preview.md#user-writeback)
* [Repetição de escrita do grupo](how-to-connect-preview.md#group-writeback)
* [Repetição de escrita do dispositivo](how-to-connect-device-writeback.md)
* [Extensões de diretórios](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Data da versão: Maio de 2015

**Novo requisito:**

* O Azure AD Sync requer agora que a versão .NET Framework 4.5.1 ser instalado.

**Problemas fixos:**

* Repetição de escrita de palavra-passe do Azure AD está a falhar com um erro de conectividade do Azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Data da versão: Abril de 2015

**Problemas fixos e melhorias:**

* O conector do Active Directory não processa eliminações corretamente se a lixeira está ativada e existirem vários domínios na floresta.
* O desempenho de operações de importação foi melhorado para o conector do Azure Active Directory.
* Quando um grupo excedeu o limite de associação (por predefinição, o limite é definido como 50 000 objetos), o grupo foi eliminado no Azure Active Directory. Com o novo comportamento, não é possível eliminar o grupo, ocorrerá um erro e novas alterações de associação não são exportadas.
* Não é possível aprovisionar um novo objeto, se uma exclusão em etapas com o mesmo DN já se encontra presente no espaço conector.
* Alguns objetos são marcados para sincronização durante uma sincronização delta, mesmo que não haja nenhuma alteração no objeto de teste.
* Forçar uma sincronização de palavra-passe também remove a lista de preferências de controlador de domínio.
* CSExportAnalyzer tem problemas com alguns Estados de objetos.

**Novas funcionalidades:**

* Uma junção pode agora ligar a "ANY" tipo de objeto de MV.

## <a name="104850222"></a>1.0.485.0222
Data da versão: Fevereiro de 2015

**Melhorias:**

* Desempenho melhorado de importação.

**Problemas fixos:**

* Sincronização de palavra-passe respeita o atributo de cloudFiltered utilizada pela filtragem de atributos. Objetos filtrados já não estão no âmbito da sincronização de palavra-passe.
* Em raras situações em que a topologia tinha muitos controladores de domínio, a sincronização de palavra-passe não funciona.
* Foi ativado "Parada-server" ao importar a partir do conector do Azure AD depois de gestão de dispositivos no Azure AD/Intune.
* Ingressar em principais de segurança externos (FSPs) de vários domínios na mesma floresta causa um erro de associação a ambígua.

## <a name="104751202"></a>1.0.475.1202
Data da versão: Dezembro de 2014

**Novas funcionalidades:**

* Sincronização de palavra-passe com a filtragem baseada em atributo é agora suportada. Para obter mais informações, consulte [sincronização de palavra-passe com a filtragem](how-to-connect-sync-configure-filtering.md).
* O atributo ms-DS-ExternalDirectoryObjectID é gravado para o Active Directory. Esta funcionalidade adiciona suporte para aplicações do Office 365. Ele usa o OAuth2 para aceder a uma implementação híbrida do Exchange de caixas de correio de Online e no local.

**Problemas de atualização fixos:**

* Uma versão mais recente do Assistente de início de sessão está disponível no servidor.
* Um caminho de instalação personalizada foi utilizado para instalar o Azure AD Sync.
* Um critério de associação personalizado inválido bloqueia a atualização.

**Outras correções:**

* Corrigido os modelos para o Office Pro Plus.
* Problemas de instalação fixo causados por nomes de utilizador que começam com um traço.
* Corrigido perder a definição de sourceAnchor ao executar o Assistente de instalação de uma segunda vez.
* Rastreamento ETW fixo para a sincronização de palavra-passe.

## <a name="104701023"></a>1.0.470.1023
Data da versão: Outubro de 2014

**Novas funcionalidades:**

* Sincronização de palavra-passe a partir de vários locais do Active Directory para o Azure AD.
* Instalação da interface do Usuário localizado para todos os idiomas do Windows Server.

**Atualizar a partir do GA AADSync 1.0**

Se já tiver instalado o Azure AD Sync, isso significa que há uma etapa adicional, que precisa levar no caso de ter alterado a qualquer uma das regras de sincronização de out-of-box. Depois de atualizar para o 1.0.470.1023 versão, a sincronização tiver modificado de regras são duplicadas. Para cada regra de sincronização modificado, faça o seguinte:

1. Localize a regra de sincronização modificou e tome nota das alterações.
1. Elimine a regra de sincronização.
1. Localize a nova regra de sincronização é criada pelo Azure AD Sync e, em seguida, volte a aplicar as alterações.

**Permissões para a conta do Active Directory**

Conta do Active Directory deve ser concedida permissões adicionais para poder ler os hashes de palavra-passe do Active Directory. As permissões a conceder com o nome "Replicar alterações do diretório" e "Diretório de replicar muda tudo." Ambas as permissões têm de ser capaz de ler os hashes de palavra-passe.

## <a name="104190911"></a>1.0.419.0911
Data da versão: setembro de 2014

**Lançamento inicial do Azure AD Sync.**

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
