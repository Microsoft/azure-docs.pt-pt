---
title: 'Azure AD Connect: Versão lança arquivo histórico de lançamento / Microsoft Docs'
description: Este artigo lista todas as versões arquivadas de Azure AD Connect e Azure AD Sync
services: active-directory
author: billmath
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0adf548b009ad6fe0c85501b9777ff23723b3e24
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413416"
---
# <a name="azure-ad-connect-version-release-history-archive"></a>Azure AD Connect: Versão lança arquivo histórico

A equipa do Azure Ative Directory (Azure AD) atualiza regularmente o Azure AD Connect com novas funcionalidades e funcionalidades. Nem todas as adições são aplicáveis a todos os públicos.

>[!NOTE] 
> Este artigo contém informações de referência sobre todas as versões arquivadas do Azure AD - 1.3.20.0 ou mais antiga.  Para ver os lançamentos atuais, consulte o histórico de lançamentos da [versão Azure AD Connect](reference-connect-version-history.md)

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Estado de lançamento 

04/24/2019: Lançamento para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 

- Adicionar suporte para atualização de domínio 
- Recurso de pastas públicas de troca vai GA 
- Melhorar o tratamento de erros do assistente para falhas de serviço 
- Ligação de aviso adicionada na UI do Gestor de Serviço de Sincronização na página de propriedades do conector. 
- A funcionalidade de writeback de grupos unificados é agora GA 
- Melhor mensagem de erro SSPR quando o DC está a perder um controlo LDAP 
- Diagnósticos adicionados para erros de registo da DCOM durante a instalação  
- Melhor rastreio dos erros do PHS RPC 
- Permitir credenciais EA de um domínio infantil 
- Permitir que o nome da base de dados seja introduzido durante a instalação (nome predefinido ADSync)
- Upgrade para ADAL 3.19.8 para pegar uma correção de WS-Trust para Ping e adicionar suporte para novos casos Azure 
- Modificar as Regras de Sincronização do Grupo para fluir samAccountName, DomainNetbios e DomainFQDN para cloud - necessário para reclamações 
- Tratamento de regras de sincronização por defeito modificados – leia mais [aqui](how-to-connect-fix-default-rules.md).
- Adicionou um novo agente a funcionar como um serviço de janelas. Este agente, chamado "Agente Admin", permite diagnósticos remotos mais profundos do servidor AZURE AD Connect para ajudar os Engenheiros da Microsoft a resolver problemas quando abre um caso de suporte. Este agente não está instalado e ativado por defeito.  Para obter mais informações sobre como instalar e ativar o agente consulte [o que é o Agente AD Connect Ad?](whatis-aadc-admin-agent.md) 
- Atualizado o Contrato de Licença de Utilizador Final (EULA) 
- Suporte de upgrade automático adicionado para implementações que usam AD FS como o seu tipo de login.  Isto também removeu a exigência de atualizar o AD FS Azure AD Relying Party Trust como parte do processo de upgrade. 
- Adicionou uma tarefa de gestão de confiança AZure que fornece duas opções: analisar/atualizar a confiança e redefinir a confiança. 
- Alterou o comportamento de confiança do AD FS AD AD Relying Party para que utilize sempre o interruptor -SupportMultipleDomain (inclui atualizações de domínio de confiança e AD AD). 
- Alterou o novo comportamento da quinta AD FS de modo a que seja necessário um certificado .pfx removendo a opção de utilização de um certificado pré-instalado.
- Atualizou o novo fluxo de trabalho agrícola AD FS de modo a permitir apenas a implantação de 1 AD FS e 1 servidor WAP.  Todos os servidores adicionais serão feitos após a instalação inicial. 

### <a name="fixed-issues"></a>Problemas corrigidos 

- Corrija a lógica de reconexão SQL para o serviço ADSync 
- Fixar para permitir a limpeza Instalar usando um DB SQL AOA vazio 
- Corrija o script de permissões ps para refinar permissões GWB 
- Corrigir erros vss com LocalDB  
- Corrija uma mensagem de erro enganosa quando o tipo de objeto não estiver no âmbito 
- Corrigi um problema em que a instalação do Azure AD PowerShell num servidor pode potencialmente causar um conflito de montagem com o Azure AD Connect. 
- Erro PHS fixo no Servidor de Encenação quando as credenciais do conector forem atualizadas na UI do Gestor de Serviço de Sincronização. 
- Corrigi algumas fugas de memória 
- Correções de auto-atualização diversas 
- Correções diversas para exportação e processamento de importação não confirmado 
- Fixo um bug com o manuseamento de uma faixa de costas na filtragem de Domínio e U 
- Corrigiu um problema em que o serviço ADSync demora mais de 2 minutos a parar e causa um problema no momento de atualização. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Estado de lançamento

12/18/2018: Lançamento para download

### <a name="fixed-issues"></a>Problemas corrigidos

