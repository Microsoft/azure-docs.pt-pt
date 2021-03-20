---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77123212"
---
O agente Azure File Sync é atualizado regularmente para adicionar novas funcionalidades e resolver problemas. Recomendamos que configuure o Microsoft Update para obter atualizações para o agente Azure File Sync à medida que estão disponíveis.

#### <a name="major-vs-minor-agent-versions"></a>Principais vs. versões de agentes menores
* As principais versões de agentes muitas vezes contêm novas funcionalidades e têm um número crescente como a primeira parte do número da versão. Por exemplo: \* \* 2. . .\*\*
* As versões de agentes menores também são chamadas de "patches" e são lançadas com mais frequência do que as principais versões. Muitas vezes contêm correções de bugs e melhorias menores, mas não há novidades. Por exemplo: \* \* .3.\*\*

#### <a name="upgrade-paths"></a>Caminhos de upgrade
Existem quatro formas aprovadas e testadas de instalar as atualizações do agente Azure File Sync. 
1. **(Preferido) Configure o Microsoft Update para descarregar e instalar automaticamente atualizações de agentes.**  
    Recomendamos sempre que tome todas as atualizações do Azure File Sync para garantir que tem acesso às correções mais recentes para o agente do servidor. O Microsoft Update torna este processo sem emenda, descarregando e instalando automaticamente atualizações para si.
2. **Utilize AfsUpdater.exe para descarregar e instalar atualizações de agentes.**  
    O AfsUpdater.exe está localizado no diretório de instalação do agente. Clique duas vezes no executável para descarregar e instalar atualizações de agentes. 
3. **Corrigir um agente Azure File Sync existente utilizando um ficheiro de patch microsoft update ou um .msp executável. O mais recente pacote de atualização do Azure File Sync pode ser descarregado a partir do Catálogo de [Atualização](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)da Microsoft .**  
    Executar um executável .msp irá atualizar a sua instalação Azure File Sync com o mesmo método utilizado automaticamente pelo Microsoft Update na trajetória de atualização anterior. A aplicação de um patch microsoft update irá realizar uma atualização no local de uma instalação Azure File Sync.
4. **Descarregue o mais recente instalador de agente Azure File Sync do [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Para atualizar uma instalação existente do agente Azure File Sync, desinstale a versão mais antiga e instale a versão mais recente do instalador descarregado. O registo do servidor, grupos de sincronização e quaisquer outras definições são mantidos pelo instalador Azure File Sync.

#### <a name="automatic-agent-lifecycle-management"></a>Gestão automática do ciclo de vida do agente
Com a versão 6 do agente, a equipa de sincronização de ficheiros introduziu uma funcionalidade de atualização automática do agente. Pode selecionar um dos dois modos e especificar uma janela de manutenção na qual a atualização será tentada no servidor. Esta funcionalidade foi concebida para o ajudar na gestão do ciclo de vida do agente, fornecendo um guarda-costas que impede o seu agente de expirar ou permitindo uma definição de corrente sem incómodos.
1. A **definição predefinida** tentará impedir que o agente expira. No prazo de 21 dias a contar da data de validade de um agente, o agente tentará fazer uma auto-actualização. Iniciará uma tentativa de atualização uma vez por semana no prazo de 21 dias antes da expiração e na janela de manutenção selecionada. **Esta opção não elimina a necessidade de tomar patches regulares do Microsoft Update.**
1. Opcionalmente, pode selecionar que o agente irá automaticamente atualizar-se assim que uma nova versão do agente estiver disponível (atualmente não aplicável aos servidores agrupados). Esta atualização ocorrerá durante a janela de manutenção selecionada e permitirá que o seu servidor beneficie de novas funcionalidades e melhorias assim que estas se tornem geralmente disponíveis. Esta é a definição recomendada e sem preocupações que irá fornecer versões de agente importantes, bem como patches de atualização regulares para o seu servidor. Todos os agentes libertados estão na qualidade de GA. Se selecionar esta opção, a Microsoft irá para si a versão mais recente do agente. Os servidores agrupados estão excluídos. Uma vez que o voo esteja concluído, o agente também estará disponível no [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257) aka.ms/AFS/agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Alterar a definição de upgrade automático

As seguintes instruções descrevem como alterar as definições depois de ter concluído o instalador, se precisar de fazer alterações.

Abra uma consola PowerShell e navegue para o diretório onde instalou o agente de sincronização e, em seguida, importe os cmdlets do servidor. Por defeito, isto seria algo parecido com isto:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

Pode correr `Get-StorageSyncAgentAutoUpdatePolicy` para verificar a definição de política atual e determinar se deseja alterá-la.

Para alterar a definição de política atual para a faixa de atualização atrasada, pode utilizar:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Para alterar a definição de política atual para a faixa de atualização imediata, pode utilizar:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garantias de gestão de ciclo de vida e mudança de agente
O Azure File Sync é um serviço em nuvem, que introduz continuamente novas funcionalidades e melhorias. Isto significa que uma versão específica do agente Azure File Sync só pode ser suportada por um tempo limitado. Para facilitar a sua implementação, as seguintes regras garantem que tem tempo e notificação suficientes para acomodar atualizações/atualizações de agentes no seu processo de gestão de alterações:

- As principais versões de agentes são suportadas durante pelo menos seis meses a partir da data de lançamento inicial.
- Garantimos que existe uma sobreposição de pelo menos três meses entre o apoio das principais versões de agentes. 
- São emitidos avisos para servidores registados utilizando um agente que em breve expirará pelo menos três meses antes da expiração. Pode verificar se um servidor registado está a utilizar uma versão mais antiga do agente sob a secção de servidores registados de um Serviço de Sincronização de Armazenamento.
- A vida útil de uma versão de agente menor está ligada à versão principal associada. Por exemplo, quando a versão 3.0 do agente é lançada, as versões de agente 2. \* Todos serão definidos para expirar juntos.

> [!Note]
> A instalação de uma versão do agente com um aviso de expiração apresentará um aviso, mas terá êxito. A tentativa de instalar ou ligar-se a uma versão de agente expirada não é suportada e será bloqueada.
