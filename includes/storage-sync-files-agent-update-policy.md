---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77123212"
---
O agente Dessincronização de Ficheiros Azure é atualizado regularmente para adicionar novas funcionalidades e resolver problemas. Recomendamos que configure o Microsoft Update para obter atualizações para o agente Dessincronização de Ficheiros Azure, uma vez que estão disponíveis.

#### <a name="major-vs-minor-agent-versions"></a>Versões de agente menor
* As versões principais de agentes muitas vezes contêm novas funcionalidades e têm um número crescente como a primeira parte do número da versão. Por exemplo: \*\*2. .\*\*
* As versões de agente menor também são chamadas de "patches" e são lançadas com mais frequência do que as principais versões. Muitas vezes contêm correções de bugs e melhorias menores, mas sem novas funcionalidades. Por exemplo: \* \*.3.\*\*

#### <a name="upgrade-paths"></a>Upgrade caminhos
Existem quatro formas aprovadas e testadas de instalar as atualizações do agente Azure File Sync. 
1. **(Preferido) Configure o Microsoft Update para descarregar e instalar automaticamente as atualizações do agente.**  
    Recomendamos sempre que todas as atualizações do Azure File Sync garantam que tem acesso às mais recentes correções para o servidor. O Microsoft Update torna este processo sem emenda, ao descarregar e instalar automaticamente atualizações para si.
2. **Utilize AfsUpdater.exe para descarregar e instalar atualizações de agentes.**  
    O AfsUpdater.exe está localizado no diretório de instalação do agente. Clique duas vezes no executável para descarregar e instalar atualizações de agentes. 
3. **Remende um agente Desincronização de Ficheiros Azure existente utilizando um ficheiro de patch da Microsoft Update ou um .msp executável. O mais recente pacote de atualização do Ficheiro Sincronizado do Azure pode ser descarregado do Catálogo de [Atualizações](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)da Microsoft .**  
    Executar uma .msp executável irá atualizar a sua instalação De sincronização de ficheiros Azure com o mesmo método usado automaticamente pela Microsoft Update no caminho de atualização anterior. A aplicação de um patch de Atualização do Microsoft irá realizar uma atualização em vigor de uma instalação De Sincronização de Ficheiros Azure.
4. **Descarregue o mais recente instalador de ficheiros Azure Sync do [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Para atualizar uma instalação existente do agente Dote De ficheiros, desinstale a versão mais antiga e, em seguida, instale a versão mais recente a partir do instalador descarregado. O registo do servidor, grupos de sincronização e quaisquer outras definições são mantidos pelo instalador De sincronização de ficheiros Azure.

#### <a name="automatic-agent-lifecycle-management"></a>Gestão automática do ciclo de vida do agente
Com a versão 6 do agente, a equipa de sincronização de ficheiros introduziu uma funcionalidade de atualização automática do agente. Pode selecionar um dos dois modos e especificar uma janela de manutenção na qual a atualização deve ser tentada no servidor. Esta funcionalidade foi concebida para o ajudar na gestão do ciclo de vida do agente, fornecendo um guarda-costas que impeça o seu agente de expirar ou permitindo uma definição de corrente sem aborrecimentos.
1. A **definição predefinida** tentará impedir a expiração do agente. No prazo de 21 dias após a data de validade de um agente, o agente tentará auto-actualizar-se. Iniciará uma tentativa de atualização uma vez por semana no prazo de 21 dias antes da expiração e na janela de manutenção selecionada. **Esta opção não elimina a necessidade de tomar patches regulares do Microsoft Update.**
1. Opcionalmente, pode selecionar que o agente irá automaticamente atualizar-se assim que uma nova versão do agente estiver disponível (atualmente não aplicável aos servidores agrupados). Esta atualização ocorrerá durante a janela de manutenção selecionada e permitirá que o seu servidor beneficie de novas funcionalidades e melhorias assim que estas se tornem geralmente disponíveis. Esta é a definição recomendada e sem preocupações que fornecerá versões de agente importantes, bem como patches de atualização regulares para o seu servidor. Todos os agentes libertados estão na qualidade da GA. Se selecionar esta opção, a Microsoft irá fazer-lhe a versão mais recente do agente. Os servidores agrupados estão excluídos. Uma vez concluído o voo, o agente também estará disponível no [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257) aka.ms/AFS/agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Alterar a definição de atualização automática

As seguintes instruções descrevem como alterar as definições depois de ter concluído o instalador, se precisar de fazer alterações.

Abra uma consola PowerShell e navegue para o diretório onde instalou o agente de sincronização e depois importe os cmdlets do servidor. Por defeito, isto seria algo parecido com isto:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

Pode correr `Get-StorageSyncAgentAutoUpdatePolicy` para verificar a definição de política atual e determinar se quer alterá-la.

Para alterar a definição de política atual para a pista de atualização retardada, pode utilizar:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Para alterar a definição de política atual para a pista de atualização imediata, pode utilizar:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garantias de gestão de ciclo de vida e mudança de agente
O Azure File Sync é um serviço na nuvem, que introduz continuamente novas funcionalidades e melhorias. Isto significa que uma versão específica do agente Dostásio de Ficheiros Azure só pode ser suportada por um período limitado. Para facilitar a sua implementação, as seguintes regras garantem que tem tempo e notificação suficientes para acomodar atualizações/atualizações de agentes no seu processo de gestão de alterações:

- As versões de agente principal são suportadas durante pelo menos seis meses a partir da data de lançamento inicial.
- Garantimos que há uma sobreposição de pelo menos três meses entre o apoio das principais versões de agentes. 
- Os avisos são emitidos para servidores registados utilizando um agente em breve expirado pelo menos três meses antes da expiração. Pode verificar se um servidor registado está a utilizar uma versão mais antiga do agente sob a secção de servidores registado de um Serviço de Sincronização de Armazenamento.
- A vida útil de uma versão de agente menor está ligada à versão principal associada. Por exemplo, quando a versão 3.0 do agente é lançada, as versões do agente 2. \* será tudo definido para expirar juntos.

> [!Note]
> Instalar uma versão do agente com um aviso de validade mostrará um aviso, mas terá sucesso. Tentar instalar ou ligar-se a uma versão de agente expirado não é suportado e será bloqueado.
