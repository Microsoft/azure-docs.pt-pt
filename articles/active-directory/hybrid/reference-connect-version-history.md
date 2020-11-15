---
title: 'Azure AD Connect: Versão de lançamento histórico / Microsoft Docs'
description: Este artigo lista todos os lançamentos de Azure AD Connect e Azure AD Sync.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 08/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73318d1ee14894f5d22f7c4d2e61418e3b1038c1
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636882"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: histórico de versões
A equipa do Azure Ative Directory (Azure AD) atualiza regularmente o Azure AD Connect com novas funcionalidades e funcionalidades. Nem todas as adições são aplicáveis a todos os públicos.

Este artigo foi concebido para o ajudar a acompanhar as versões que foram lançadas e a perceber quais são as alterações na versão mais recente.



Esta tabela é uma lista de tópicos relacionados:

Tópico |  Detalhes
--------- | --------- |
Passos para atualizar a partir do Azure AD Connect | Diferentes métodos para [atualizar de uma versão anterior para o mais recente](how-to-upgrade-previous-version.md) lançamento do Azure AD Connect.
Permissões obrigatórias | Para permissões necessárias para aplicar uma atualização, consulte [contas e permissões](reference-connect-accounts-permissions.md#upgrade).
Download| [Baixar Azure Ad Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Lançar uma nova versão do Azure AD Connect é um processo que requer vários passos de controlo de qualidade para garantir a funcionalidade de funcionamento do serviço, e enquanto passamos por este processo o número de versão de um novo lançamento, bem como o estado de lançamento, serão atualizados para refletir o estado mais recente.
Enquanto passamos por este processo, o número de versão do lançamento será mostrado com um "X" na posição de número de lançamento menor, como em "1.3.X.0" - isto indica que as notas de lançamento neste documento são válidas para todas as versões a partir de "1.3". Assim que finalizarmos o processo de lançamento, o número da versão de lançamento será atualizado para a versão mais recentemente lançada e o estado de lançamento será atualizado para "Release for download and auto upgrade".
Nem todas as versões do Azure AD Connect serão disponibilizadas para atualização automática. O estado de desbloqueio indicará se uma versão está disponível para upgrade automático ou apenas para download. Se a atualização automática foi ativada no seu servidor Azure AD Connect, então esse servidor irá automaticamente atualizar para a versão mais recente do Azure AD Connect que é lançado para atualização automática. Note que nem todas as configurações do Azure AD Connect são elegíveis para upgrade automático. 

Para clarificar a utilização do Auto Upgrade, destina-se a empurrar todas as atualizações importantes e correções críticas para si. Esta não é necessariamente a versão mais recente porque nem todas as versões exigirão/incluem uma correção para um problema crítico de segurança (apenas um exemplo de muitos). Um problema como este seria abordado com uma nova versão fornecida através de Auto Upgrade. Se não existirem tais problemas, não existem atualizações empurradas para fora usando o Auto Upgrade e, em geral, se estiver a utilizar a versão mais recente de upgrade automático deverá ser boa.
No entanto, se quiser todas as funcionalidades e atualizações mais recentes, a melhor maneira de ver se existem alguma é verificar esta página e instalá-las como entender. 

Por favor, siga este link para ler mais sobre [atualização automática](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> A partir de 1 de novembro de 2020, começaremos a implementar um processo de depreciação pelo qual as versões do Azure AD Connect que foram lançadas há mais de 18 meses serão depreciadas. Nessa altura iniciaremos este processo depretendo todos os lançamentos do Azure AD Connect com a versão 1.3.20.0 (que foi lançada em 4/24/2019) e mais antiga, e vamos proceder à avaliação da depreciação das versões mais antigas do Azure AD Connect sempre que uma nova versão for lançada.
>
> Tem de se certificar de que está a executar uma versão recente do Azure AD Connect para receber uma experiência de suporte ideal. 
>
>Se executar uma versão prectificada do Azure AD Connect poderá não ter as mais recentes correções de segurança, melhorias de desempenho, resolução de problemas e ferramentas de diagnóstico e melhorias de serviço, e se necessitar de apoio, poderemos não ser capazes de lhe fornecer o nível de serviço que a sua organização necessita.
>
>Se tiver ativado o Azure AD Connect para sincronização, em breve começará automaticamente a receber notificações de Saúde que o avisam sobre as próximas depreciações quando estiver a executar uma das versões mais antigas.
>
>Consulte [este artigo](./how-to-upgrade-previous-version.md) para saber mais sobre como atualizar o Azure AD Connect para a versão mais recente.
>
>Para obter informações sobre o histórico da versão em versões prepretadas, consulte [o arquivo histórico de versão Azure AD Connect](reference-connect-version-history-archive.md)

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Estado de lançamento
07/29/2020: Lançamento para download

### <a name="functional-changes"></a>Alterações funcionais
Isto é uma correção de erros. Não há alterações funcionais nesta versão.

### <a name="fixed-issues"></a>Problemas corrigidos

- Corrigiu um problema em que a administração não pode ativar "Sem emenda Single Sign On" se a conta de computador AZUREADSSOACC já estiver presente no "Ative Directory".
- Corrigiu um problema que causou um erro de encenação durante a importação delta da V2 API para um objeto conflituoso que foi reparado através do portal de saúde.
- Fixou um problema na configuração de importação/exportação em que a regra de produtos personalizados com deficiência foi importada conforme habilitado.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Estado de lançamento
07/10/2020: Lançamento para download

### <a name="functional-changes"></a>Alterações funcionais
Esta versão inclui uma pré-visualização pública da funcionalidade para exportar a configuração de um servidor AZure AD Connect existente para um . Ficheiro JSON que pode ser utilizado ao instalar um novo servidor Azure AD Connect para criar uma cópia do servidor original.

Uma descrição detalhada desta nova característica pode ser encontrada [neste artigo](./how-to-connect-import-export-config.md)

### <a name="fixed-issues"></a>Problemas corrigidos
- Corrigi um bug onde haveria um aviso falso sobre o tamanho do DB local nas construções localizadas durante a atualização.
- Corrigiu um bug onde haveria um erro falso nos eventos da aplicação para a troca de nome de conta/nome de domínio.
- Corrigiu um erro em que o Azure AD Connect não conseguiria instalar-se num DC, dando um erro de "membro não encontrado".


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Estado de lançamento
05/07/2020: Lançamento para download

### <a name="fixed-issues"></a>Problemas corrigidos
Esta construção de hotfix corrige um problema em que domínios não selecionados estavam a ser incorretamente selecionados a partir da UI do feiticeiro se apenas os recipientes de netos fossem selecionados.


>[!NOTE]
>Esta versão inclui a nova Azure AD Connect sync V2 endpoint API.  Este novo ponto final V2 está atualmente em pré-visualização pública.  Esta versão ou posteriormente é necessária para utilizar a nova API do ponto final V2.  No entanto, a simples instalação desta versão não permite o ponto final V2. Continuará a utilizar o ponto de terminação V1 a menos que ative o ponto final V2.  Tem de seguir os passos sob [a Azure AD Connect sync V2 endpoint API (pré-visualização pública)](how-to-connect-sync-endpoint-api-v2.md) para o permitir e optar pela pré-visualização pública.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Estado de lançamento
04/23/2020: Lançamento para download

### <a name="fixed-issues"></a>Problemas corrigidos
Esta construção de hotfix corrige uma questão introduzida na construção 1.5.20.0 onde um administrador inquilino com MFA não foi capaz de ativar o DSSO.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Estado de lançamento
04/20/2020: Lançamento para download

### <a name="fixed-issues"></a>Problemas corrigidos
Esta construção de hotfix corrige um problema na construção 1.5.20.0 se tiver clonado o **In from AD - Regra de Grupo Aderir** e não tiver clonado o In da **AD - Regra comum do Grupo.**

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Estado de lançamento
04/09/2020: Lançamento para download

### <a name="fixed-issues"></a>Problemas corrigidos
- Esta construção de hotfix corrige um problema com a construção 1.5.18.0 se tiver a função de filtragem do grupo ativada e utilizar o mS-DS-ConsistencyGuid como âncora de origem.
- Corrigiu um problema no módulo ADSyncConfig PowerShell, onde invocar o comando DSACLS utilizado em todos os cmdlets de permissões Set-ADSync* causaria um dos seguintes erros:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Se clonou o **In from AD - Regra de** sincronização do Grupo e não clonou a regra de In from **AD - Group Common** sync rule e planeia atualizar, complete os seguintes passos como parte da atualização:
> 1. Durante a atualização, desmarque a opção **Inicie o processo de sincronização quando a configuração estiver concluída**.
> 2. Editar a regra de sincronização clonada e adicionar as seguintes duas transformações:
>     - Desacorda o fluxo direto `objectGUID` para `sourceAnchorBinary` .
>     - Definir o fluxo de expressão `ConvertToBase64([objectGUID])` para `sourceAnchor` .     
> 3. Ativar a utilização do programador `Set-ADSyncScheduler -SyncCycleEnabled $true` .



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Estado de lançamento
04/02/2020: Lançamento para download

### <a name="functional-changes-adsyncautoupgrade"></a>Alterações funcionais ADSyncAutoUpgrado 

- Suporte adicional para a função mS-DS-ConsistencyGuid para objetos de grupo. Isto permite-lhe mover grupos entre florestas ou reconectar grupos em AD para Azure AD onde o objetoid do grupo AD mudou, por exemplo, quando um servidor AD é reconstruído após uma calamidade. Para obter mais informações, consulte [grupos móveis entre florestas.](how-to-connect-migrate-groups.md)
- O atributo mS-DS-ConsistencyGuid é automaticamente definido em todos os grupos sincronizados e não tem de fazer nada para ativar esta funcionalidade. 
- Retire o Get-ADSyncRunProfile porque já não está a ser utilizado. 
- Alterou o aviso que vê ao tentar utilizar uma conta Enterprise Admin ou Domain Admin para a conta de conector AD DS para fornecer mais contexto. 
- Adicionou um novo cmdlet para remover objetos do espaço do conector, a antiga ferramenta CSDelete.exe é removida e é substituída pelo novo cmdlet Remove-ADSyncCSObject. O Remove-ADSyncCSObject cmdlet toma um CsObject como entrada. Este objeto pode ser recuperado utilizando o Get-ADSyncCSObject cmdlet.

>[!NOTE]
>A antiga ferramenta CSDelete.exe foi removida e substituída pelo novo cmdlet Remove-ADSyncCSObject 

### <a name="fixed-issues"></a>Problemas corrigidos

- Corrigiu um erro no seletor de floresta/u de gravação de grupo ao refazer o assistente Azure AD Connect depois de desativar a funcionalidade. 
- Introduziu uma nova página de erro que será exibida se os valores de registo DCOM necessários estiverem em falta com uma nova ligação de ajuda. A informação também é escrita para registar ficheiros. 
- Corrigiu um problema com a criação da conta de sincronização do Azure Ative Directory onde permitir extensões de diretório ou PHS pode falhar porque a conta não se propagou em todas as réplicas de serviço antes de tentar a utilização. 
- Corrigiu um erro no utilitário de compressão de erros de sincronização que não manuseava corretamente os caracteres de substituição. 
- Corrigiu um erro na atualização automática que deixou o servidor no estado suspenso do programador. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Estado de lançamento
12/9/2019: Lançamento para download. Não disponível através de upgrade automático.
### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
- Atualizámos o Password Hash Sync para os Serviços de Domínio AZure AD para contabilizar adequadamente o enchimento em kerberos hashes.  Isto proporcionará uma melhoria de desempenho durante a sincronização de passwords de Azure AD para Azure AD Domain Services.
- Adicionámos suporte para sessões fiáveis entre o agente de autenticação e o autocarro de serviço.
- Esta versão impõe TLS 1.2 para comunicação entre o agente de autenticação e os serviços na nuvem.
- Adicionámos uma cache DNS para ligações websocket entre o agente de autenticação e os serviços de nuvem.
- Adicionámos a capacidade de direcionar o agente específico da nuvem para testar a conectividade do agente.

### <a name="fixed-issues"></a>Problemas corrigidos
- O lançamento 1.4.18.0 tinha um bug onde o cmdlet PowerShell para DSSO estava a usar as credenciais de janelas de login em vez das credenciais de administração fornecidas durante a execução do PS. Como resultado do qual não foi possível ativar o DSSO em múltiplas florestas através da interface de utilizador Azure AD Connect. 
- Foi feita uma correção para permitir o DSSO simultaneamente em toda a floresta através da interface de utilizador Azure AD Connect

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Estado de lançamento
11/08/2019: Lançamento para download. Não disponível através de upgrade automático.

>[!IMPORTANT]
>Devido a uma alteração de esquema interno nesta versão do Azure AD Connect, se gerir as definições de configuração de relacionamento de confiança AD FS utilizando o MSOnline PowerShell, então deve atualizar o seu módulo MSOnline PowerShell para a versão 1.1.183.57 ou superior

### <a name="fixed-issues"></a>Problemas corrigidos

Esta versão corrige um problema com os dispositivos existentes híbridos Azure AD. Esta versão contém uma nova regra de sincronização do dispositivo que corrige este problema.
Note que esta alteração de regras pode causar a eliminação de dispositivos obsoletos da Azure AD. Isto não é motivo de preocupação, uma vez que estes objetos do dispositivo não são utilizados pela Azure AD durante a autorização de Acesso Condicional. Para alguns clientes, o número de dispositivos que serão eliminados através desta alteração de regra pode exceder o limiar de eliminação. Se vir a eliminação de objetos do dispositivo em Azure AD que exceda o Limiar de Eliminação de Exportação, é aconselhável permitir a passagem das supressões. [Como permitir que as eliminações fluam quando excedem o limiar de eliminação](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Estado de lançamento
28/09/2019: Lançado para upgrade automático para inquilinos selecionados. Não está disponível para download.

Esta versão corrige um bug onde alguns servidores que foram atualizados automaticamente de uma versão anterior para 1.4.18.0 e problemas experimentados com reset de palavra-passe de self-service (SSPR) e Password Writeback.

### <a name="fixed-issues"></a>Problemas corrigidos

Em certas circunstâncias, os servidores que foram atualizados automaticamente para a versão 1.4.18.0 não reeceram o reset da palavra-passe de autosserviço e a writeback de palavra-passe após a conclusão da atualização. Esta versão de atualização automática corrige esse problema e re-permite reiniciar a palavra-passe de autosserviço e o Writeback de Password.

Corrigimos um bug no utilitário de compressão de erros de sincronização que não manuseava corretamente os caracteres de substituição.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Estamos a investigar um incidente em que alguns clientes estão a ter um problema com os dispositivos híbridos Azure AD existentes após o upgrade para esta versão do Azure AD Connect. Aconselhamos os clientes que implementaram a Hybrid Azure AD a aderir ao adiamento da atualização para esta versão até que a causa principal destes problemas seja totalmente compreendida e atenuada. Mais informações serão fornecidas o mais rapidamente possível.

>[!IMPORTANT]
>Com esta versão do Azure AD Connect alguns clientes podem ver alguns ou todos os seus dispositivos Windows desaparecerem do Azure AD. Isto não é motivo de preocupação, uma vez que estas identidades do dispositivo não são utilizadas pela Azure AD durante a autorização de Acesso Condicional. Para mais informações consulte [o AD AD Understanding 1.4.xx.x dispositivo desaparecer](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Estado de lançamento
9/25/2019: Lançado apenas para upgrade automático.

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
- A nova ferramenta de resolução de problemas ajuda a resolver problemas de cenários de "utilizador não sincronizado", "grupo não sincronizado" ou "membro do grupo que não sincroniza".
- Adicione suporte para nuvens nacionais no roteiro de resolução de problemas do Azure AD Connect.
- Os clientes devem ser informados de que os pontos finais do WMI precotados para MIIS_Service foram agora removidos. Quaisquer operações de WMI devem agora ser feitas através de cmdlets PS.
- Melhoria da segurança repondo a delegação restrita no objeto AZUREADSSOACC.
- Ao adicionar/editar uma regra de sincronização, se existirem quaisquer atributos utilizados na regra que estejam no esquema do conector mas não adicionados ao conector, os atributos adicionados automaticamente ao conector. O mesmo se aplica ao tipo de objeto que a regra afeta. Se alguma coisa for adicionada ao conector, o conector será marcado para a importação completa no próximo ciclo de sincronização.
- A utilização de um administrador enterprise ou de domínio, uma vez que a conta de conector já não é suportada em novas implementações de conexão Ad AD Azure. As atuais implementações de Azure AD Connect utilizando um administrador Enterprise ou Domain, uma vez que a conta de conector não será afetada por esta versão.
- No Gestor de Sincronização é executado um sincronização completo na criação/edição/eliminação de regras. Aparecerá um pop-up em qualquer alteração de regras que notifique o utilizador se a importação total ou sincronização total forão executadas.
- Medidas de mitigação adicionais para erros de palavra-passe para "conectores > propriedades > conectividade".
- Adicionei um aviso de depreciação para o gestor de serviço de sincronização na página de propriedades do conector. Este aviso notifica o utilizador de que as alterações devem ser efetuadas através do assistente Azure AD Connect.
- Adicionou um novo erro para problemas com a política de senha de um utilizador.
- Evitar a configuração errada da filtragem de grupo por domínio e filtros U. A filtragem do grupo mostrará um erro quando o domínio/U do grupo introduzido já estiver filtrado e impedirá o utilizador de avançar até que o problema seja resolvido.
- Os utilizadores já não podem criar um conector para serviços de domínio de diretório ativo ou diretório ativo do Windows Azure na UI do Gestor de Serviços de Sincronização.
- Acessibilidade fixa dos controlos de UI personalizados no Gestor de Serviços de Sincronização.
- Habilitaram seis tarefas de gestão da federação para todos os métodos de inscrição no Azure AD Connect.  (Anteriormente, apenas a tarefa "Update AD FS TLS/SSL certificate" estava disponível para todos os insus máximos.)
- Acrescentou um aviso ao alterar o método de inscrição da federação para PHS ou PTA, que todos os domínios EAure e utilizadores serão convertidos para autenticação gerida.
- Removidos certificados de assinatura simbólica da tarefa "Reset Azure AD e AD FS trust" e adicionou uma sub-tarefa separada para atualizar estes certificados.
- Adicionou uma nova tarefa de gestão da federação chamada "Gerir certificados" que tem sub-tarefas para atualizar os certificados de assinatura de TLS ou token para a exploração AD FS.
- Adicionou uma nova sub-tarefa de gestão da federação chamada "Especificar o servidor primário" que permite aos administradores especificar um novo servidor primário para a fazenda AD FS.
- Adicionou uma nova tarefa de gestão da federação chamada "Gerir servidores" que tem sub-tarefas para implementar um servidor AD FS, implementar um servidor Proxy de aplicação web e especificar o servidor primário.
- Acrescentou uma nova tarefa de gestão da federação chamada "Ver configuração da federação" que exibe as atuais definições de FS AD.  (Devido a esta adição, as definições de FS AD foram removidas da página "Rever a sua solução".)

### <a name="fixed-issues"></a>Problemas corrigidos
- Problema de erro de sincronização resolvido para o cenário em que um objeto de utilizador que assume o seu objeto de contacto correspondente tem uma autorreferência (por exemplo, o utilizador é o seu próprio gestor).
- Ajude os popups agora a aparecer no foco do teclado.
- Para a atualização automática, se alguma aplicação conflituosa estiver a funcionar a partir de 6 horas, mate-a e continue com o upgrade.
- Limitar o número de atributos que um cliente pode selecionar para 100 por objeto ao selecionar extensões de diretório. Isto evitará que o erro ocorra durante a exportação, uma vez que o Azure tem um máximo de 100 atributos de extensão por objeto.
- Corrigiu um bug para tornar o script de Conectividade AD mais robusto.
- Corrigiu um bug para tornar a instalação Azure AD Connect numa máquina utilizando um serviço WCF de tubos denominado existente mais robusto.
- Diagnósticos melhorados e resolução de problemas em torno de políticas de grupo que não permitem que o serviço ADSync comece quando inicialmente instalado.
- Corrigiu um erro onde o nome de exibição de um computador Windows foi escrito incorretamente.
- Corrija um bug onde o tipo de SO para um computador Windows foi escrito incorretamente.
- Corrigiu um bug onde os computadores não-Windows 10 estavam a sincronizar inesperadamente. Note que o efeito desta alteração é que os computadores não-Windows-10 que foram previamente sincronizados serão agora eliminados. Isto não afeta nenhuma funcionalidade, uma vez que a sincronização dos computadores Windows é utilizada apenas para a junção de domínio Azure AD híbrido, que funciona apenas para dispositivos Windows-10.
- Adicionou vários cmdlets novos (internos) ao módulo ADSync PowerShell.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Existe um problema conhecido com a atualização do Azure AD Connect de uma versão anterior para 1.3.21.0 onde o portal Microsoft 365 não reflete a versão atualizada, apesar de o Azure AD Connect ter atualizado com sucesso.
>
> Para resolver isto, é necessário importar o módulo **AdSync** e, em seguida, executar o `Set-ADSyncDirSyncConfiguration` cmdlet PowerShell no servidor Azure AD Connect.  Pode utilizar os seguintes passos:
>
>1. Abrir PowerShell no modo de administrador.
>2. Execute `Import-Module "ADSync"`.
>3. Execute `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>Estado de lançamento 

05/14/2019: Lançamento para download

### <a name="fixed-issues"></a>Problemas corrigidos 

- Fixou uma vulnerabilidade de elevação de privilégios que existe no Microsoft Azure Ative Directory Connect construir 1.3.20.0.  Esta vulnerabilidade, sob determinadas condições, pode permitir que um intruso execute dois cmdlets PowerShell no contexto de uma conta privilegiada, e execute ações privilegiadas.  Esta atualização de segurança aborda o problema desativando estes cmdlets. Para mais informações consulte [a atualização de segurança](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