Esta construção atualiza os conectores não standard (por exemplo, Conector LDAP Genérico e Conector SQL Genérico) enviados com Azure AD Connect. Para obter mais informações sobre os conectores aplicáveis, consulte a versão 1.1.911.0 no [Histórico de Desbloqueio da versão do Conector](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Estado de lançamento
12/11/2018: Lançamento para download

### <a name="fixed-issues"></a>Problemas corrigidos
Esta construção de hotfix permite ao utilizador selecionar um domínio-alvo, dentro da floresta especificada, para o recipiente 'RegistoDe) ao ativar a redução do dispositivo.  Nas versões anteriores que contêm a nova funcionalidade Opções de Dispositivo (1.1.819.0 – 1.2.68.0), a localização do contentor Devices registado limitava-se à raiz da floresta e não permitia domínios infantis.  Esta limitação apenas se manifestou em novas implementações – as atualizações no local não foram afetadas.  

Se alguma construção que contenha a funcionalidade De Opções de Dispositivo atualizadas foi implantada num novo servidor e a gravação do dispositivo foi ativada, terá de especificar manualmente a localização do recipiente se não o quiser na raiz da floresta.  Para isso, é necessário desativar a gravação do dispositivo e reativá-la, o que lhe permitirá especificar a localização do recipiente na página "Writeback forest".



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Estado de lançamento 

11/30/2018: Lançamento para download

### <a name="fixed-issues"></a>Problemas corrigidos

Esta construção de hotfix corrige um conflito onde pode ocorrer um erro de autenticação devido à presença independente do módulo MSOnline PowerShell Gallery no servidor de sincronização.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Estado de lançamento 

11/19/2018: Lançamento para download

### <a name="fixed-issues"></a>Problemas corrigidos

Esta construção de hotfix corrige uma regressão na construção anterior, onde a writeback de palavra-passe falha ao utilizar um controlador de domínio ADDS no Windows Server 2008/R2.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Estado de lançamento 

10/25/2018: lançado para download


### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 


- Alterou a funcionalidade de desativar o atributo para garantir que o correio de voz hospedado está a funcionar como esperado.  Em certos cenários, a Azure AD estava a sobrepor-se ao atributo msExchUcVoicemailSettings durante a amortização com um valor nulo.  O Azure AD deixará agora de limpar o valor no local deste atributo se o valor da nuvem não for definido.
- Diagnósticos adicionados no assistente Azure AD Connect para investigar e identificar problemas de conectividade ao Azure AD. Estes mesmos diagnósticos também podem ser executados diretamente através do PowerShell utilizando o Cmdlet Test-AdSyncAzureServiceConnectivity. 
- Diagnósticos adicionados no assistente Azure AD Connect para investigar e identificar problemas de conectividade à AD. Estes mesmos diagnósticos também podem ser executados diretamente através do PowerShell utilizando a função Start-ConnectivityValidation no módulo ADConnectivityTools PowerShell.  Para mais informações consulte [o que é o Módulo PowerShell ADConnectivityTool?](how-to-connect-adconnectivitytools.md)
- Adicionou uma versão de esquema AD pré-verificação para Hybrid Azure Ative Directory Join e ressarte de dispositivo 
- Alterou a procura de atributos da página de extensão do diretório para não ser sensível a casos.
-   Acrescentou suporte total para TLS 1.2. Esta versão suporta que todos os outros protocolos sejam desativados e apenas o TLS 1.2 esteja ativado na máquina onde o Azure AD Connect está instalado.  Para mais informações consulte [a aplicação da aplicação da aplicação da TLS 1.2 para a Azure AD Connect](reference-connect-tls-enforcement.md)



### <a name="fixed-issues"></a>Problemas corrigidos   

- Corrigiu um bug onde o Azure AD Connect Upgrade falharia se o SQL Always On estivesse a ser utilizado. 
- Fixo um bug para analisar corretamente os nomes da OU que contêm um corte para a frente. 
- Corrigiu um problema em que Pass-Through Autenticação seria desativada para uma instalação limpa no modo de preparação. 
- Corrigiu um bug que impedia que o módulo PowerShell fosse carregado ao executar as ferramentas de resolução de problemas 
- Corrigiu um bug que impediria os clientes de usarem valores numéricos no primeiro carácter de um nome de hospedeiro. 
- Fixo um bug onde o Azure AD Connect permitiria divisórias inválidas e a seleção de contentores 
- Corrigi a mensagem de erro "Palavra-passe inválida" quando o Desktop SSO estiver ativado. 
- Várias correções de bugs para a AD FS Trust Management  
- Ao configurar o Writeback do Dispositivo - fixou o cheque de esquema para procurar a classe de objetos MSDs-DeviceContainer (introduzida no WS2012 R2)


## <a name="118820"></a>1.1.882.0  

9/7/2018: lançado para download, não será lançado para upgrade automático 

### <a name="fixed-issues"></a>Problemas corrigidos  

O Azure AD Connect Upgrade falha se o SQL Always On Availability estiver configurado para o DB ADSync. Este hotfix aborda esta questão e permite que o Upgrade tenha sucesso. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Estado de lançamento

8/21/2018: Lançado para download e atualização automática. 

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

- A integração ping federate no Azure AD Connect já está disponível para Disponibilidade Geral. [Saiba mais sobre como federar AD AZure com Ping Federate](./plan-connect-user-signin.md#federation-with-pingfederate)
- O Azure AD Connect cria agora a cópia de segurança da confiança AD AD em AD FS sempre que uma atualização é feita e armazena-a num ficheiro separado para uma fácil restauração, se necessário. [Saiba mais sobre a nova funcionalidade e gestão de confiança AZure AD no Azure AD Connect](./how-to-connect-azure-ad-trust.md).
- Nova ferramenta de resolução de problemas ajuda a resolver problemas alterando o endereço de e-mail primário e escondendo a conta da lista de endereços globais
- Azure AD Connect foi atualizado para incluir o mais recente CLIENTE Nativo SQL Server 2012
- Quando muda o registo do utilizador para a Sincronização de Hash password ou para a autenticação pass-through na tarefa "Alterar o registo do utilizador", a caixa de verificação Sem Emenda Sign-On é ativada por predefinição.
- Suporte adicional para Windows Server Essentials 2019
- O agente Azure AD Connect Health foi atualizado para a versão mais recente 3.1.7.0
- Durante uma atualização, se o instalador detetar alterações nas regras de sincronização predefinidas, o administrador é solicitado com um aviso antes de sobrepor as regras modificadas. Isto permitirá ao utilizador tomar medidas corretivas e retomar mais tarde. Comportamento Antigo: Se houvesse alguma regra fora da caixa modificada, então a atualização manual estava a sobrepor essas regras sem dar qualquer aviso ao utilizador e o programador de sincronização foi desativado sem informar o utilizador. Novo Comportamento: O utilizador será solicitado com aviso antes de sobrepor as regras de sincronização fora da caixa modificadas. O utilizador terá a opção de interromper o processo de atualização e retomar mais tarde depois de tomar medidas corretivas.
- Forneça um melhor manuseamento de um problema de conformidade com o FIPS, fornecendo uma mensagem de erro para a geração de haxixe MD5 num ambiente compatível com FIPS e um link para documentação que fornece um trabalho em torno deste problema.
- Atualização da UI para melhorar as tarefas da federação no assistente, que estão agora sob um subgrupo separado para a federação. 
- Todas as tarefas adicionais da federação estão agora agrupadas sob um único sub-menu para facilitar a utilização.
- Um novo Módulo ADSyncConfig Posh (AdSyncConfig.psm1) com novas funções de Permissões AD movidos do antigo ADSyncPrep.psm1 (que pode ser depreciado em breve)

### <a name="fixed-issues"></a>Problemas corrigidos 

- Corrigiu um bug onde o servidor AZure AD Connect mostraria uma elevada utilização do CPU após a atualização para .NET 4.7.2
- Corrigiu um bug que produziria intermitentemente uma mensagem de erro para um problema de impasse auto-resolvido do SQL
- Fixou vários problemas de acessibilidade para o Editor de Regras de Sincronização e o Gestor de Serviços de Sincronização  
- Corrigiu um bug onde o Azure AD Connect não pode obter informações de definição de registo
- Corrigiu um bug que criou problemas quando o utilizador avança/recua no assistente
- Corrigiu um bug para evitar que um erro acontecesse devido a uma mão multi-rosca incorreta no assistente
- Quando a página de filtragem do Sync do Grupo encontra um erro LDAP ao resolver grupos de segurança, o Azure AD Connect devolve agora a exceção com total fidelidade.  A causa principal da exceção de encaminhamento ainda é desconhecida e será abordada por um bug diferente.
-  Fixou um bug onde as permissões para as teclas STK e NGC (atributo MS-DS-KeyCredentialLink nos objetos do Utilizador/Dispositivo para WHfB) não foram corretamente definidas.     
- Corrigiu um bug onde 'Set-ADSyncRestrictedPermissions' não foi chamado corretamente
-  Adicionar suporte para permissão de concessão de writeback do Grupo no assistente de instalação do Azure ADConnect
- Ao alterar o sinal no método de Password Hash Sync para AD FS, o Password Hash Sync não foi desativado.
- Verificação adicional para endereços IPv6 na configuração AD FS
- Atualize a mensagem de notificação para informar que existe uma configuração existente.
- O recuo do dispositivo não deteta o contentor na floresta não fidedquica. Isto foi atualizado para fornecer uma melhor mensagem de erro e um link para a documentação apropriada
- Desseleccionar um OU e, em seguida, sincronizar/writeback correspondente a esse OU dá um erro de sincronização genérica. Isto foi alterado para criar uma mensagem de erro mais compreensível.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Estado de lançamento

5/14/2018: Lançado para upgrade e download automático.

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

Novas funcionalidades e melhorias

- Esta versão inclui a pré-visualização pública da integração do PingFederate no Azure AD Connect. Com esta versão, os clientes podem configurar facilmente, e de forma fiável, o seu ambiente Azure Ative Directory para alavancar o PingFederate como seu provedor de federação. Para saber mais sobre como usar esta nova funcionalidade, visite a nossa [documentação online.](plan-connect-user-signin.md#federation-with-pingfederate) 
- Atualizou o Utilitário de Resolução de Problemas do Assistente de Ligação AD Azure, onde agora analisa mais cenários de erro, como caixas de correio ligadas e grupos dinâmicos AD. Leia mais sobre a utilidade de resolução de problemas [aqui.](tshoot-connect-objectsync.md)
- A configuração de writeback do dispositivo é agora gerida exclusivamente dentro do Azure AD Connect Wizard.
- Um novo Módulo PowerShell chamado ADSyncTools.psm1 é adicionado que pode ser usado para resolver problemas de conectividade SQL e vários outros utilitários de resolução de problemas. Leia mais sobre o módulo ADSyncTools [aqui.](tshoot-connect-tshoot-sql-connectivity.md) 
- Foi adicionada uma nova tarefa adicional "Configurar opções de dispositivos". Pode utilizar a tarefa para configurar as duas operações seguintes: 
  - **Hybrid Azure AD adere** : Se o seu ambiente tiver uma pegada AD no local e também quiser beneficiar das capacidades fornecidas pelo Azure Ative Directory, pode implementar dispositivos híbridos Azure AD. Estes são dispositivos associados tanto ao Active Directory no local, como ao Azure Active Directory.
  - **Writeback do dispositivo** : A writeback do dispositivo é utilizada para permitir o acesso condicional com base em dispositivos a dispositivos protegidos AD FS (2012 R2 ou superior)

    >[!NOTE] 
    > - A opção de ativar a gravação do dispositivo a partir de opções de sincronização personalizadas será acinzentada. 
    > -  O módulo PowerShell para ADPrep é depreciado com esta versão.


### <a name="fixed-issues"></a>Problemas corrigidos 

- Esta versão atualiza a instalação SQL Server Express para o SQL Server 2012 SP4, que, entre outros, fornece correções para várias vulnerabilidades de segurança.  Consulte [aqui](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) mais informações sobre o SQL Server 2012 SP4.
- Processamento de regras de sincronização: as regras de sincronização de saída sem condição de junção devem ser desaplicáveis se a regra de sincronização dos pais deixar de ser aplicável
- Várias correções de acessibilidade foram aplicadas ao UI do Serviço de Sincronização e ao Editor de Regras de Sincronização
- Assistente de ligação AZure AD: Erro que cria conta de conector AD quando a Azure AD Connect está num grupo de trabalho
- Assistente de ligação AZure AD: Na página de entrada AD Azure, a caixa de verificação é sempre que existe qualquer incompatibilidade nos domínios AD e Azure AD
- Atualização automática da Correção PowerShell para definir o estado de atualização automática corretamente em certos casos após a tentativa de atualização automática.
- Azure AD Connect Wizard: Telemetria atualizada para capturar informações anteriormente desaparecidas
- Assistente de ligação AD AD: Foram feitas as seguintes alterações quando utiliza a tarefa **de inscrição** do utilizador change para mudar de AD FS para Autenticação Pass-through:
    - O Agente de Autenticação Pass-through está instalado no servidor Azure AD Connect e a função de autenticação pass-through está ativada, antes de convertermos domínios de federados para geridos.
    - Os utilizadores já não são convertidos de federados para geridos. Apenas os domínios são convertidos.
- Azure AD Connect Wizard: AD FS Multi Domain Regex não está correto quando o utilizador UPN tem a atualização regex de caracteres especiais para suportar caracteres especiais
- Assistente de ligação Azure AD: Remova a mensagem "Configure source anchor attribut" quando não há alteração 
- Azure AD Connect Wizard: Suporte AD FS para o cenário da dupla federação
- Azure AD Connect Wizard: AD FS Claims não são atualizados para domínio adicionado ao converter um domínio gerido para federado
- Azure AD Connect Wizard: Durante a deteção de pacotes instalados, encontramos produtos relacionados com a ligação a um ad de dirsync/Azure. Tentaremos agora desinstalar os produtos velhos.
- Assistente de ligação Azure AD: Corrigir mapeamento de mensagem de erro quando a instalação do agente de autenticação passthrough falha
- Assistente de ligação AZure AD: Recipiente de "Configuração" removido da página de filtragem do Domain OU
- Instalação do Sync Engine: remova a lógica legado desnecessária que ocasionalmente falhou na instalação do Sync Engine msi
- Azure AD Connect Wizard: Corrigir texto de ajuda popup na página de funcionalidades opcionais para password Hash Sync
- Sincronizar o tempo de funcionaamento do motor: Fixe o cenário em que um objeto CS tenha uma eliminação importada e as Regras de Sincronização tentem realojar o objeto.
- Sincronizar o tempo de funcionamento do motor: Adicionar link de ajuda para guia de resolução de problemas de conectividade on-line ao registo de eventos para um Erro de Importação
- Sincronização do tempo de funcionamento do motor: Utilização reduzida da memória do Programador de Sincronização ao enumerar conectores
- Azure AD Connect Wizard: Corrija um problema que resolva uma conta de serviço de sincronização personalizada que não tenha privilégios de leitura AD
- Assistente de ligação AZure AD: Melhorar o registo de seleções de filtragem de domínio e uA
- Azure AD Connect Wizard: AD FS Add predefinidos reclamações à confiança da federação criada para cenário de MFA
- Assistente de ligação AZure AD: AD FS Deploy WAP: Adicionar servidor não utiliza novo certificado
- Azure AD Connect Wizard: Exceção DSSO quando em PremCredentials não são inicializados para um domínio 
- Preferencialmente fluir o atributo AD distintoName do objeto Utilizador Ativo.
- Fixo um bug cosmético foi a Precedência da primeira Regra de Sincronização OOB foi definida para 99 em vez de 100



## <a name="117510"></a>1.1.751.0
Status 4/12/2018: Lançado apenas para download

>[!NOTE]
>Este lançamento é um hotfix para Azure AD Connect
### <a name="azure-ad-connect-sync"></a>Sincronização do Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
Corrigido um problema eram a descoberta automática de exemplos Azure para os inquilinos da China estava ocasionalmente falhando.  

### <a name="ad-fs-management"></a>Gestão de AD FS
#### <a name="fixed-issues"></a>Problemas corrigidos

Havia um problema na lógica de relemisão de configuração que resultaria numa argumentação argumentando que "um item com a mesma chave já foi adicionado".  Isto faria com que todas as operações de repetição falhassem.

## <a name="117500"></a>1.1.750.0
Estado 3/22/2018: Lançado para upgrade automático e download.
>[!NOTE]
>Quando a atualização para esta nova versão estiver concluída, irá automaticamente desencadear uma sincronização completa e uma importação completa para o conector AZURE AD e uma sincronização completa para o conector AD. Uma vez que isto pode demorar algum tempo, dependendo do tamanho do seu ambiente Azure AD Connect, certifique-se de que tomou as medidas necessárias para o suportar ou adiar a atualização até encontrar um momento conveniente para o fazer.
>[!NOTE]
>"A funcionalidade AutoUpgrade foi incorretamente desativada para alguns inquilinos que implantaram construções mais tarde de 1.1.524.0. Para garantir que a sua instância AD Connect Azure ainda é elegível para AutoUpgrade, executar o seguinte cmdlet PowerShell: "Set-ADSyncAutoUpgrade -AutoupGradeState Enabled"

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos

* Set-ADSyncAutoUpgrade cmdlet bloquearia previamente o Auto-upgrade se o estado de atualização automática estiver definido para suspenso. Esta funcionalidade mudou agora, pelo que não bloqueia o AutoUpgrade de futuras construções.
* Alterou a opção **de página de acesso ao utilizador** "Sincronização de passwords" para "Sincronização de Hash password".  O Azure AD Connect sincroniza as hashes de palavra-passe, não as palavras-passe, pelo que isto está alinhado com o que está realmente a ocorrer.  Para mais informações consulte [implementar a sincronização de hash de palavra-passe com sincronização Azure AD Connect](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Estado: Lançado para selecionar clientes

>[!NOTE]
>Quando a atualização para esta nova versão estiver concluída, irá automaticamente desencadear uma sincronização completa e uma importação completa para o conector AZURE AD e uma sincronização completa para o conector AD. Uma vez que isto pode demorar algum tempo, dependendo do tamanho do seu ambiente Azure AD Connect, certifique-se de que tomou as medidas necessárias para o suportar ou adiar a atualização até encontrar um momento conveniente para o fazer.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
* Fixar a janela de tempo nas tarefas de fundo para a página de filtragem de partição ao mudar para a página seguinte.

* Corrigiu um bug que causou a violação do Acesso durante a ação personalizada ConfigDB.

* Corrigiu um erro para recuperar do tempo limite de ligação SQL.

* Fixou um bug onde os certificados com wildcards san falharam uma verificação pré-requisito.

* Corrigiu um inseto que faz com que miiserver.exe se despenhe durante a exportação de um conector AD Azure.

* Corrigiu um erro que a tentativa de má palavra-passe registou em DC ao executar o assistente Azure AD Connect para alterar a configuração.


#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Adicionar Definições de Privacidade para o Regulamento Geral de Proteção de Dados (RGPD).  Para mais informações consulte o artigo [aqui.](reference-connect-user-privacy.md)

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* telemetria de aplicação - o administrador pode mudar esta classe de dados ligados/desligados à vontade

* Dados da Ad Health Azure - o administrador deve visitar o portal da saúde para controlar as suas definições de saúde.
   Uma vez alterada a política de serviços, os agentes vão lê-la e aplicá-la.

* Ações de configuração de recuo de dispositivo adicionados e uma barra de progresso para a inicialização da página

* Diagnóstico Geral melhorado com relatório HTML e recolha completa de dados num relatório ZIP-Text/HTML

* Melhorou a fiabilidade da atualização automática e adicionou telemetria adicional para garantir que a saúde do servidor pode ser determinada

* Restringir permissões disponíveis para contas privilegiadas na conta do Conector AD

  * Para novas instalações, o assistente irá restringir as permissões que as contas privilegiadas têm na conta MSOL após a criação da conta MSOL.

As alterações terão a ver com o seguinte:
1. Instalações Express
2. Instalações personalizadas com conta De criar automaticamente
3. Alterou o instalador para que não exija privilégio SA na instalação limpa do Azure AD Connect

* Adicionei uma nova utilidade para resolver problemas de sincronização para um objeto específico. Está disponível sob a opção 'Troubleshoot Object Synchronization' da Azure AD Connect Wizard Troubleshoot Tarefa Adicional. Atualmente, os serviços públicos verificam o seguinte:

  * Desfasamento entre o objeto de utilizador sincronizado e a conta de utilizador no Azure AD Tenant.
  * Se o objeto for filtrado da sincronização devido à filtragem do domínio
  * Se o objeto for filtrado da sincronização devido à filtragem da unidade organizacional (OU)

* Adicionou um novo utilitário para sincronizar o hash de senha atual armazenado no Ative Directory no local para uma conta de utilizador específica.

O utilitário não requer uma alteração de senha. Está disponível sob a opção 'Troubleshoot Password Hash Synchronization' da Azure AD Connect Wizard Troubleshoot Tarefa Adicional.






## <a name="116540"></a>1.1.654.0
Estado: 12 de dezembro de 2017

>[!NOTE]
>Esta versão é um hotfix relacionado com segurança para Azure AD Connect
### <a name="azure-ad-connect"></a>Azure AD Connect
Foi adicionada uma melhoria à versão 1.1.654.0 (e depois) do Azure AD Connect para garantir que as alterações de permissão recomendadas descritas na secção [Bloquear o acesso à conta AD DS](#lock) são automaticamente aplicadas quando o Azure AD Connect cria a conta DS AD. 

- Ao configurar o Azure AD Connect, o administrador de instalação pode fornecer uma conta DS AD existente ou deixar o Azure AD Connect criar automaticamente a conta. As alterações de permissão são aplicadas automaticamente na conta DS AD que é criada pelo Azure AD Connect durante a configuração. Não são aplicadas à conta DS AD existente fornecida pelo administrador de instalação.
- Para os clientes que tenham atualizado a partir de uma versão mais antiga do Azure AD Connect para 1.1.654.0 (ou depois), as alterações de permissão não serão aplicadas retroativamente às contas DS AD existentes criadas antes da atualização. Só serão aplicadas a novas contas DS AD criadas após a atualização. Isto ocorre quando está a adicionar novas florestas de AD para serem sincronizadas ao Azure AD.

>[!NOTE]
>Esta versão apenas remove a vulnerabilidade para novas instalações do Azure AD Connect onde a conta de serviço é criada pelo processo de instalação. Para as instalações existentes, ou nos casos em que forneça a conta por si mesmo, deve certificar-se de que esta vulnerabilidade não existe.
#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a> Bloqueie o acesso à conta DS da AD
Bloquear o acesso à conta DS da AD implementando as seguintes alterações de permissão no AD no local:  

*   Desativar a herança no objeto especificado
*   Remova todos os ACEs no objeto específico, exceto ACEs específicos de SELF. Queremos manter as permissões por defeito intactas quando se trata de SELF.
*   Atribua estas permissões específicas:

Tipo     | Name                          | Access               | Aplica-se A
---------|-------------------------------|----------------------|--------------|
Permitir    | SISTEMA                        | Controlo Total         | Este objeto  |
Permitir    | Administradores da Empresa             | Controlo Total         | Este objeto  |
Permitir    | Administradores do Domínio                 | Controlo Total         | Este objeto  |
Permitir    | Administradores                | Controlo Total         | Este objeto  |
Permitir    | Controladores de domínio da empresa | Conteúdo da Lista        | Este objeto  |
Permitir    | Controladores de domínio da empresa | Ler todas as propriedades  | Este objeto  |
Permitir    | Controladores de domínio da empresa | Ler Permissões     | Este objeto  |
Permitir    | Utilizadores Autenticados           | Conteúdo da Lista        | Este objeto  |
Permitir    | Utilizadores Autenticados           | Ler todas as propriedades  | Este objeto  |
Permitir    | Utilizadores Autenticados           | Ler Permissões     | Este objeto  |

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Script PowerShell para apertar uma conta de serviço pré-existente

Para utilizar o script PowerShell, para aplicar estas definições, a uma conta DS AD pré-existente (éter fornecido pela sua organização ou criado por uma instalação anterior do Azure AD Connect, descarregue o script a partir do link acima fornecido.

##### <a name="usage"></a>Utilização:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Onde 

**$ObjectDN** = A conta Ative Directory cujas permissões precisam de ser reforçadas.

**$Credential** = Credencial de administrador que tem os privilégios necessários para restringir as permissões na conta $ObjectDN. Estes privilégios são normalmente detidos pelo administrador da Enterprise ou do Domínio. Utilize o nome de domínio totalmente qualificado da conta do administrador para evitar falhas de procura de conta. Exemplo: contoso.com\admin.

>[!NOTE] 
>$credential. O Nome do Utilizador deve estar no formato FQDN\username. Exemplo: contoso.com\admin 
##### <a name="example"></a>Exemplo:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Esta vulnerabilidade foi usada para ter acesso não autorizado?

Para ver se esta vulnerabilidade foi usada para comprometer a sua configuração Azure AD Connect, deve verificar a última data de reset da palavra-passe da conta de serviço.  Se o tempo de tempo em inesperada, uma investigação mais aprofundada, através do registo do evento, para o evento de reset da palavra-passe, deve ser realizado.

Para mais informações, consulte [o Microsoft Security Advisory 4056318](/security-updates/securityadvisories/2017/4056318)

## <a name="116490"></a>1.1.649.0
Estado: 27 de outubro de 2017

>[!NOTE]
>Esta construção não está disponível para os clientes através da funcionalidade Azure AD Connect Auto Upgrade.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Problema  corrigido
* Corrigiu um problema de compatibilidade de versão entre o Azure AD Connect e o Azure AD Connect Health Agent (para sincronização). Este problema afeta os clientes que estão a realizar a atualização do Azure AD Connect no local para a versão 1.1.647.0, mas que atualmente tem a versão 3.0.127.0 do Health Agent. Após a atualização, o Agente de Saúde já não pode enviar dados de saúde sobre o Serviço de Sincronização AZURE AD Connect Para o Serviço de Saúde Azure AD. Com esta correção, a versão 3.0.129.0 do Health Agent é instalada durante a atualização do Azure AD Connect no local. A versão 3.0.129.0 do Health Agent não tem problema de compatibilidade com a versão 1.1.649.0 do Health Agent.


## <a name="116470"></a>1.1.647.0
Estado: 19 de outubro de 2017

> [!IMPORTANT]
> Existe um problema de compatibilidade conhecido entre a versão 1.1.647.0 e a versão 3.0.127.0 do Azure AD Connect Health Agent (para sincronização). Este problema impede o Agente de Saúde de enviar dados de saúde sobre o Serviço de Sincronização AZURE AD Connect (incluindo erros de sincronização de objetos e executar dados de histórico) para o Serviço de Saúde Azure AD. Antes de atualizar manualmente a sua implementação Azure AD Connect para a versão 1.1.647.0, verifique a versão atual do Azure AD Connect Health Agent instalado no seu servidor Azure AD Connect. Pode fazê-lo indo ao *Painel de Controlo → adicionar programas de remoção* e procurar a aplicação Microsoft *Azure AD Connect Health Agent for Sync*. Se a sua versão for 3.0.127.0, recomenda-se que aguarde que a próxima versão AZure AD Connect esteja disponível antes da atualização. Se a versão do Health Agent não for 3.0.127.0, não há problema em proceder ao atualização manual e no local. Note que este problema não afeta o upgrade do balanço ou os clientes que estão a realizar uma nova instalação do Azure AD Connect.
>
>
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigiu um problema com a tarefa *de inscrição do utilizador Change* no assistente Azure AD Connect:

  * O problema ocorre quando tem uma implementação de Conexão Azure AD existente com sincronização de palavras-passe **ativada** , e está a tentar definir o método de entrada do utilizador como *Autenticação Pass-through*. Antes de aplicar a alteração, o assistente mostra incorretamente a indicação " *Desativar a sincronização da palavra-passe* ". No entanto, a sincronização de palavras-passe permanece ativada após a aplicação da alteração. Com esta correção, o assistente já não mostra a solicitação.

  * Por design, o assistente não desativa a sincronização de palavras-passe quando atualiza o método de entrada do utilizador utilizando a tarefa *de entrada do utilizador Change.* Isto é para evitar perturbações para os clientes que querem manter a Sincronização de Passwords, mesmo que estejam a permitir a Autenticação pass-through ou a federação como o seu principal método de entrada de utilizador.

  * Se pretender desativar a Sincronização de Palavras-Passe após a atualização do método de entrada no utilizador, tem de executar a tarefa *de Configuração de Sincronização de Sincronização personalizada* no assistente. Quando navegar para a página *de funcionalidades Opcionais,* desmarque a opção *de sincronização de palavras-passe.*

  * Note que o mesmo problema também ocorre se tentar ativar/desativar o Sign-On Único Sem Emenda. Especificamente, tem uma implementação de Conexão Azure AD existente com sincronização de palavras-passe ativada e o método de entrada do utilizador já está configurado como *Autenticação Pass-through*. Utilizando a tarefa *de entrada do utilizador Change,* tente verificar/desmarcar a opção Ativar sem emenda única de *acesso,* enquanto o método de entrada do utilizador permanece configurado como "Autenticação Pass-through". Antes de aplicar a alteração, o assistente mostra incorretamente a indicação " *Desativar a sincronização da palavra-passe* ". No entanto, a sincronização de palavras-passe permanece ativada após a aplicação da alteração. Com esta correção, o assistente já não mostra a solicitação.

* Corrigiu um problema com a tarefa *de inscrição do utilizador Change* no assistente Azure AD Connect:

  * O problema ocorre quando tem uma implementação de Ligação AZure AD existente com sincronização de palavras-passe **desativada** , e está a tentar definir o método de entrada do utilizador como *Autenticação Pass-through*. Quando a alteração é aplicada, o assistente ativa a autenticação pass-through e a sincronização de palavras-passe. Com esta correção, o assistente já não permite a sincronização de palavras-passe.

  * Anteriormente, a Sincronização de Passwords era um pré-requisito para permitir a autenticação pass-through. Quando definir o método de entrada do utilizador como *Autenticação Pass-through,* o assistente ativaria a autenticação pass-through e a sincronização de passwords. Recentemente, a Sincronização de Passwords foi removida como um pré-requisito. Como parte da versão 1.1.557.0 do Azure AD Connect, foi feita uma alteração ao Azure AD Connect para não ativar a sincronização de passwords quando definiu o método de entrada do utilizador como *Autenticação Pass-through*. No entanto, a alteração foi aplicada apenas à instalação Azure AD Connect. Com esta correção, a mesma alteração também é aplicada na tarefa de inscrição do *utilizador Change.*

  * Note que o mesmo problema também ocorre se tentar ativar/desativar o Sign-On Único Sem Emenda. Especificamente, tem uma implementação de Conexão Azure AD existente com sincronização de palavras-passe desativada e o método de entrada do utilizador já está configurado como *Autenticação Pass-through*. Utilizando a tarefa *de entrada do utilizador Change,* tente verificar/desmarcar a opção Ativar sem emenda única de *acesso,* enquanto o método de entrada do utilizador permanece configurado como "Autenticação Pass-through". Quando a alteração é aplicada, o assistente ativa a sincronização da palavra-passe. Com esta correção, o assistente já não permite a sincronização de palavras-passe. 

* Corrigiu um problema que fez com que a atualização do Azure AD Connect falhasse com o erro " *Incapaz de atualizar o Serviço de Sincronização* ". Além disso, o Serviço de Sincronização já não pode começar com erro de evento " *O serviço não foi capaz de iniciar porque a versão da base de dados é mais recente do que a versão dos binários instalados* ". O problema ocorre quando o administrador que executa a atualização não tem privilégio sysadmin para o servidor SQL que está a ser utilizado pelo Azure AD Connect. Com esta correção, o Azure AD Connect apenas requer que o administrador tenha db_owner privilégio à base de dados ADSync durante a atualização.

* Corrigiu um problema de Azure AD Connect Upgrade que afetou os clientes que ativaram [o Sign-On Único Sem Emenda](./how-to-connect-sso.md). Depois de o Azure AD Connect ser atualizado, o Seamless Single Sign-On aparece incorretamente como desativado no assistente AZure AD Connect, mesmo que a funcionalidade permaneça ativada e totalmente funcional. Com esta correção, a funcionalidade aparece agora corretamente como ativada no assistente.

* Corrigiu um problema que fez com que o assistente Azure AD Connect mostrasse sempre a mensagem " *Configure Source Anchor* " na página *Ready to Configure,* mesmo que não fossem efetuadas alterações relacionadas com a Âncora de Origem.

* Ao realizar a atualização manual in-place do Azure AD Connect, o cliente é obrigado a fornecer as credenciais de Administrador Global do inquilino AD correspondente. Anteriormente, a atualização poderia prosseguir, mesmo que as credenciais do Administrador Global pertencessem a um inquilino AD Azure diferente. Embora a atualização pareça estar concluída com sucesso, certas configurações não são corretamente persistiu com a atualização. Com esta alteração, o assistente impede que a atualização prossiga se as credenciais fornecidas não corresponderem ao inquilino AZure AD.

* Removido a lógica redundante que reiniciou desnecessariamente o serviço Azure AD Connect Health no início de uma atualização manual.


#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* Somou lógica para simplificar os passos necessários para configurar o Azure AD Connect com o Microsoft Germany Cloud. Anteriormente, é necessário atualizar chaves de registo específicas no servidor Azure AD Connect para que funcione corretamente com a Microsoft Germany Cloud, conforme descrito neste artigo. Agora, o Azure AD Connect pode detetar automaticamente se o seu inquilino estiver na Microsoft Germany Cloud com base nas credenciais globais de administrador fornecidas durante a configuração.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
> [!NOTE]
> Nota: O Serviço de Sincronização tem uma interface WMI que lhe permite desenvolver o seu próprio programador personalizado. Esta interface está agora depretada e será removida das futuras versões do Azure AD Connect enviadas após 30 de junho de 2018. Os clientes que pretendam personalizar o calendário de sincronização devem utilizar o [programador incorporado.](./how-to-connect-sync-feature-scheduler.md)
#### <a name="fixed-issues"></a>Problemas corrigidos
* Quando o assistente Azure AD Connect cria a conta de Conector AD necessária para sincronizar as alterações a partir do Ative Directory no local, não atribui corretamente a conta a permissão necessária para ler objetos PublicFolder. Este problema afeta tanto a instalação express como a instalação personalizada. Esta mudança corrige a questão.

* Corrigiu um problema que fez com que a página de resolução de problemas do Azure AD Connect Wizard não prestasse corretamente para os administradores que executam a partir do Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* Ao resolver problemas a sincronização de passwords utilizando a página de resolução de problemas do assistente Azure AD Connect, a página de resolução de problemas retorna agora o estado específico do domínio.

* Anteriormente, se tentou ativar a sincronização de passwords Hash Synch, o Azure AD Connect não verifica se a conta de Conector AD exigiu permissões para sincronizar hashes de password a partir de AD no local. Agora, o assistente AZure AD Connect verificará e irá avisá-lo se a conta de Conector AD não tiver permissões suficientes.

### <a name="ad-fs-management"></a>Gestão de AD FS
#### <a name="fixed-issue"></a>Problema  corrigido
* Corrigi um problema relacionado com a utilização da [ms-DS-ConsistencyGuid como](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) recurso Source Anchor. Este problema afeta clientes que configuraram *a Federação com FS AD* como o método de inscrição do utilizador. Quando executa a tarefa *Configure Source Anchor* no assistente, o Azure AD Connect comuta para usar *ms-DS-ConsistênciayGuid como atributo de origem para imutávelId. Como parte desta mudança, o Azure AD Connect tenta atualizar as regras de reclamação do ImuttableId em AD FS. No entanto, este passo falhou porque o Azure AD Connect não tinha as credenciais de administrador necessárias para configurar a AD FS. Com esta correção, o Azure AD Connect pede-lhe agora que introduza as credenciais de administrador para AD FS quando executa a tarefa *Configure Source Anchor.*



## <a name="116140"></a>1.1.614.0
Estado: 05 de setembro de 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Problemas conhecidos
* Existe um problema conhecido que está a fazer com que a atualização do Azure AD Connect falhe com o erro " *Incapaz de atualizar o Serviço de Sincronização* ". Além disso, o Serviço de Sincronização já não pode começar com erro de evento " *O serviço não foi capaz de iniciar porque a versão da base de dados é mais recente do que a versão dos binários instalados* ". O problema ocorre quando o administrador que executa a atualização não tem privilégio sysadmin para o servidor SQL que está a ser utilizado pelo Azure AD Connect. Permissões dbo não são suficientes.

* Existe um problema conhecido com o Azure AD Connect Upgrade que está a afetar os clientes que ativaram [o Sign-On Único Sem Emenda.](how-to-connect-sso.md) Depois de o Azure AD Connect ser atualizado, a funcionalidade aparece como desativada no assistente, mesmo que a funcionalidade permaneça ativada. Uma correção para esta questão será fornecida no futuro. Os clientes que estão preocupados com este problema de exibição podem corrigi-lo manualmente, permitindo que o Single sem emenda Sign-On no assistente.

#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigiu um problema que impedia o Azure AD Connect de atualizar as regras de sinistros no AD FS no local, permitindo ao mesmo tempo o [ms-DS-ConsistencyGuid como](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) fonte anchor. O problema ocorre se tentar ativar a funcionalidade para uma implementação existente do Azure AD Connect que tenha O FS configurado como método de inscrição. O problema ocorre porque o assistente não solicita credenciais ADFS antes de tentar atualizar as regras de sinistros em AD FS.
* Corrigiu um problema que fez com que o Azure AD Connect falhasse a instalação se a floresta AD no local tiver NTLM desativada. O problema deve-se ao assistente Azure AD Connect não fornecer credenciais totalmente qualificadas ao criar os contextos de segurança necessários para a autenticação kerberos. Isto faz com que a autenticação kerberos falhe e o assistente Azure AD Connect recue para usar o NTLM.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigiu um problema em que não é possível criar uma nova regra de sincronização se o atributo Tag não estiver povoado.
* Corrigiu um problema que fez com que o Azure AD Connect se ligasse ao AD no local para sincronização de passwords utilizando o NTLM, mesmo estando a Kerberos disponível. Este problema ocorre se a topologia de AD no local tiver um ou mais controladores de domínio que foram restaurados a partir de uma cópia de segurança.
* Corrigiu um problema que fez com que as etapas de sincronização completas ocorressem desnecessariamente após a atualização. Em geral, são necessárias etapas de sincronização completas após a atualização se houver alterações nas regras de sincronização fora da caixa. O problema deveu-se a um erro na lógica de deteção de alterações que detetou incorretamente uma alteração ao encontrar a expressão da regra de sincronização com caracteres newline. Os caracteres newline são inseridos na expressão da regra de sincronização para melhorar a legibilidade.
* Corrigiu um problema que pode fazer com que o servidor Azure AD Connect não funcione corretamente após a atualização automática. Este problema afeta os servidores Azure AD Connect com a versão 1.1.443.0 (ou mais cedo). Para obter detalhes sobre o assunto, consulte o artigo [Azure AD Connect não está a funcionar corretamente após uma atualização automática](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Corrigiu um problema que pode fazer com que a Atualização Automática seja novamente julgada a cada 5 minutos quando os erros são encontrados. Com a correção, a atualização automática de retrações com recuo exponencial quando se encontram erros.
* Corrigiu um problema em que o evento de sincronização de palavras-passe 611 é incorretamente mostrado nos registos do Evento de Aplicação do Windows como **informativo** em vez de **erro**. O Evento 611 é gerado sempre que a sincronização da palavra-passe encontra um problema. 
* Corrigiu um problema no assistente Azure AD Connect que permite ativar a função de writeback do Grupo sem selecionar um OU necessário para a gravação do Grupo.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* Adicionei uma tarefa de resolução de problemas ao assistente Azure AD Connect em Tarefas Adicionais. Os clientes podem aproveitar esta tarefa para resolver problemas relacionados com a sincronização de palavras-passe e recolher diagnósticos gerais. No futuro, a tarefa de resolução de problemas será alargada para incluir outras questões relacionadas com a sincronização do diretório.
* O Azure AD Connect suporta agora um novo modo de instalação chamado **Use Existing Database**. Este modo de instalação permite aos clientes instalarem o Azure AD Connect que especifica uma base de dados ADSync existente. Para obter mais informações sobre esta funcionalidade, consulte o artigo [Utilize uma base de dados existente.](how-to-connect-install-existing-database.md)
* Para uma segurança melhorada, o Azure AD Connect passa a estar em incumprimento ao uso do TLS1.2 para ligar ao Azure AD para sincronização de diretórios. Anteriormente, o padrão era TLS1.0.
* Quando o Agente de Sincronização de Passwords AZure AD Connect Começa, tenta ligar-se ao ponto final conhecido da Azure AD para sincronização de palavras-passe. Após uma ligação bem sucedida, é redirecionado para um ponto final específico da região. Anteriormente, o Agente de Sincronização de Palavras-Passe caches o ponto final específico da região até que seja reiniciado. Agora, o agente limpa a cache e as retrações com o conhecido ponto final se encontrar problema de ligação com o ponto final específico da região. Esta alteração garante que a sincronização da palavra-passe pode falhar até um ponto final específico da região quando o ponto final específico da região em cache já não estiver disponível.
* Para sincronizar alterações de uma floresta do AD local, precisa de uma conta do AD DS. Pode (i) criar a conta DS AD e fornecer a sua credencial ao Azure AD Connect, ou (ii) fornecer as credenciais de um Administrador da Empresa e deixar a Azure AD Connect criar a conta AD DS para si. Anteriormente, (i) é a opção predefinida no assistente Azure AD Connect. Agora, (ii) é a opção padrão.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* Suporte adicional para Microsoft Azure Government Cloud e Microsoft Cloud Germany.

### <a name="ad-fs-management"></a>Gestão de AD FS
#### <a name="fixed-issues"></a>Problemas corrigidos
* O Initialize-ADSyncNGCKeysWriteBack cmdlet no módulo powerShell de preparação AD estava a aplicar incorretamente ACLs no recipiente de registo do dispositivo e, portanto, só herdaria as permissões existentes.  Isto foi atualizado para que a conta de serviço de sincronização tenha as permissões corretas.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* A tarefa Azure AD Connect Check ADFS Login foi atualizada de modo a verificar logins contra o Microsoft Online e não apenas a recuperação simbólica da ADFS.
* Ao configurar uma nova quinta ADFS utilizando o Azure AD Connect, a página que pede credenciais ADFS foi movida de modo a que isso ocorra agora antes de o utilizador ser solicitado a fornecer servidores ADFS e WAP.  Isto permite ao Azure AD Connect verificar se a conta especificada tem as permissões corretas.
* Durante a atualização do Azure AD Connect, deixaremos de falhar uma atualização se o ADFS Azure AD Trust não atualizar.  Se isso acontecer, o utilizador será apresentado como uma mensagem de aviso apropriada e deverá proceder à reposição da confiança através da tarefa adicional Azure AD Connect.

### <a name="seamless-single-sign-on"></a>Sign-On single sem emenda
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigiu um problema que fez com que o assistente Azure AD Connect retornasse um erro se tentasse ativar [o Sign-On Único Sem Emenda](how-to-connect-sso.md). A mensagem de erro é *"A configuração do Agente de Autenticação AD AD do Microsoft Azure falhou."* Este problema afeta os clientes existentes que tinham atualizado manualmente a versão de pré-visualização dos Agentes de Autenticação para [Autenticação Pass-through](how-to-connect-sso.md) com base nos passos descritos neste [artigo.](how-to-connect-pta-upgrade-preview-authentication-agents.md)


## <a name="115610"></a>1.1.561.0
Estado: 23 de julho de 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema  corrigido

* Corrigiu um problema que fez com que a regra de sincronização fora da caixa "out to AD - User ImmutableId" fosse removida:

  * O problema ocorre quando o Azure AD Connect é atualizado ou quando a configuração de sincronização de sincronização de *atualização* de tarefas no assistente Azure AD Connect é usada para atualizar a configuração de sincronização AZure AD Connect.

  * Esta regra de sincronização aplica-se aos clientes que ativaram o [ms-DS-ConsistencyGuid como recurso Source Anchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Esta funcionalidade foi introduzida na versão 1.1.524.0 e depois. Quando a regra de sincronização é removida, o Azure AD Connect já não pode preencher no local o atributo AD ms-DS-ConsistênciaGuid com o valor do atributo ObjectGuid. Não impede que novos utilizadores sejam adustados no Azure AD.

  * A correção garante que a regra de sincronização deixará de ser removida durante a atualização ou durante a alteração da configuração, desde que a funcionalidade esteja ativada. Para os clientes existentes que tenham sido afetados por este problema, a correção também garante que a regra de sincronização é adicionada de volta após o upgrade para esta versão do Azure AD Connect.

* Corrigiu um problema que faz com que as regras de sincronização fora da caixa tenham um valor de precedência inferior a 100:

  * Em geral, os valores de precedência 0 - 99 estão reservados para regras de sincronização personalizadas. Durante a atualização, os valores de precedência das regras de sincronização fora da caixa são atualizados para acomodar alterações de regras de sincronização. Devido a esta questão, as regras de sincronização fora da caixa podem ser atribuídas a um valor de precedência inferior a 100.

  * A correção impede que o problema ocorra durante a atualização. No entanto, não restaura os valores de precedência para os clientes existentes que tenham sido afetados pelo problema. No futuro, será fornecida uma correção separada para ajudar na restauração.

* Corrigiu um problema em que o [ecrã de filtragem De Domínio e UA](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente Azure AD Connect mostra *sincronizar todos os domínios e* opção OUs como selecionados, mesmo que a filtragem baseada em OU esteja ativada.

*   Corrigiu um problema que fez com que o ecrã de [partições do Diretório de Configuração](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) no Gestor de Serviços de Sincronização devolvesse um erro se o botão *'Refresh'* for clicado. A mensagem de erro é *"Um erro foi encontrado enquanto domínios refrescantes: Incapaz de lançar objeto do tipo 'System.Collections.ArrayList' para escrever 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* O erro ocorre quando o novo domínio AD foi adicionado a uma floresta AD existente e está a tentar atualizar o Azure AD Connect utilizando o botão Refresh.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* [A funcionalidade de atualização automática](how-to-connect-install-automatic-upgrade.md) foi expandida para apoiar os clientes com as seguintes configurações:
  * Ativou a função de reprodução do dispositivo.
  * Ativou a funcionalidade de reversão de grupo.
  * A instalação não é uma definição Express ou uma atualização DirSync.
  * Tem mais de 100.000 objetos no metaverso.
  * Estás a ligar-te a mais do que uma floresta. A configuração expressa só se liga a uma floresta.
  * A conta AD Connector já não é a conta MSOL_ predefinido.
  * O servidor está programado para estar no modo de preparação.
  * Ativou a função de descodão do utilizador.

  >[!NOTE]
  >A expansão de âmbito da funcionalidade de Atualização Automática afeta os clientes com Azure AD Connect build 1.1.105.0 e depois. Se não quiser que o seu servidor AZURE AD Connect seja automaticamente atualizado, tem de ser executado seguindo o cmdlet no seu servidor Azure AD `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` Connect: Para obter mais informações sobre como ativar/desativar a atualização automática, consulte o artigo [Azure AD Connect: Atualização automática](how-to-connect-install-automatic-upgrade.md).
## <a name="115580"></a>1.1.558.0
Estado: Não será libertado. As alterações nesta construção estão incluídas na versão 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema  corrigido

* Corrigiu um problema que fez com que a regra de sincronização fora da caixa "out to AD - User ImmutableId" fosse removida quando a configuração de filtragem baseada em OU for atualizada. Esta regra de sincronização é necessária para a função [ms-DS-ConsistencyGuid como fonte âncora](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Corrigiu um problema em que o [ecrã de filtragem De Domínio e UA](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente Azure AD Connect mostra *sincronizar todos os domínios e* opção OUs como selecionados, mesmo que a filtragem baseada em OU esteja ativada.

*   Corrigiu um problema que fez com que o ecrã de [partições do Diretório de Configuração](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) no Gestor de Serviços de Sincronização devolvesse um erro se o botão *'Refresh'* for clicado. A mensagem de erro é *"Um erro foi encontrado enquanto domínios refrescantes: Incapaz de lançar objeto do tipo 'System.Collections.ArrayList' para escrever 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* O erro ocorre quando o novo domínio AD foi adicionado a uma floresta AD existente e está a tentar atualizar o Azure AD Connect utilizando o botão Refresh.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* [A funcionalidade de atualização automática](how-to-connect-install-automatic-upgrade.md) foi expandida para apoiar os clientes com as seguintes configurações:
  * Ativou a função de reprodução do dispositivo.
  * Ativou a funcionalidade de reversão de grupo.
  * A instalação não é uma definição Express ou uma atualização DirSync.
  * Tem mais de 100.000 objetos no metaverso.
  * Estás a ligar-te a mais do que uma floresta. A configuração expressa só se liga a uma floresta.
  * A conta AD Connector já não é a conta MSOL_ predefinido.
  * O servidor está programado para estar no modo de preparação.
  * Ativou a função de descodão do utilizador.

  >[!NOTE]
  >A expansão de âmbito da funcionalidade de Atualização Automática afeta os clientes com Azure AD Connect build 1.1.105.0 e depois. Se não quiser que o seu servidor AZURE AD Connect seja automaticamente atualizado, tem de ser executado seguindo o cmdlet no seu servidor Azure AD `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` Connect: Para obter mais informações sobre como ativar/desativar a atualização automática, consulte o artigo [Azure AD Connect: Atualização automática](how-to-connect-install-automatic-upgrade.md).
## <a name="115570"></a>1.1.557.0
Estado: julho 2017

>[!NOTE]
>Esta construção não está disponível para os clientes através da funcionalidade Azure AD Connect Auto Upgrade.
### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema  corrigido
* Corrigiu um problema com o cmdlet Initialize-ADSyncDomainJoinedComputerSync que fez com que o domínio verificado configurado no objeto do ponto de ligação existente fosse alterado mesmo que ainda seja um domínio válido. Este problema ocorre quando o seu inquilino Azure AD tem mais de um domínio verificado que pode ser usado para configurar o ponto de ligação de serviço.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* O writeback de palavra-passe já está disponível para pré-visualização com a cloud do Microsoft Azure Government e microsoft Cloud Germany. Para obter mais informações sobre o suporte Azure AD Connect para as diferentes instâncias de serviço, consulte o artigo [Azure AD Connect: Considerações especiais para exemplos](reference-connect-instances.md).

* O Initialize-ADSyncDomainJoinedComputerSync cmdlet tem agora um novo parâmetro opcional chamado AzureADDomain. Este parâmetro permite especificar qual o domínio verificado a utilizar para configurar o ponto de ligação de serviço.

### <a name="pass-through-authentication"></a>Autenticação pass-through

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* O nome do agente necessário para a autenticação pass-through foi alterado de *Conector de Aplicação AD AD do Microsoft Azure* para *o Microsoft Azure AD Connect Authentication Agent*.

* Ativar a autenticação pass-through já não permite a sincronização de hash de palavra-passe por predefinição.


## <a name="115530"></a>1.1.553.0
Estado: junho de 2017

> [!IMPORTANT]
> Há alterações de regras de esquema e sincronização introduzidas nesta construção. O Serviço de Sincronização AD Connect AD Ad ativará os passos de importação completa e sincronização completa após a atualização. Os detalhes das alterações são descritos abaixo. Para adiar temporariamente os passos de importação completa e sincronização completa após a atualização, consulte o artigo [Como adiar a sincronização completa após a atualização](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>
### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Problema conhecido
* Existe um problema que afeta os clientes que estão a utilizar [a filtragem baseada em U](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) com a sincronização Azure AD Connect. Quando navega para a [página de filtragem de domínio e uA](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente Azure AD Connect, espera-se o seguinte comportamento:
  * Se a filtragem baseada em OU estiver ativada, seleciona-se a opção Sync selecionada e a opção **OUs.**
  * Caso contrário, a opção **Sync todos os domínios e OUs** é selecionada.

O problema que se coloca é que a **opção Sync todos os domínios e OUs** é sempre selecionada quando executam o Assistente.  Isto ocorre mesmo que a filtragem baseada em U tenha sido previamente configurada. Antes de guardar quaisquer alterações de configuração AD Connect Azure, certifique-se de que os **domínios e opção OUs selecionados para sincronização estão selecionados** e confirme que todas as OUs que precisam de sincronizar estão novamente ativadas. Caso contrário, a filtragem baseada em U será desativada.

#### <a name="fixed-issues"></a>Problemas corrigidos

* Corrigiu um problema com a writeback password que permite a um Administrador AD Azure redefinir a palavra-passe de uma conta de utilizador privilegiada em AD no local. O problema ocorre quando o Azure AD Connect recebe a permissão de Palavra-passe reset sobre a conta privilegiada. O problema é abordado nesta versão do Azure AD Connect ao não permitir que um Administrador AD da Azure reponha a palavra-passe de uma conta de utilizador privilegiada arbitrária no local, a menos que o administrador seja o proprietário dessa conta. Para mais informações, consulte o [Aviso de Segurança 4033453](/security-updates/SecurityAdvisories/2017/4033453).

* Corrigi um problema relacionado com o [ms-DS-ConsistencyGuid como](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) fonte âncora, onde o Azure AD Connect não é o atributo AD ms-DS-ConsistencyGuid. O problema ocorre quando existem múltiplas florestas de AD no local adicionadas ao Azure AD Connect e as identidades do *Utilizador existem em várias direções.* Quando tal configuração é utilizada, as regras de sincronização resultantes não povoam o atributo origemAnchorBinary no Metaverse. O atributo SourceAnchorBinary é usado como o atributo de origem para o atributo MS-DS-ConsistencyGuid. Como resultado, não ocorre a redução do atributo MS-DSConsistencyGuid. Para corrigir o problema, foram atualizadas as seguintes regras de sincronização para garantir que o atributo origemAnchorBinary no Metaverse é sempre povoado:
  * In from AD - InetOrgPerson AccountEnabled.xml
  * In from AD - InetOrgPerson Common.xml
  * In from AD - User AccountEnabled.xml
  * In from AD - User Common.xml
  * In from AD - User Join SOAInAAD.xml

* Anteriormente, mesmo que a função [de âncora ms-DS-Consistência como Âncora de Origem](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) não esteja ativada, a regra de sincronização "out to AD – User ImmutableId" ainda é adicionada ao Azure AD Connect. O efeito é benigno e não faz com que ocorra a gravação do atributo Ms-DS-ConsistencyGuid. Para evitar confusões, foi adicionada lógica para garantir que a regra de sincronização só é adicionada quando a funcionalidade está ativada.

* Corrigiu um problema que fez com que a sincronização do hash da palavra-passe falhasse com o evento de erro 611. Este problema ocorre depois de um ou mais controladores de domínio terem sido removidos do AD no local. No final de cada ciclo de sincronização de palavras-passe, o cookie de sincronização emitido pela AD no local contém IDs de invocação dos controladores de domínio removidos com o valor USN (Número de sequência de atualização) de 0. O Gestor de Sincronização de Palavras-Passe não consegue persistir no cookie de sincronização contendo o valor USN de 0 e falha com o evento de erro 611. Durante o próximo ciclo de sincronização, o Gestor de Sincronização de Palavras-Passe reutiliza o último cookie de sincronização persistido que não contém o valor USN de 0. Isto faz com que as mesmas alterações de senha sejam ressincronizadas. Com esta correção, o Gestor de Sincronização de Palavras-Passe persiste corretamente no cookie de sincronização.

* Anteriormente, mesmo que a Atualização Automática tenha sido desativada utilizando o Set-ADSyncAutoUpgrade cmdlet, o processo de Atualização Automática continua a verificar periodicamente a atualização e conta com o instalador descarregado para honrar a desativação. Com esta correção, o processo de Atualização Automática deixou de verificar periodicamente a atualização. A correção é aplicada automaticamente quando o instalador de upgrade para esta versão Azure AD Connect é executado uma vez.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Anteriormente, a funcionalidade [ms-DS-ConsistencyGuid como Âncora de Origem](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) estava disponível apenas para novas implementações. Agora, está disponível para implementações existentes. Mais especificamente:
  * Para aceder à funcionalidade, inicie o assistente Azure AD Connect e escolha a opção *'Ancorar fonte de* actualização'.
  * Esta opção só é visível para implementações existentes que estão a usar o objectGuid como atributo sourceAnchor.
  * Ao configurar a opção, o assistente valida o estado do atributo MS-DS-ConsistencyGuid no seu Ative Directory no local. Se o atributo não estiver configurado em nenhum objeto do utilizador no diretório, o assistente utiliza o ms-DS-ConsistencyGuid como o atributo SourceAnchor. Se o atributo estiver configurado num ou mais objetos de utilizador no diretório, o assistente conclui que o atributo está a ser utilizado por outras aplicações e não é adequado como atributo SourceAnchor e não permite que a alteração da Âncora fonte prossiga. Se tiver a certeza de que o atributo não é utilizado pelas aplicações existentes, tem de contactar o Support para obter informações sobre como suprimir o erro.

* Específico para **o utilizador Atribuir** atributos certificados em objetos do Dispositivo, o Azure AD Connect procura agora os valores dos certificados necessários para ligar [dispositivos ligados ao domínio ao AZure AD para a experiência do Windows 10](../devices/hybrid-azuread-join-plan.md) e filtra o resto antes de sincronizar com o AZure AD. Para ativar este comportamento, foi atualizada a regra de sincronização fora de caixa "out to AAD - Device Join SOAInAD".

* O Azure AD Connect suporta agora a gravação do atributo Exchange Online **cloudPublicDeegates** atribuídos a anúncios **públicos** AD no local. Isto permite o cenário em que uma caixa de correio Exchange Online pode ser concedida a SendOnBehalfTo direitos para utilizadores com caixa de correio exchange no local. Para suportar esta funcionalidade, foi adicionada uma nova regra de sincronização fora de caixa "out to AD – User Exchange Hybrid PublicDegates writeback". Esta regra de sincronização só é adicionada ao Azure AD Connect quando a funcionalidade Exchange Hybrid estiver ativada.

* O Azure AD Connect suporta agora sincronizar o atributo **altRecipient** da Azure AD. Para apoiar esta alteração, foram atualizadas as seguintes regras de sincronização fora de caixa para incluir o fluxo de atributos necessário:
  * In from AD – User Exchange
  * Out to AAD – User ExchangeOnline

* O atributo **cloudSOAExchMailbox** no Metaverse indica se um determinado utilizador tem ou não caixa de correio Exchange Online. A sua definição foi atualizada para incluir outros Exchange Online RecipientDisplayTypes como tais caixas de correio de equipamento e sala de conferências. Para permitir esta alteração, a definição do atributo cloudSOAExchMailbox, que é encontrado sob a regra de sincronização fora da caixa "In from AAD – User Exchange Hybrid", foi atualizada a partir de:

    ```
    CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
    ```

    ... a seguir:

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

* Adicionou o seguinte conjunto de funções compatíveis com X509Certificate2 para a criação de expressões de regras de sincronização para lidar com os valores dos certificados no atributo userCertificate:
  * CertSubject 
  * CertIssuer
  * CertKeyAlgorithm
  * CertsubjectNamedn
  * CertIssuerOid
  * CertNameInfo
  * CertSubjectNameOid
  * CertIssuerDN
  * IsCert
  * Nome CertFriendly
  * CertThumbprint
  * CertExtensionOids
  * CertFormat
  * CertNotAfter
  * CertPublicKeyOid 
  * Número de Certificadoserial
  * CertNotBefore
  * CertPublicKeyParametersOid
  * CertVersion
  * CertSignatureAlgorithmOid
  * Selecione
  * CertKeyAlgorithmParams
  * CertHashString
  * Onde
  * Com

* Foram introduzidas alterações de esquemas que permitem aos clientes criar regras de sincronização personalizadas para fluir sAMAccountName, domainNetBios e domainFQDN para objetos de grupo, bem como nome distinto para objetos do utilizador:

  * Foram adicionados seguintes atributos ao esquema de MV:
    * Grupo: Nome da conta
    * Grupo: domainNetBios
    * Grupo: domainFQDN
    * Pessoa: nome distinto

  * Foram adicionados seguintes atributos ao esquema do conector AD Ad Azure:
    * Grupo: OnPremisesSamAccountName
    * Grupo: NetBiosName
    * Grupo: DnsDomainName
    * Utilizador: OnPremisesDistinguishedName

* O script ADSyncDomainJoinedComputerSync cmdlet tem agora um novo parâmetro opcional chamado AzureEnvironment. O parâmetro é utilizado para especificar em que região está hospedado o inquilino correspondente do Azure Ative Directory. Valores válidos incluem:
  * AzureCloud (padrão)
  * AzureChinaCloud
  * AzureGermanyCloud
  * Governo dos EUA

* Editor de Regras de Sincronização Atualizado para usar o Join (em vez de Provision) como o valor padrão do tipo de ligação durante a criação de regras de sincronização.

### <a name="ad-fs-management"></a>Gestão de FS AD

#### <a name="issues-fixed"></a>Questões fixas

* Os URLs seguintes são novos WS-Federation pontos finais introduzidos pela Azure AD para melhorar a resiliência contra a interrupção da autenticação e serão adicionados à configuração de confiança da parte que responde:
  * https: \/ /ests.login.microsoftonline.com/login.srf
  * https: \/ /stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf

* Corrigiu um problema que fez com que a AD FS gerasse valor de reclamação incorreto para o EmiterID. O problema ocorre se existirem vários domínios verificados no inquilino AD Azure e o sufixo de domínio do atributo userPrincipalName utilizado para gerar a alegação emitente é de pelo menos 3 níveis de profundidade (por exemplo, johndoe@us.contoso.com ). A questão resolve-se atualizando o regex utilizado pelas regras de reclamação.

#### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
* Anteriormente, a função de Gestão de Certificados ADFS fornecida pelo Azure AD Connect só pode ser utilizada com explorações ADFS geridas através do Azure AD Connect. Agora, pode utilizar a funcionalidade com as quintas ADFS que não são geridas utilizando o Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Lançado: maio 2017

> [!IMPORTANT]
> Há alterações de regras de esquema e sincronização introduzidas nesta construção. O Serviço de Sincronização AZURE AD Connect irá ativar os passos de Importação Completa e Sincronização Completa após a atualização. Os detalhes das alterações são descritos abaixo.
>
>
**Questões fixas:**

Sincronização do Azure AD Connect

* Corrigiu um problema que faz com que o upgrade automático ocorra no servidor Azure AD Connect mesmo que o cliente tenha desativado a funcionalidade utilizando o Set-ADSyncAutoUpgrade cmdlet. Com esta correção, o processo de Atualização Automática no servidor ainda verifica a atualização periodicamente, mas o instalador descarregado honra a configuração de Upgrade Automático.
* Durante a atualização no local da DirSync, o Azure AD Connect cria uma conta de serviço AZure AD para ser utilizada pelo conector AD AD Azure para sincronização com Azure AD. Após a criação da conta, o Azure AD Connect autentica-se com a Azure AD utilizando a conta. Por vezes, a autenticação falha devido a problemas transitórios, o que, por sua vez, faz com que a atualização da DirSync falhe com erro "Ocorreu um erro na *execução da tarefa Configure AAD Sync: AADSTS50034: Para assinar esta aplicação, a conta deve ser adicionada ao diretório xxx.onmicrosoft.com."* Para melhorar a resiliência da atualização dirSync, o Azure AD Connect agora retriries o passo de autenticação.
* Houve um problema com a construção 443 que faz com que a atualização do DirSync no local tenha sucesso, mas não são criados perfis necessários para a sincronização do diretório. A lógica de cura está incluída nesta construção do Azure AD Connect. Quando o cliente atualiza para esta construção, o Azure AD Connect deteta perfis de execução em falta e cria-os.
* Corrigi um problema que faz com que o processo de sincronização da Palavra-Passe não comece com o ID do Evento 6900 e tenha *sido adicionado um erro "Já foi adicionado um item com a mesma chave".* Este problema ocorre se atualizar a configuração de filtragem da UA para incluir a partição de configuração AD. Para corrigir este problema, o processo de sincronização de palavras-passe agora sincroniza as alterações de palavra-passe apenas a partir de partições de domínio de AD. As divisórias não-domínio, tais como a partição de configuração, são ignoradas.
* Durante a instalação do Expresso, o Azure AD Connect cria uma conta AD DS no local para ser utilizada pelo conector AD para comunicar com a AD no local. Anteriormente, a conta é criada com a bandeira PASSWD_NOTREQD definida no atributo Controlo de Conta de Utilizador e é definida uma senha aleatória na conta. Agora, o Azure AD Connect remove explicitamente a bandeira PASSWD_NOTREQD depois de a palavra-passe ser definida na conta.
* Corrigiu um problema que faz com que a atualização da DirSync falhe com o erro *"ocorreu um impasse no servidor sql que tenta adquirir um bloqueio de aplicação"* quando o atributo mailNickname é encontrado no esquema de AD no local, mas não está limitado à classe de objeto de utilizador AD.
* Corrigiu um problema que faz com que a função de desativação do dispositivo seja automaticamente desativada quando um administrador está a atualizar a configuração de sincronização AZure AD Connect utilizando o assistente Azure AD Connect. Este problema é causado pelo assistente que executa uma verificação prévia para a configuração de writeback do dispositivo existente no AD no local e a verificação falha. A correção é saltar a verificação se o recuo do dispositivo já estiver ativado anteriormente.
* Para configurar a filtragem da OU, pode utilizar o assistente Azure AD Connect ou o Gestor de Serviço de Sincronização. Anteriormente, se utilizar o assistente Azure AD Connect para configurar a filtragem OU, as novas OUs criadas posteriormente estão incluídas para sincronização de diretórios. Se não quiser que sejam incluídas novas OUs, tem de configurar a filtragem da OU utilizando o Gestor de Serviços de Sincronização. Agora, pode obter o mesmo comportamento usando o assistente Azure AD Connect.
* Corrigiu um problema que faz com que os procedimentos armazenados exigidos pelo Azure AD Connect sejam criados sob o esquema da administração de instalação, em vez de sob o esquema dbo.
* Corrigiu um problema que faz com que o atributo TrackingId devolvido pela Azure AD seja omitido nos Registos de Eventos do Servidor de Ligação AD Azure. O problema ocorre se o Azure AD Connect receber uma mensagem de reorientação do Azure AD e o Azure AD Connect não conseguir ligar-se ao ponto final fornecido. O TrackingId é utilizado pelos Engenheiros de Suporte para se correlacionar com os registos laterais de serviço durante a resolução de problemas.
* Quando o Azure AD Connect recebe um erro largeObject do Azure AD, o Azure AD Connect gera um evento com o EventID 6941 e a mensagem *"O objeto provisionado é demasiado grande. Corte o número de valores de atributos neste objeto."* Ao mesmo tempo, o Azure AD Connect também gera um evento enganoso com o EventID 6900 e a mensagem *"Microsoft.Online.Coexistence.ProvisionRetryException: Incapaz de comunicar com o serviço de Diretório Ativo Windows Azure.".* Para minimizar a confusão, o Azure AD Connect já não gera este último evento quando o erro do LargeObject é recebido.
* Corrigiu um problema que faz com que o Gestor de Serviços de Sincronização não responda ao tentar atualizar a configuração do conector LDAP genérico.

**Novas funcionalidades/melhorias:**

Sincronização do Azure AD Connect
* Alterações de Regras de Sincronização - Foram implementadas as seguintes alterações de regras de sincronização:
  * Regra de sincronização padrão atualizada definida para não exportar atributos **Certificação** e **utilizadorSMIMECertificate** se os atributos tiverem mais de 15 valores.
  * Os atributos **ADid** e **msExchBypassModerationLink** estão agora incluídos no conjunto de regras de sincronização padrão.
  * A **foto** de atributo AD foi removida do conjunto de regras de sincronização padrão.
  * Adicionado **DataLocation preferido** ao esquema de metaverso e esquema de conector AD Azure. Os clientes que pretendam atualizar qualquer um dos atributos em Azure AD podem implementar regras de sincronização personalizadas para o fazer. 
  * Adicione **o userType** ao esquema metaverso e ao esquema do conector AD Azure. Os clientes que pretendam atualizar qualquer um dos atributos em Azure AD podem implementar regras de sincronização personalizadas para o fazer.

* O Azure AD Connect ativa agora automaticamente a utilização do atributo ConsistencyGuid como atributo Âncora de Origem para objetos AD no local. Além disso, a Azure AD Connect povoa o atributo ConsistencyGuid com o valor de atributo objectGuid se estiver vazio. Esta funcionalidade aplica-se apenas a novas implementações. Para saber mais sobre esta funcionalidade, consulte a secção [de artigos Azure AD Connect: Design concepts - Using ms-DS-ConsistencyGuid as sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* Foi adicionado um novo cmdlet de resolução de problemas Invoke-ADSyncDiagnostics para ajudar a diagnosticar problemas relacionados com a sincronização de passwords Hash. Para obter informações sobre a utilização do cmdlet, consulte o artigo [Sincronização de hash de palavra-passe de resolução de problemas com sincronização Azure AD Connect](tshoot-connect-password-hash-synchronization.md).
* O Azure AD Connect suporta agora a sincronização Mail-Enabled objetos de pasta pública, desde o AD no local até ao Azure AD. Pode ativar a funcionalidade utilizando o assistente Azure AD Connect em Funcionalidades Opcionais. Para saber mais sobre esta funcionalidade, consulte o suporte do Office [365 Directy Edge Blocking para as pastas públicas ativadas no local.](https://techcommunity.microsoft.com/t5/exchange/office-365-directory-based-edge-blocking-support-for-on-premises/m-p/74218)
* O Azure AD Connect requer uma conta DS AD para sincronizar a partir de AD no local. Anteriormente, se instalou o Azure AD Connect utilizando o modo Express, poderia fornecer as credenciais de uma conta Enterprise Admin e o Azure AD Connect criaria a conta DS AD necessária. No entanto, para uma instalação personalizada e adição de florestas a uma implantação existente, foi-lhe exigido que fornecesse a conta DS AD. Agora, também tem a opção de fornecer as credenciais de uma conta Enterprise Admin durante uma instalação personalizada e deixar o Azure AD Connect criar a conta DS AD necessária.
* O Azure AD Connect suporta agora o SQL AOA. Tem de ativar o SQL AOA antes de instalar o Azure AD Connect. Durante a instalação, o Azure AD Connect deteta se a instância SQL fornecida está ativada para o SQL AOA ou não. Se o SQL AOA estiver ativado, o Azure AD Connect calcula ainda se o SQL AOA está configurado para utilizar uma replicação sincronizada ou uma replicação assíncrona. Ao configurar o Ouvinte do Grupo disponibilidade, recomenda-se que ele ajuste a propriedade RegisterAllProvidersIP para 0. Esta recomendação é porque o Azure AD Connect utiliza atualmente o SQL Native Client para se conectar ao SQL e ao SQL Native Client não suporta o uso de propriedade MultiSubNetFailover.
* Se estiver a utilizar o LocalDB como base de dados para o seu servidor AZure AD Connect e tiver atingido o seu limite de tamanho de 10 GB, o Serviço de Sincronização já não arranca. Anteriormente, é necessário executar a operação ShrinkDatabase no LocalDB para recuperar espaço DB suficiente para o Serviço de Sincronização iniciar. Depois disso, pode utilizar o Gestor de Serviços de Sincronização para eliminar o histórico de execução para recuperar mais espaço DB. Agora, você pode usar Start-ADSyncPurgeRunHistory cmdlet para limpar os dados de histórico de execução do LocalDB para recuperar o espaço DB. Além disso, este cmdlet suporta um modo offline (especificando o parâmetro -offline) que pode ser utilizado quando o Serviço de Sincronização não está em funcionamento. Nota: O modo offline só pode ser utilizado se o Serviço de Sincronização não estiver em funcionamento e a base de dados utilizada for LocalDB.
* Para reduzir a quantidade de espaço de armazenamento necessário, o Azure AD Connect comprime agora detalhes de erro sincronizados antes de os armazenar nas bases de dados LocalDB/SQL. Ao atualizar a partir de uma versão mais antiga do Azure AD Connect para esta versão, o Azure AD Connect executa uma compressão única nos detalhes de erro sincronizados existentes.
* Anteriormente, após atualizar a configuração de filtragem da U, deve executar manualmente a importação completa para garantir que os objetos existentes estão corretamente incluídos/excluídos da sincronização do diretório. Agora, o Azure AD Connect ativa automaticamente a importação total durante o próximo ciclo de sincronização. Além disso, a importação completa só é aplicada aos conectores AD afetados pela atualização. Nota: esta melhoria é aplicável às atualizações de filtragem da UA efetuadas apenas utilizando o assistente Azure AD Connect. Não é aplicável à atualização de filtragem da UO feita utilizando o Gestor de Serviço de Sincronização.
* Anteriormente, a filtragem baseada em grupo suporta apenas utilizadores, grupos e objetos de contacto. Agora, a filtragem baseada em grupo também suporta objetos de computador.
* Anteriormente, pode eliminar os dados do Connector Space sem desativar o programador de sincronização Azure AD Connect. Agora, o Gestor de Serviço de Sincronização bloqueia a eliminação de dados do Espaço do Conector se detetar que o programador está ativado. Além disso, é devolvido um aviso para informar os clientes sobre a possível perda de dados se os dados do espaço do Conector forem eliminados.
* Anteriormente, deve desativar a transcrição PowerShell para o assistente Azure AD Connect funcionar corretamente. Esta questão está parcialmente resolvida. Pode ativar a transcrição powerShell se estiver a utilizar o assistente Azure AD Connect para gerir a configuração de sincronização. Tem de desativar a transcrição powerShell se estiver a utilizar o assistente Azure AD Connect para gerir a configuração ADFS.



## <a name="114860"></a>1.1.486.0
Lançado: abril 2017

**Questões fixas:**
* Corrigi o problema em que o Azure AD Connect não será instalado com sucesso na versão localizada do Windows Server.

## <a name="114840"></a>1.1.484.0
Lançado: abril 2017

**Questões conhecidas:**

* Esta versão do Azure AD Connect não será instalada com sucesso se as seguintes condições forem todas verdadeiras:
   1. Está a realizar um upgrade no local da DirSync ou uma nova instalação do Azure AD Connect.
   2. Está a utilizar uma versão localizada do Windows Server onde o nome do grupo de administrador incorporado no servidor não é "Administradores".
   3. Está a utilizar o SQL Server 2012 Express LocalDB padrão instalado com o Azure AD Connect em vez de fornecer o seu próprio SQL completo.

**Questões fixas:**

Sincronização do Azure AD Connect
* Corrigiu um problema em que o programador de sincronização salta todo o passo de sincronização se um ou mais conectores estiverem a perder o perfil de execução para esse passo de sincronização. Por exemplo, adicionou manualmente um conector utilizando o Gestor de Serviços de Sincronização sem criar um perfil de execução da Delta Import para o mesmo. Esta correção garante que o programador de sincronização continua a executar a Delta Import para outros conectores.
* Corrigiu um problema em que o Serviço de Sincronização para imediatamente de processar um perfil de execução quando se encontra um problema com um dos passos de execução. Esta correção garante que o Serviço de Sincronização salta esse passo de execução e continua a processar o resto. Por exemplo, tem um perfil de execução Delta Import para o seu conector AD com vários passos de execução (um para cada domínio AD no local). O Serviço de Sincronização funcionará a Delta Import com os outros domínios de AD, mesmo que um deles tenha problemas de conectividade de rede.
* Corrigiu um problema que faz com que a atualização do Conector AD Azure seja ignorada durante a Atualização Automática.
* Corrigiu um problema que faz com que o Azure AD Connect determine incorretamente se o servidor é um controlador de domínio durante a configuração, o que, por sua vez, faz com que a atualização da DirSync falhe.
* Corrigiu um problema que faz com que a atualização do DirSync no local não crie qualquer perfil de execução para o Conector AD Azure.
* Corrigiu um problema em que a interface de utilizador do Gestor de Serviço de Sincronização não responde ao tentar configurar o Conector LDAP Genérico.

Gestão de FS AD
* Corrigiu um problema em que o assistente Azure AD Connect falha se o nó primário AD FS tiver sido movido para outro servidor.

Desktop SSO
* Corrigiu um problema no assistente Azure AD Connect onde o ecrã Sign-In não permite ativar a funcionalidade Desktop SSO se escolheu a Sincronização de Palavras-Passe como opção Sign-In durante a nova instalação.

**Novas funcionalidades/melhorias:**

Sincronização do Azure AD Connect
* O Azure AD Connect Sync suporta agora a utilização da Conta de Serviço Virtual, conta de serviço gerido e conta de serviço gerido pelo grupo como conta de serviço. Isto aplica-se apenas à nova instalação do Azure AD Connect. Ao instalar o Azure AD Connect:
    * Por predefinição, o assistente Azure AD Connect criará uma Conta de Serviço Virtual e utiliza-a como conta de serviço.
    * Se estiver a instalar num controlador de domínio, o Azure AD Connect recua para o comportamento anterior, onde criará uma conta de utilizador de domínio e a utiliza como conta de serviço.
    * Pode anular o comportamento predefinido, fornecendo um dos seguintes:
      * Uma conta de serviço gerida pelo grupo
      * Uma conta de serviço gerido
      * Uma conta de utilizador de domínio
      * Uma conta de utilizador local
* Anteriormente, se atualizar para uma nova construção do Azure AD Connect contendo atualizações de conectores ou alterações de regra sincronizadas, o Azure AD Connect irá desencadear um ciclo de sincronização completo. Agora, o Azure AD Connect ativa seletivamente o passo de Importação Completa apenas para conectores com atualização e passo de sincronização completa apenas para conectores com alterações de regras de sincronização.
* Anteriormente, o limiar de eliminação das exportações aplica-se apenas às exportações que são desencadeadas através do programador sincronizado. Agora, a funcionalidade é estendida para incluir exportações ativadas manualmente pelo cliente usando o Gestor de Serviço de Sincronização.
* No seu inquilino Azure AD, existe uma configuração de serviço que indica se a função de Sincronização de Palavras-Passe está ativada para o seu inquilino ou não. Anteriormente, é fácil que a configuração do serviço seja configurada incorretamente pelo Azure AD Connect quando tem um servidor ativo e de paragem. Agora, o Azure AD Connect tentará manter a configuração de serviço consistente apenas com o seu servidor AZURE AD Connect ativo.
* O assistente Azure AD Connect deteta e devolve um aviso se o AD no local não tiver o Caixote do Reciclagem AD ativado.
* Anteriormente, exporte para Azure AD vezes e falha se o tamanho combinado dos objetos no lote exceder determinado limiar. Agora, o Serviço de Sincronização voltará a tentar reencaminhá-lo para reencaminhar os objetos em lotes separados e menores se o problema for encontrado.
* A aplicação de Gestão de Chaves de Serviço de Sincronização foi removida do Menu Inicial do Windows. A gestão da chave de encriptação continuará a ser suportada através da interface da linha de comando utilizando miiskmu.exe. Para obter informações sobre a chave de encriptação, consulte o artigo [Abandonando a chave de encriptação Azure AD Connect Sync](./how-to-connect-sync-change-serviceacct-pass.md#abandoning-the-adsync-service-account-encryption-key).
* Anteriormente, se alterar a palavra-passe do serviço de sincronização Azure AD Connect, o Serviço de Sincronização não poderá iniciar-se corretamente até ter abandonado a chave de encriptação e reiniciado a palavra-passe do serviço de sincronização Azure AD Connect. Agora, este processo já não é necessário.

Desktop SSO

* O assistente Azure AD Connect já não necessita da porta 9090 para ser aberta na rede ao configurar a Autenticação Pass-through e o Desktop SSO. Só é necessária a porta 443.

## <a name="114430"></a>1.1.443.0
Lançado: março 2017

**Questões fixas:**

Sincronização do Azure AD Connect
* Corrigiu um problema que faz com que o assistente Azure AD Connect falhe se o nome de exibição do Conector AD Azure não contiver o domínio inicial onmicrosoft.com atribuído ao inquilino Azure AD.
* Corrigiu um problema que faz com que o assistente Azure AD Connect falhe ao fazer a ligação à base de dados SQL quando a palavra-passe da Conta de Serviço de Sincronização contém caracteres especiais como apóstrofo, cólon e espaço.
* Corrigiu um problema que causa o erro "A imagem tem uma âncora diferente da imagem" a ocorrer num servidor AZURE AD Connect no modo de fase, depois de ter excluído temporariamente um objeto AD no local da sincronização e depois o ter incluído novamente para sincronização.
* Corrigiu um problema que faz com que o erro "O objeto localizado pelo DN seja um fantasma" ocorra num servidor AZURE AD Connect no modo de faseamento, depois de ter excluído temporariamente um objeto AD no local da sincronização e depois o ter incluído novamente para sincronização.

Gestão de FS AD
* Corrigi um problema em que o assistente Azure AD Connect não atualiza a configuração AD FS e define as alegações certas na confiança da parte que conta depois de configurar o ID do Login Alternativo.
* Corrigiu um problema em que o assistente Azure AD Connect não consegue manusear corretamente servidores AD FS cujas contas de serviço estão configuradas utilizando o formato userPrincipalName em vez do formato sAMAccountName.

Autenticação pass-through
* Corrigiu um problema que faz com que o assistente Azure AD Connect falhe se for selecionado o Passe Através da Autenticação, mas o registo do conector falha.
* Corrigiu um problema que faz com que o assistente Azure AD Connect contorne as verificações de validação no método de entrada selecionada quando a funcionalidade SSO do Desktop estiver ativada.

Repor palavra-passe
* Corrigiu um problema que pode fazer com que o servidor Azure AD Connect Azure não tente voltar a ligar se a ligação tiver sido morta por uma firewall ou procuração.

**Novas funcionalidades/melhorias:**

Sincronização do Azure AD Connect
* Get-ADSyncScheduler cmdlet agora devolve uma nova propriedade booleana chamada SyncCycleInProgress. Se o valor devolvido for verdadeiro, significa que há um ciclo de sincronização programado em curso.
* A pasta de destino para armazenar registos de instalação e configuração do Azure AD Connect foi transferida de %localappdata%\AADConnect para %programdata%\AADConnect para melhorar a acessibilidade aos ficheiros de registo.

Gestão de FS AD
* Suporte adicional para a atualização do Certificado AD FS Farm TLS/SSL.
* Apoio adicional para a gestão da AD FS 2016.
* Pode agora especificar o gMSA existente (Conta de Serviço Gerido pelo Grupo) durante a instalação AD FS.
* Agora pode configurar SHA-256 como o algoritmo de hash de assinatura para Azure AD confiando na confiança do partido.

Repor palavra-passe
* Introduziu melhorias para permitir que o produto funcione em ambientes com regras de firewall mais rigorosas.
* Melhor fiabilidade da ligação à Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Lançado: dezembro 2016

**Emissão fixa:**

* Corrigiu a questão em que falta a regra de reclamação em causa para os Serviços da Federação de Diretórios Ativos (AD FS) neste mesmo.

>[!NOTE]
>Esta construção não está disponível para os clientes através da funcionalidade Azure AD Connect Auto Upgrade.
## <a name="113710"></a>1.1.371.0
Lançado: dezembro 2016

**Edição conhecida:**

* A regra de reivindicação emitente para a AD FS está faltando nesta construção. A regra de reclamação emitente é necessária se estiver a federar vários domínios com o Azure Ative Directory (Azure AD). Se estiver a utilizar o Azure AD Connect para gerir a sua implementação AD FS no local, o upgrade para esta construção remove a regra de reclamação emitente existente da configuração AD FS. Pode contornar o problema adicionando a regra de reclamação emitente após a instalação/atualização. Para obter mais informações sobre a adição da regra de reclamação emitente, consulte este artigo sobre suporte de [domínio múltiplo para federar com Azure AD](how-to-connect-install-multiple-domains.md).

**Emissão fixa:**

* Se a porta 9090 não for aberta para a ligação de saída, a instalação ou atualização Azure AD Connect falha.

>[!NOTE]
>Esta construção não está disponível para os clientes através da funcionalidade Azure AD Connect Auto Upgrade.
## <a name="113700"></a>1.1.370.0
Lançado: dezembro 2016

**Questões conhecidas:**

* A regra de reivindicação emitente para a AD FS está faltando nesta construção. A regra de reclamação emitente é necessária se estiver a federar vários domínios com Azure AD. Se estiver a utilizar o Azure AD Connect para gerir a sua implementação AD FS no local, o upgrade para esta construção remove a regra de reclamação emitente existente da configuração AD FS. Pode contornar o problema adicionando a regra de reclamação emitente após a instalação/atualização. Para obter detalhes sobre a adição da regra de reclamação emitida, consulte este artigo sobre [suporte de domínio múltiplo para federar com Azure AD](how-to-connect-install-multiple-domains.md).
* A porta 9090 deve estar aberta para a instalação completa.

**Novas funcionalidades:**

* Autenticação pass-through (Pré-visualização).

>[!NOTE]
>Esta construção não está disponível para os clientes através da funcionalidade Azure AD Connect Auto Upgrade.
## <a name="113430"></a>1.1.343.0
Lançado: novembro 2016

**Edição conhecida:**

* A regra de reivindicação emitente para a AD FS está faltando nesta construção. A regra de reclamação emitente é necessária se estiver a federar vários domínios com Azure AD. Se estiver a utilizar o Azure AD Connect para gerir a sua implementação AD FS no local, o upgrade para esta construção remove a regra de reclamação emitente existente da configuração AD FS. Pode contornar o problema adicionando a regra de reclamação emitente após a instalação/atualização. Para obter detalhes sobre a adição da regra de reclamação emitida, consulte este artigo sobre [suporte de domínio múltiplo para federar com Azure AD](how-to-connect-install-multiple-domains.md).

**Questões fixas:**

* Por vezes, a instalação do Azure AD Connect falha porque não é capaz de criar uma conta de serviço local cuja palavra-passe satisfaça o nível de complexidade especificado pela política de senha da organização.
* Fixo um problema em que as regras de junção não são reavaliadas quando um objeto no espaço do conector simultaneamente se torna fora de alcance para uma regra de junção e se torna no âmbito de outra. Isto pode acontecer se tiver duas ou mais regras de associação cujas condições de união são mutuamente exclusivas.
* Corrigido um problema em que as regras de sincronização de entrada (do Azure AD), que não contêm regras de junção, não são processadas se tiverem valores de precedência mais baixos do que os que contêm regras de junção.

**Melhorias:**

* Suporte adicional para instalar O AZure AD Connect no Windows Server 2016 Standard ou superior.
* Suporte adicionado para a utilização do SQL Server 2016 como base de dados remota para Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Lançado: agosto 2016

**Questões fixas:**

* As alterações ao intervalo de sincronização só ocorrem depois de concluído o ciclo de sincronização seguinte.
* O assistente Azure AD Connect não aceita uma conta AZure AD cujo nome de utilizador começa com um sublinhado ( \_ ).
* O assistente Azure AD Connect não autentica a conta Azure AD se a palavra-passe da conta contiver demasiados caracteres especiais. Mensagem de erro "Incapaz de validar credenciais. Ocorreu um erro inesperado." do produto".
* Desinstalar o servidor de staging desativa a sincronização de palavras-passe no inquilino Azure AD e faz com que a sincronização da palavra-passe falhe com o servidor ativo.
* A sincronização da palavra-passe falha em casos incomuns quando não há hash de senha armazenado no utilizador.
* Quando o servidor AD Connect Azure está ativado para o modo de paragem, a desativação da palavra-passe não é temporariamente desativada.
* O assistente Azure AD Connect não mostra a configuração real de sincronização de palavra-passe e de writeback de palavra-passe quando o servidor está em modo de fase. Mostra-os sempre como deficientes.
* As alterações de configuração da sincronização da palavra-passe e da writeback de palavra-passe não são persistiu pelo assistente Azure AD Connect quando o servidor está em modo de fase.

**Melhorias:**

* Atualize o Start-ADSyncSyncCycle cmdlet para indicar se é capaz de iniciar ou não um novo ciclo de sincronização.
* Acrescentou o Stop-ADSyncSyncCycle cmdlet para terminar o ciclo e o funcionamento da sincronização, que estão atualmente em curso.
* Atualizou o Stop-ADSyncScheduler cmdlet para terminar o ciclo e o funcionamento da sincronização, que estão atualmente em curso.
* Ao configurar [as extensões do Diretório](how-to-connect-sync-feature-directory-extensions.md) no assistente Azure AD Connect, o atributo AD AD do tipo "Cadeia Teletex" pode agora ser selecionado.

## <a name="111890"></a>1.1.189.0
Lançado: junho 2016

**Questões fixas e melhorias:**

* O Azure AD Connect pode agora ser instalado num servidor compatível com FIPS.
  * Para sincronização de palavras-passe, consulte [a sincronização de hash da palavra-passe e o FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Corrigiu um problema em que um nome NetBIOS não podia ser resolvido para o FQDN no Conector de Diretório Ativo.

## <a name="111800"></a>1.1.180.0
Lançado: maio 2016

**Novas funcionalidades:**

* Avisa e ajuda a verificar os domínios se não o fez antes de executar o Azure AD Connect.
* Suporte adicional para [a Microsoft Cloud Germany](reference-connect-instances.md#microsoft-cloud-germany).
* Suporte adicional para a mais recente infraestrutura [em nuvem do Governo microsoft Azure](reference-connect-instances.md#microsoft-azure-government) com novos requisitos de URL.

**Questões fixas e melhorias:**

* Adicionou filtragem ao Sync Rule Editor para facilitar a procura de regras de sincronização.
* Melhor desempenho ao eliminar um espaço de conector.
* Corrigiu um problema quando o mesmo objeto foi apagado e adicionado na mesma execução (chamado delete/add).
* Uma regra de sincronização desativada já não permite que os objetos e atributos incluídos sejam atualizados ou atualizados.

## <a name="111300"></a>1.1.130.0
Lançado: abril 2016

**Novas funcionalidades:**

* Suporte adicional para atributos multi-valorizados às [extensões do Diretório](how-to-connect-sync-feature-directory-extensions.md).
* Suporte adicional para mais variações de configuração para [atualização automática](how-to-connect-install-automatic-upgrade.md) a considerar elegível para upgrade.
* Adicione alguns cmdlets para [agendador personalizado](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Lançado: março 2016

**Questões fixas:**

* Certifique-se de que a instalação Express não pode ser utilizada no Windows Server 2008 (pré-R2) porque a sincronização de palavras-passe não é suportada neste sistema operativo.
* O upgrade da DirSync com uma configuração de filtro personalizado não funcionou como esperado.
* Ao atualizar para uma versão mais recente e não houver alterações na configuração, não deve ser agendada uma importação/sincronização completa.

## <a name="111100"></a>1.1.110.0
Lançado: fevereiro 2016

**Questões fixas:**

* A atualização de versões anteriores não funciona se a instalação não estiver na pasta C:\Program Files predefinido.
* Se instalar e limpar Iniciar o processo de **sincronização** no final do assistente de instalação, executar o assistente de instalação uma segunda vez não ativará o programador.
* O programador não funciona como esperado em servidores onde o formato us-en date/time não é usado. Também bloqueará `Get-ADSyncScheduler` a retornação dos tempos corretos.
* Se instalou um lançamento anterior do Azure AD Connect com AD FS como opção de início e atualização, não poderá voltar a executar o assistente de instalação.

## <a name="111050"></a>1.1.105.0
Lançado: fevereiro 2016

**Novas funcionalidades:**

* [Funcionalidade de atualização automática](how-to-connect-install-automatic-upgrade.md) para clientes express settings.
* Suporte para a administração global utilizando a autenticação multi-factor Azure e a Gestão de Identidade Privilegiada no assistente de instalação.
  * Tem de permitir que o seu representante também permita o tráfego se utilizar a https://secure.aadcdn.microsoftonline-p.com Autenticação Multi-Factor.
  * Tem de adicionar https://secure.aadcdn.microsoftonline-p.com à sua lista de sites fidedignos para que a autenticação multi-factor funcione corretamente.
* Deixe alterar o método de insuimento do utilizador após a instalação inicial.
* Permitir [a filtragem do domínio e da U](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente de instalação. Isto também permite a ligação a florestas onde nem todos os domínios estão disponíveis.
* [O programador](how-to-connect-sync-feature-scheduler.md) está integrado no motor de sincronização.

**Características promovidas desde a pré-visualização até à AG:**

* [Reprodução do dispositivo](how-to-connect-device-writeback.md).
* [Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md).

**Novas funcionalidades de pré-visualização:**

* O novo intervalo de sincronização padrão é de 30 minutos. Costumava ser três horas para todos os lançamentos anteriores. Adiciona suporte para alterar o comportamento do [programador.](how-to-connect-sync-feature-scheduler.md)

**Questões fixas:**

* A página de domínios DNS de verificação nem sempre reconheceu os domínios.
* Solicita credenciais de administração de domínio ao configurar AD FS.
* As contas AD no local não são reconhecidas pelo assistente de instalação se estiverem localizadas num domínio com uma árvore DNS diferente do domínio raiz.

## <a name="1091310"></a>1.0.9131.0
Lançado: dezembro 2015

**Questões fixas:**

* A sincronização de palavras-passe pode não funcionar quando altera palavras-passe nos Serviços de Domínio do Diretório Ativo (DS AD), mas funciona quando define uma palavra-passe.
* Quando tiver um servidor proxy, a autenticação para Azure AD pode falhar durante a instalação ou se uma atualização for cancelada na página de configuração.
* A atualização de uma versão anterior do Azure AD Connect com uma instância completa do SQL Server falha se não for um administrador de sistema SQL Server (SA).
* A atualização de uma versão anterior do Azure AD Connect com um servidor SQL remoto mostra o erro "Não conseguir aceder à base de dados ADSync SQL".

## <a name="1091250"></a>1.0.9125.0
Lançado: novembro 2015

**Novas funcionalidades:**

* Pode reconfigurar o FS AD para a confiança AZure AD.
* Pode refrescar o esquema do Ative Directory e regenerar as regras de sincronização.
* Pode desativar uma regra de sincronização.
* Pode definir "AuthoritituativeNull" como um novo literal numa regra de sincronização.

**Novas funcionalidades de pré-visualização:**

* [Azure AD Connect Health para sincronização](how-to-connect-health-sync.md).
* Suporte para sincronização [de passwords dos Serviços de Domínio Azure AD.](../user-help/active-directory-passwords-update-your-own-password.md)

**Novo cenário apoiado:**

* Suporta várias organizações de intercâmbio no local. Para obter mais informações, consulte [as implementações híbridas com múltiplas florestas de Diretório Ativo.](/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150))

**Questões fixas:**

* Problemas de sincronização de palavras-passe:
  * Um objeto deslocado de fora de alcance para in-scope não terá a sua palavra-passe sincronizada. Isto inclui tanto a uo como a filtragem de atributos.
  * Selecionar um novo OU para incluir em sincronização não requer uma sincronização completa da palavra-passe.
  * Quando um utilizador desativado está ativado, a palavra-passe não sincroniza.
  * A fila de retíria da palavra-passe é infinita e o limite anterior de 5.000 objetos a serem retirados foi removido.
* Não é possível ligar-se ao Ative Directory com o Nível de Funcionamento Florestal do Windows Server 2016.
* Não é possível alterar o grupo utilizado para a filtragem em grupo após a instalação inicial.
* Deixou de criar um novo perfil de utilizador no servidor Azure AD Connect para cada utilizador que faça uma alteração de palavra-passe com a gravação de palavra-passe ativada.
* Não é possível utilizar valores de Inteiro Inteiro em âmbitos de regras de sincronização.
* A caixa de verificação "writeback" permanece desativada se existirem controladores de domínio inacessíveis.

## <a name="1086670"></a>1.0.8667.0
Lançado: agosto 2015

**Novas funcionalidades:**

* O assistente de instalação Azure AD Connect está agora localizado em todos os idiomas do Windows Server.
* Suporte adicional para desbloquear conta ao utilizar a gestão de passwords Azure AD.

**Questões fixas:**

* O assistente de instalação Azure AD Connect falha se outro utilizador continuar a instalação em vez da pessoa que iniciou a instalação.
* Se uma desinstalação anterior do Azure AD Connect não conseguir desinstalar a sincronização do Azure AD Connect de forma limpa, não é possível reinstalar.
* Não é possível instalar o Azure AD Connect utilizando a instalação Express se o utilizador não estiver no domínio raiz da floresta ou se for utilizada uma versão não inglesa do Ative Directory.
* Se o FQDN da conta de utilizador do Ative Directory não puder ser resolvido, é apresentada uma mensagem de erro enganosa "Não ter cometido o esquema".
* Se a conta utilizada no Conector de Diretório Ativo for alterada fora do assistente, o assistente falha nas execuções subsequentes.
* O Azure AD Connect por vezes não é instalado num controlador de domínio.
* Não é possível ativar e desativar "Modo de preparação" se os atributos de extensão tiverem sido adicionados.
* A gravação de palavra-passe falha em algumas configurações devido a uma má palavra-passe no Ative Directory Connector.
* DirSync não pode ser atualizado se um nome distinto (DN) for usado na filtragem de atributos.
* Utilização excessiva do CPU ao utilizar o reset da palavra-passe.

**Funcionalidades de pré-visualização removidas:**

* A funcionalidade de pré-visualização [A gravação do utilizador](how-to-connect-preview.md#user-writeback) foi temporariamente removida com base no feedback dos nossos clientes de pré-visualização. Será adicionado mais tarde depois de abordarmos o feedback fornecido.

## <a name="1086410"></a>1.0.8641.0
Lançado: junho 2015

**Lançamento inicial do Azure AD Connect.**

Mudou de nome de Azure AD Sync para Azure AD Connect.

**Novas funcionalidades:**

* [Instalação de configurações expressas](how-to-connect-install-express.md)
* Pode [configurar ad FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* Pode [fazer upgrade a partir de DirSync](how-to-dirsync-upgrade-get-started.md)
* [Impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Modo [de paragem](how-to-connect-sync-staging-server.md) introduzido

**Novas funcionalidades de pré-visualização:**

* [Writeback do utilizador](how-to-connect-preview.md#user-writeback)
* [Repetição de escrita do dispositivo](how-to-connect-device-writeback.md)
* [Extensões de diretórios](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Lançado: maio 2015

**Novo requisito:**

* O Azure AD Sync exige agora a instalação da versão 4.5.1 do Quadro .NET.

**Questões fixas:**

* A gravação de passwords do Azure AD está a falhar com um erro de conectividade do Azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Lançado: abril 2015

**Questões fixas e melhorias:**

* O Conector ative diretório não processa corretamente se o caixote do lixo estiver ativado e existirem vários domínios na floresta.
* O desempenho das operações de importação foi melhorado para o Conector ativo do Azure.
* Quando um grupo excedeu o limite de adesão (por defeito, o limite é fixado para 50.000 objetos), o grupo foi eliminado no Diretório Ativo Azure. Com o novo comportamento, o grupo não é eliminado, um erro é lançado, e novas mudanças de adesão não são exportadas.
* Um novo objeto não pode ser a provisionado se uma eliminação faseada com o mesmo DN já estiver presente no espaço do conector.
* Alguns objetos estão marcados para sincronização durante uma sincronização delta, mesmo que não haja alterações encenadas no objeto.
* Forçar uma sincronização de palavra-passe também remove a lista de DC preferida.
* O CSExportAnalyzer tem problemas com alguns estados de objetos.

**Novas funcionalidades:**

* Uma junta pode agora ligar-se ao tipo de objeto "ANY" no MV.

## <a name="104850222"></a>1.0.485.0222
Lançado: fevereiro 2015

**Melhorias:**

* Melhor desempenho das importações.

**Questões fixas:**

* Password Sync honra o atributo cloudFiltered que é usado pela filtragem de atributos. Os objetos filtrados já não estão no âmbito da sincronização da palavra-passe.
* Em situações raras em que a topologia tinha muitos controladores de domínio, a sincronização de palavras-passe não funciona.
* "Stop-server" ao importar do Conector AD Azure após a gestão do dispositivo ter sido ativada em Azure AD/Intune.
* Juntar-se aos Principais de Segurança Estrangeira (FSPs) de vários domínios na mesma floresta causa um erro de junção ambígua.

## <a name="104751202"></a>1.0.475.1202
Lançado: dezembro 2014

**Novas funcionalidades:**

* A sincronização de palavra-passe com a filtragem baseada em atributos é agora suportada. Para obter mais informações, consulte [a sincronização da Palavra-passe com a filtragem](how-to-connect-sync-configure-filtering.md).
* O atributo MS-DS-ExternalDirectoryObjectID é escrito de volta ao Ative Directory. Esta funcionalidade adiciona suporte para aplicações microsoft 365. Utiliza o OAuth2 para aceder a caixas de correio online e no local numa Implementação de Intercâmbio Híbrido.

**Problemas de atualização fixos:**

* Uma versão mais recente do assistente de inscrição está disponível no servidor.
* Um caminho de instalação personalizado foi usado para instalar Azure AD Sync.
* Um critério de adesão personalizado inválido bloqueia a atualização.

**Outras correções:**

* Fixou os modelos para Office Pro Plus.
* Problemas de instalação fixos causados por nomes de utilizador que começam com um traço.
* Fixo perder a definição de origemAnchor ao executar o assistente de instalação uma segunda vez.
* Rastreio de ETW fixo para sincronização de palavra-passe.

## <a name="104701023"></a>1.0.470.1023
Lançado: outubro 2014

**Novas funcionalidades:**

* Sincronização de palavra-passe de vários no local Ative Directy a Azure AD.
* UI de instalação localizada a todos os idiomas do Windows Server.

**Upgrade de AADSync 1.0 GA**

Se já tiver o Azure AD Sync instalado, há um passo adicional que tem de dar no caso de ter alterado alguma das regras de sincronização fora de caixa. Depois de ter atualizado para a versão 1.0.470.1023, as regras de sincronização que modificou são duplicadas. Para cada regra de sincronização modificada, faça o seguinte:

1. Localize a regra de sincronização que modificou e tome nota das alterações.
1. Elimine a regra de sincronização.
1. Localize a nova regra de sincronização que é criada por Azure AD Sync e, em seguida, reaplicar as alterações.

**Permissões para a conta ative Directory**

A conta Ative Directory deve ter permissões adicionais para poder ler as hashes de palavra-passe do Ative Directory. As permissões a conceder são denominada "Replicating Directory Changes" e "Replicating Directory Changes All". Ambas as permissões são necessárias para poder ler as hashes da palavra-passe.

## <a name="104190911"></a>1.0.419.0911
Lançado: setembro 2014

**Lançamento inicial do Azure AD Sync.**

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).