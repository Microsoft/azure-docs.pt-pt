---
title: Notas de lançamento para o agente Dessincronização de Ficheiros Azure [ Microsoft Docs
description: Notas de lançamento para o agente Dessincronização de Ficheiros Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/16/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 578d00d4bd65b3ffbfb6cdac439762344604e6b8
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804882"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notas de versão do agente do Azure File Sync
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. As suas instalações do Windows Server são transformadas numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS). Pode ter o número de caches que precisar em todo o mundo.

Este artigo disponibiliza as notas de versão das versões suportadas do agente do Azure File Sync.

## <a name="supported-versions"></a>Versões suportadas
São suportadas as seguintes versões para o agente do Azure File Sync:

| Marco | Número de versão do agente | Data da versão | Estado |
|----|----------------------|--------------|------------------|
| Rollup de atualização de dezembro de 2019 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 de dezembro de 2019 | Suportado |
| V9 Lançamento - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 de dezembro de 2019 | Suportado |
| V8 Lançamento - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 de outubro de 2019 | Suportado |
| Julho 2019 atualização rollup - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 de julho de 2019 | Suportado |
| Julho 2019 atualização rollup - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 de julho de 2019 | Suportado |
| V7 Lançamento - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 de junho de 2019 | Suportado |
| Junho de 2019 rollup - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27 de junho de 2019 | Suportado - Versão do agente expirará no dia 21 de abril de 2020 |
| Junho de 2019 atualização rollup - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13 de junho de 2019 | Suportado - Versão do agente expirará no dia 21 de abril de 2020 |
| Rollup de atualização de maio de 2019 - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7 de maio de 2019 | Suportado - Versão do agente expirará no dia 21 de abril de 2020 |
| V6 Lançamento - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21 de abril de 2019 | Suportado - Versão do agente expirará no dia 21 de abril de 2020 |
| Libertação V5 | 5.0.2.0 - 5.2.0.0 | N/D | Não Suportado - Versões de agente expiraram em 18 de março de 2020 |
| Lançamento V4 | 4.0.1.0 - 4.3.0.0 | N/D | Não Suportado - Versões de agente expiraram a 6 de novembro de 2019 |
| Lançamento V3 | 3.1.0.0 - 3.4.0.0 | N/D | Não Suportado - Versões de agente expiraram a 19 de agosto de 2019 |
| Agentes pré-GA | 1.1.0.0 - 3.0.13.0 | N/D | Não Suportado - Versões de agente expiraram a 1 de outubro de 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente do Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-9100"></a>Versão do agente 9.1.0.0
As seguintes notas de lançamento são para a versão 9.1.0.0 do agente Azure File Sync lançado a 12 de dezembro de 2019. Estas notas são além das notas de lançamento listadas para a versão 9.0.0.0.

Emissão fixada nesta versão:  
- Sync falha com um dos seguintes erros após a atualização para a versão 9.0 do agente Doficheiro Azure:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Verdete 9.0.0.0
As seguintes notas de lançamento são para a versão 9.0.0.0 do agente Azure File Sync (lançado a 2 de dezembro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e questões que são corrigidas

- Suporte de restauração de self-service
    - Os utilizadores podem agora restaurar os seus ficheiros utilizando a funcionalidade versão anterior. Antes do lançamento do V9, a funcionalidade de versão anterior não era suportada em volumes que tivessem o nível de nuvem ativado. Esta função deve ser ativada para cada volume separadamente, no qual existe um ponto final com tiering em nuvem ativado. Para saber mais, ver  
[Restauro de self-service através de versões anteriores e VSS (Serviço](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service)de Cópia de Sombra de Volume) . 
 
- Suporte para maiores tamanhos de partilha de ficheiros 
    - O Azure File Sync suporta agora até 64TiB e 100 milhões de ficheiros num único espaço de nome sincronizado.  
 
- Suporte de desduplicação de dados no Servidor 2019 
    - A deduplicação de dados é agora suportada com o tiering em nuvem ativado no Windows Server 2019. Para suportar a desduplicação de dados em volumes com tiering em nuvem, o Windows atualiza [KB4520062](https://support.microsoft.com/help/4520062) deve ser instalado. 
 
- Tamanho mínimo de ficheiro melhorado para um ficheiro a nível 
    - O tamanho mínimo de ficheiro para um ficheiro a nível baseia-se agora no tamanho do cluster do sistema de ficheiros (o dobro do tamanho do cluster do sistema de ficheiros). Por exemplo, por padrão, o tamanho do cluster do sistema de ficheiros NTFS é 4KB, o tamanho mínimo de ficheiro resultante para um ficheiro a nível é de 8KB. 
 
- Cmdlet de teste de conectividade da rede 
    - Como parte da configuração do Sincronia de Ficheiros Azure, vários pontos finais de serviço devem ser contactados. Cada um tem o seu próprio nome DNS que precisa de ser acessível ao servidor. Estes URLs também são específicos da região a que um servidor está registado. Uma vez registado um servidor, o cmdlet de teste de conectividade (PowerShell e Server Registration Utility) pode ser usado para testar comunicações com todos os URLs específicos deste servidor. Este cmdlet pode ajudar a resolver problemas quando uma comunicação incompleta impede o servidor de trabalhar totalmente com o Azure File Sync e pode ser usado para afinar configurações de proxy e firewall.  
 
        Para executar o teste de conectividade da rede, executar os seguintes comandos PowerShell: 
 
        Módulo de Importação "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Remova a melhoria do ponto final do servidor quando o tiering da nuvem estiver ativado 
    - Como antes, a remoção de um ponto final do servidor não resulta na remoção de ficheiros na partilha de ficheiros Azure. No entanto, o comportamento dos pontos de reparse no servidor local mudou. Os pontos de reparse (indicações para ficheiros que não são locais no servidor) são agora eliminados ao remover um ponto final do servidor. Os ficheiros totalmente em cache permanecerão no servidor. Esta melhoria foi feita para evitar [ficheiros órfãos](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) ao remover um ponto final do servidor. Se o ponto final do servidor for recriado, os pontos de reparse para os ficheiros hierárquicos serão recriados no servidor.  
 
- Melhorias no desempenho e na fiabilidade 
    - Falhas de recordação reduzidas. O tamanho da recolha é agora automaticamente ajustado com base na largura de banda da rede. 
    - Melhordesempenho de download ao adicionar um novo servidor a um grupo de sincronização. 
    - Ficheiros reduzidos não sincronizados devido a conflitos de restrição. 
    - Os ficheiros não conseguem ser nidificados ou são inesperadamente recordados em certos cenários se o caminho final do servidor for um ponto de montagem de volume.
    
### <a name="evaluation-tool"></a>Ferramenta de Avaliação
Antes de implementar o Azure File Sync, deve avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação De sincronização de ficheiros Azure. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão sem suporte de SO. Para instruções de instalação e utilização, consulte a secção ferramenta de [avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planeamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para obter mais informações sobre como instalar e configurar o agente Dessincronização de ficheiros Azure com o Windows Server, consulte o Planeamento para uma implementação de Sincronização de [Ficheiros Azure](storage-sync-files-planning.md) e como implementar o [Sync de Ficheiros Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões elevadas (administradoras).
- O agente não é suportado na opção de implementação do Nano Server.
- O agente é suportado apenas no Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um MiB mínimo de 2048.
- O serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) não suporta pontos finais do servidor localizados num volume que tenha o diretório de informação de volume do sistema (SVI) comprimido. Esta configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- O antivírus, a cópia de segurança e outras aplicações que acedam a ficheiros em camadas podem causar uma revogação indesejável, a menos que respeitem o atributo offline e ignorem a leitura do conteúdo desses ficheiros. Para mais informações, consulte [Troubleshoot Azure File Sync](storage-sync-files-troubleshoot.md).
- Os ecrãs de ficheiros do Gestor de Recursos do Servidor de Ficheiros (FSRM) podem causar falhas de sincronização intermináveis quando os ficheiros são bloqueados devido ao ecrã de ficheiros.
- A sysprep de execução num servidor que tenha o agente Dessincronizado de Ficheiros Azure instalado não é suportada e pode levar a resultados inesperados. O agente Dessincronização de ficheiros Azure deve ser instalado após a implementação da imagem do servidor e a configuração de mini-configuração de sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os itens seguintes não são sincronizados, mas o restante sistema continua a funcionar normalmente:
- Ficheiros com caracteres não suportados. Consulte o guia de resolução de [problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para lista de caracteres não suportados.
- Ficheiros ou diretórios que terminam com um período.
- Caminhos com mais de 2048 carateres.
- A parte da lista de controlo de acesso discricionário (DACL) de um descritor de segurança se for superior a 2 KB. (Este problema aplica-se apenas quando tem mais do que cerca de 40 registos de controlo de acesso (ACEs) num único item.)
- A parte da lista de controlo de acesso do sistema (SACL) de um descritor de segurança utilizado para auditoria.
- Atributos expandidos.
- Fluxos de dados alternados.
- Pontos de reanálise.
- Ligações fixas.
- A compressão (se estiver definida num ficheiro de servidor) não é mantida quando as alterações são sincronizadas nesse ficheiro a partir de outros pontos finais.
- Qualquer ficheiro encriptado com EFS (ou outra encriptação do modo de utilizador) que impeça a leitura dos dados por parte do serviço.

    > [!Note]  
    > O Azure File Sync encripta sempre os dados em trânsito. Os dados são sempre encriptados quando estão inativos no Azure.
 
### <a name="server-endpoint"></a>Ponto final do servidor
- Só é possível criar um ponto final do servidor num volume NTFS. O ReFS, FAT, FAT32 e outros sistemas de ficheiros não são atualmente suportados pelo Azure File Sync.
- Os ficheiros hierárquicos tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de apagarem o ponto final do servidor. Para restaurar o acesso aos ficheiros, recrie o ponto final do servidor. Se passaram 30 dias desde que o ponto final do servidor foi eliminado ou se o ponto final da nuvem foi eliminado, os ficheiros hierárquicos que não foram recolhidos serão inutilizáveis. Para saber mais, consulte que [os ficheiros Tiered não estão acessíveis no servidor depois](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)de apagar um ponto final do servidor .
- O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.
- O Clustering de Ativação Pós-falha só é suportado com discos em cluster, mas não com Volumes Partilhados de Cluster (CSVs).
- Não é possível aninhar um ponto final do servidor. Pode coexistir no mesmo volume em paralelo com outro ponto final.
- Não guarde um ficheiro de atenção de sistema operativo ou de aplicação dentro de uma localização final do servidor.
- O nome do servidor no portal não é atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efetuadas na partilha de ficheiros Azure têm primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de mudanças é iniciado para um ponto final de nuvem uma vez a cada 24 horas. Para sincronizar imediatamente os ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet [DeDetecção De Deteção De Deteção De Deteção De Deteção Invocpor-AzStorageSyncChange](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) Pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Além disso, as alterações introduzidas numa partilha de ficheiros Azure sobre o protocolo REST não atualizarão o SMB no último tempo modificado e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento podem ser transferidos para um grupo de recursos diferente ou subscrição dentro do atual inquilino da AD Azure. Se a conta de armazenamento for movida, tem de dar ao Serviço de Sincronização de Ficheiros Híbridos acesso à conta de armazenamento (ver [Certifique-se](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)de que o Ficheiro Sync do Azure tem acesso à conta de armazenamento).

    > [!Note]  
    > O Azure File Sync não suporta a deslocação da subscrição para um inquilino Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os selos de tempo do ficheiro. Isto garantirá que os ficheiros mais antigos são hierárquicos mais cedo do que os ficheiros recentemente acedidos.
- Os ficheiros podem não ser nivelado se o ficheiro de página.sys estiver localizado num volume que tenha o tiering em nuvem ativado. O pagefile.sys deve ser localizado num volume que tenha o nível de nuvem desativado.

## <a name="agent-version-8000"></a>Versão do agente 8.0.0.0
As seguintes notas de lançamento são para a versão 8.0.0.0 do agente Azure File Sync (lançado a 8 de outubro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e questões que são corrigidas

- Restaurar melhorias de desempenho
    - Tempos de recuperação mais rápidos para recuperação feita através do Azure Backup. Os ficheiros restaurados sincronizarão os servidores Do Ficheiro Sync do Azure muito mais rapidamente. 
- Melhor experiência do portal de tiering de nuvem  
    - Se tiver ficheiros hierárquicos que não estão a conseguir recordar, pode agora visualizar os erros de recuperação nas propriedades do ponto final do servidor. Além disso, a saúde do ponto final do servidor irá agora mostrar um erro e medidas de mitigação se o controlador de filtro de nível de nuvem não estiver carregado no servidor.
- Instalação de agente mais simples
    - O módulo Az\AzureRM PowerShell já não é necessário para registar o servidor tornando a instalação mais simples e rápida.
- Melhorias no desempenho e na fiabilidade diversas

### <a name="evaluation-tool"></a>Ferramenta de Avaliação
Antes de implementar o Azure File Sync, deve avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação De sincronização de ficheiros Azure. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão sem suporte de SO. Para instruções de instalação e utilização, consulte a secção ferramenta de [avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planeamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para obter mais informações sobre como instalar e configurar o agente Dessincronização de ficheiros Azure com o Windows Server, consulte o Planeamento para uma implementação de Sincronização de [Ficheiros Azure](storage-sync-files-planning.md) e como implementar o [Sync de Ficheiros Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões elevadas (administradoras).
- O agente não é suportado na opção de implementação do Nano Server.
- O agente é suportado apenas no Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um MiB mínimo de 2048.
- O serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) não suporta pontos finais do servidor localizados num volume que tenha o diretório de informação de volume do sistema (SVI) comprimido. Esta configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- O antivírus, a cópia de segurança e outras aplicações que acedam a ficheiros em camadas podem causar uma revogação indesejável, a menos que respeitem o atributo offline e ignorem a leitura do conteúdo desses ficheiros. Para mais informações, consulte [Troubleshoot Azure File Sync](storage-sync-files-troubleshoot.md).
- Os ecrãs de ficheiros do Gestor de Recursos do Servidor de Ficheiros (FSRM) podem causar falhas de sincronização intermináveis quando os ficheiros são bloqueados devido ao ecrã de ficheiros.
- A sysprep de execução num servidor que tenha o agente Dessincronizado de Ficheiros Azure instalado não é suportada e pode levar a resultados inesperados. O agente Dessincronização de ficheiros Azure deve ser instalado após a implementação da imagem do servidor e a configuração de mini-configuração de sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os itens seguintes não são sincronizados, mas o restante sistema continua a funcionar normalmente:
- Ficheiros com caracteres não suportados. Consulte o guia de resolução de [problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para lista de caracteres não suportados.
- Ficheiros ou diretórios que terminam com um período.
- Caminhos com mais de 2048 carateres.
- A parte da lista de controlo de acesso discricionário (DACL) de um descritor de segurança se for superior a 2 KB. (Este problema aplica-se apenas quando tem mais do que cerca de 40 registos de controlo de acesso (ACEs) num único item.)
- A parte da lista de controlo de acesso do sistema (SACL) de um descritor de segurança utilizado para auditoria.
- Atributos expandidos.
- Fluxos de dados alternados.
- Pontos de reanálise.
- Ligações fixas.
- A compressão (se estiver definida num ficheiro de servidor) não é mantida quando as alterações são sincronizadas nesse ficheiro a partir de outros pontos finais.
- Qualquer ficheiro encriptado com EFS (ou outra encriptação do modo de utilizador) que impeça a leitura dos dados por parte do serviço.

    > [!Note]  
    > O Azure File Sync encripta sempre os dados em trânsito. Os dados são sempre encriptados quando estão inativos no Azure.
 
### <a name="server-endpoint"></a>Ponto final do servidor
- Só é possível criar um ponto final do servidor num volume NTFS. O ReFS, FAT, FAT32 e outros sistemas de ficheiros não são atualmente suportados pelo Azure File Sync.
- Os ficheiros hierárquicos tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de apagarem o ponto final do servidor. Para restaurar o acesso aos ficheiros, recrie o ponto final do servidor. Se passaram 30 dias desde que o ponto final do servidor foi eliminado ou se o ponto final da nuvem foi eliminado, os ficheiros hierárquicos que não foram recolhidos serão inutilizáveis. Para saber mais, consulte que [os ficheiros Tiered não estão acessíveis no servidor depois](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)de apagar um ponto final do servidor .
- O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.
- O Clustering de Ativação Pós-falha só é suportado com discos em cluster, mas não com Volumes Partilhados de Cluster (CSVs).
- Não é possível aninhar um ponto final do servidor. Pode coexistir no mesmo volume em paralelo com outro ponto final.
- Não guarde um ficheiro de atenção de sistema operativo ou de aplicação dentro de uma localização final do servidor.
- O nome do servidor no portal não é atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efetuadas na partilha de ficheiros Azure têm primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de mudanças é iniciado para um ponto final de nuvem uma vez a cada 24 horas. Para sincronizar imediatamente os ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet [DeDetecção De Deteção De Deteção De Deteção De Deteção Invocpor-AzStorageSyncChange](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) Pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Além disso, as alterações introduzidas numa partilha de ficheiros Azure sobre o protocolo REST não atualizarão o SMB no último tempo modificado e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento podem ser transferidos para um grupo de recursos diferente ou subscrição dentro do atual inquilino da AD Azure. Se a conta de armazenamento for movida, tem de dar ao Serviço de Sincronização de Ficheiros Híbridos acesso à conta de armazenamento (ver [Certifique-se](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)de que o Ficheiro Sync do Azure tem acesso à conta de armazenamento).

    > [!Note]  
    > O Azure File Sync não suporta a deslocação da subscrição para um inquilino Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os selos de tempo do ficheiro. Isto garantirá que os ficheiros mais antigos são hierárquicos mais cedo do que os ficheiros recentemente acedidos.

## <a name="agent-version-7200"></a>Versão do agente 7.2.0.0
As seguintes notas de lançamento são para a versão 7.2.0.0 do agente Azure File Sync lançado a 24 de julho de 2019. Estas notas são além das notas de lançamento listadas para a versão 7.0.0.0.

Lista de questões corrigidas nesta versão:  
- O Agente de Sincronização de Armazenamento (FileSyncSvc) falha se a configuração proxy for nula.
- O ponto final do servidor iniciará o BCDR (erro 0x80c80257 - ECS_E_BCDR_IN_PROGRESS) se vários pontos finais no servidor tiverem o mesmo nome.
- Melhorias na fiabilidade do tiering da nuvem.

## <a name="agent-version-7100"></a>Versão do agente 7.1.0.0
As seguintes notas de lançamento são para a versão 7.1.0.0 do agente Azure File Sync lançado a 12 de julho de 2019. Estas notas são além das notas de lançamento listadas para a versão 7.0.0.0.

Lista de questões corrigidas nesta versão:  
- Aceder ou navegar num ponto final do servidor sobre o SMB é lento no Windows Server 2012 R2. 
- Aumento da utilização do CPU após a instalação do agente Azure File Sync v6.
- Melhorias na telemetria de nível de nuvem.
- Melhorias de fiabilidade diversas para o tiering e sincronização da nuvem.

## <a name="agent-version-7000"></a>Versão do agente 7.0.0.0
As seguintes notas de lançamento são para a versão 7.0.0.0 do agente Azure File Sync (lançado a 19 de junho de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e questões que são corrigidas

- Suporte para maiores tamanhos de partilha de ficheiros
    - Com a pré-visualização de maiores ações de ficheiros Azure, estamos a aumentar os nossos limites de suporte para sincronização de ficheiros também. Neste primeiro passo, o Azure File Sync suporta agora até 25 TB e 50 milhões de ficheiros num único espaço de nome sincronizado. Para se candidatar à pré-visualização https://aka.ms/azurefilesatscalesurveyde partilha de ficheiros, preencha este formulário . 
- Suporte para firewall e configuração de rede virtual em contas de armazenamento
    - O Azure File Sync suporta agora a definição de firewall e rede virtual nas contas de armazenamento. Para configurar a sua implementação para funcionar com a firewall e a definição de rede virtual, consulte configurações de [firewall configure e redes virtuais](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- PowerShell cmdlet para sincronizar imediatamente ficheiros alterados na partilha de ficheiros Azure
    - Para sincronizar imediatamente os ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet DeDetecção De Deteção De Deteção De Deteção De Deteção Invocpor-AzStorageSyncChange Pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Este cmdlet destina-se a cenários em que algum tipo de processo automatizado esteja a fazer alterações na partilha de ficheiros Azure ou as alterações são feitas por um administrador (como transferir ficheiros e diretórios para a partilha). Para alterações no utilizador final, a recomendação é instalar o agente Dessincronização de ficheiros Azure num VM IaaS e ter os utilizadores finais a aceder à partilha de ficheiros através do VM IaaS. Desta forma, todas as alterações sincronizar-se-ão rapidamente com outros agentes sem a necessidade de utilizar o cmdlet Invoke-AzStorageSyncChangeDetection. Para saber mais, consulte a documentação [Invoke-AzStorageSyncChangeDetection.](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
- Experiência melhorada do portal se encontrar ficheiros que não estão sincronizados
    - Se tem ficheiros que não estão a sincronizar, agora diferenciamos os erros transitórios e persistentes no portal. Os erros transitórios normalmente resolvem-se sem a necessidade de ação administrativa. Por exemplo, um ficheiro que esteja atualmente em uso não sincronizará até que a pega do ficheiro esteja fechada. Para erros persistentes, mostramos agora o número de ficheiros impactados por cada erro. A contagem de erros persistente também é apresentada nos ficheiros que não sincronizam a coluna de todos os pontos finais do servidor num grupo de sincronização.
- Restauro melhorado do nível de ficheiro sintetário azure
    - Os ficheiros individuais restaurados utilizando o Azure Backup são agora detetados e sincronizados com o ponto final do servidor mais rapidamente.
- Melhoria do tiering de nível de nuvem recorda a fiabilidade do cmdlet 
    - O cmdlet Invoke-StorageSyncFileRecall agora permite que os clientes especifiquem por contagem de retry de ficheiros e por atraso de retry de ficheiro semelhante à robocopia. Anteriormente, este cmdlet recordaria todos os ficheiros hierárquicos sob um determinado caminho por ordem aleatória. Com o novo parâmetro -Ordem, este cmdlet recordará primeiro os dados mais quentes e honrará a política de tiering em nuvem (pare de recordar se a política de data é cumprida ou se o espaço livre de volume é cumprido; o que acontecer primeiro).
- Suporte apenas para TLS 1.2 (TLS 1.0 e 1.1 está desativado)
    - O Azure File Sync suporta agora a utilização de TLS 1.2 apenas em servidores com TLS 1.0 e 1.1 desativados. Antes desta melhoria, o registo do servidor falharia se o TLS 1.0 e 1.1 fossem desativados no servidor.
- Melhorias de desempenho e fiabilidade diversas para sincronização e tiering em nuvem
    - Existem várias melhorias de fiabilidade e desempenho nesta versão. Alguns deles são direcionados para tornar o tiering de nuvem mais eficiente e o Azure File Sync como um todo funciona melhor nessas situações quando você tem um conjunto de horários de estrangulamento de largura de banda.

### <a name="evaluation-tool"></a>Ferramenta de Avaliação
Antes de implementar o Azure File Sync, deve avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação De sincronização de ficheiros Azure. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão sem suporte de SO. Para instruções de instalação e utilização, consulte a secção ferramenta de [avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planeamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para obter mais informações sobre como instalar e configurar o agente Dessincronização de ficheiros Azure com o Windows Server, consulte o Planeamento para uma implementação de Sincronização de [Ficheiros Azure](storage-sync-files-planning.md) e como implementar o [Sync de Ficheiros Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões elevadas (administradoras).
- O agente não é suportado na opção de implementação do Nano Server.
- O agente é suportado apenas no Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um MiB mínimo de 2048.
- O serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) não suporta pontos finais do servidor localizados num volume que tenha o diretório de informação de volume do sistema (SVI) comprimido. Esta configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- O antivírus, a cópia de segurança e outras aplicações que acedam a ficheiros em camadas podem causar uma revogação indesejável, a menos que respeitem o atributo offline e ignorem a leitura do conteúdo desses ficheiros. Para mais informações, consulte [Troubleshoot Azure File Sync](storage-sync-files-troubleshoot.md).
- Os ecrãs de ficheiros do Gestor de Recursos do Servidor de Ficheiros (FSRM) podem causar falhas de sincronização intermináveis quando os ficheiros são bloqueados devido ao ecrã de ficheiros.
- A sysprep de execução num servidor que tenha o agente Dessincronizado de Ficheiros Azure instalado não é suportada e pode levar a resultados inesperados. O agente Dessincronização de ficheiros Azure deve ser instalado após a implementação da imagem do servidor e a configuração de mini-configuração de sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os itens seguintes não são sincronizados, mas o restante sistema continua a funcionar normalmente:
- Ficheiros com caracteres não suportados. Consulte o guia de resolução de [problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para lista de caracteres não suportados.
- Ficheiros ou diretórios que terminam com um período.
- Caminhos com mais de 2048 carateres.
- A parte da lista de controlo de acesso discricionário (DACL) de um descritor de segurança se for superior a 2 KB. (Este problema aplica-se apenas quando tem mais do que cerca de 40 registos de controlo de acesso (ACEs) num único item.)
- A parte da lista de controlo de acesso do sistema (SACL) de um descritor de segurança utilizado para auditoria.
- Atributos expandidos.
- Fluxos de dados alternados.
- Pontos de reanálise.
- Ligações fixas.
- A compressão (se estiver definida num ficheiro de servidor) não é mantida quando as alterações são sincronizadas nesse ficheiro a partir de outros pontos finais.
- Qualquer ficheiro encriptado com EFS (ou outra encriptação do modo de utilizador) que impeça a leitura dos dados por parte do serviço.

    > [!Note]  
    > O Azure File Sync encripta sempre os dados em trânsito. Os dados são sempre encriptados quando estão inativos no Azure.
 
### <a name="server-endpoint"></a>Ponto final do servidor
- Só é possível criar um ponto final do servidor num volume NTFS. O ReFS, FAT, FAT32 e outros sistemas de ficheiros não são atualmente suportados pelo Azure File Sync.
- Os ficheiros hierárquicos tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de apagarem o ponto final do servidor. Para restaurar o acesso aos ficheiros, recrie o ponto final do servidor. Se passaram 30 dias desde que o ponto final do servidor foi eliminado ou se o ponto final da nuvem foi eliminado, os ficheiros hierárquicos que não foram recolhidos serão inutilizáveis.
- O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.
- O Clustering de Ativação Pós-falha só é suportado com discos em cluster, mas não com Volumes Partilhados de Cluster (CSVs).
- Não é possível aninhar um ponto final do servidor. Pode coexistir no mesmo volume em paralelo com outro ponto final.
- Não guarde um ficheiro de atenção de sistema operativo ou de aplicação dentro de uma localização final do servidor.
- O nome do servidor no portal não é atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efetuadas na partilha de ficheiros Azure têm primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de mudanças é iniciado para um ponto final de nuvem uma vez a cada 24 horas. Além disso, as alterações introduzidas numa partilha de ficheiros Azure sobre o protocolo REST não atualizarão o SMB no último tempo modificado e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento podem ser transferidos para um grupo de recursos diferente ou subscrição dentro do atual inquilino da AD Azure. Se a conta de armazenamento for movida, tem de dar ao Serviço de Sincronização de Ficheiros Híbridos acesso à conta de armazenamento (ver [Certifique-se](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)de que o Ficheiro Sync do Azure tem acesso à conta de armazenamento).

    > [!Note]  
    > O Azure File Sync não suporta a deslocação da subscrição para um inquilino Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os selos de tempo do ficheiro. Isto garantirá que os ficheiros mais antigos são hierárquicos mais cedo do que os ficheiros recentemente acedidos.

## <a name="agent-version-6300"></a>Versão do agente 6.3.0.0
As seguintes notas de lançamento são para a versão 6.3.0.0 do agente Azure File Sync lançado a 27 de junho de 2019. Estas notas são além das notas de lançamento listadas para a versão 6.0.0.0.

Lista de questões corrigidas nesta versão:  
- Aceder ou navegar num ponto final do servidor sobre o SMB é lento no Windows Server 2012 R2 
- Maior utilização do CPU após a instalação do agente Azure File Sync v6
- Melhorias da telemetria de nível de nuvem

## <a name="agent-version-6200"></a>Versão do agente 6.2.0.0
As seguintes notas de lançamento são para a versão 6.2.0.0 do agente Azure File Sync lançado a 13 de junho de 2019. Estas notas são além das notas de lançamento listadas para a versão 6.0.0.0.

Lista de questões corrigidas nesta versão:  
- Depois de criar um ponto final do servidor, o uso de CPU elevado pode ocorrer quando a recolha de fundos está a descarregar ficheiros para o servidor
- As operações de sincronização e de nidamento de nuvem podem falhar com erros ECS_E_SERVER_CREDENTIAL_NEEDED devido à expiração do token
- Recordar um ficheiro pode falhar se o URL para descarregar o ficheiro contiver caracteres reservados 

## <a name="agent-version-6100"></a>Versão do agente 6.1.0.0
As seguintes notas de lançamento são para a versão 6.1.0.0 do agente Azure File Sync lançado a 6 de maio de 2019. Estas notas são além das notas de lançamento listadas para a versão 6.0.0.0.

Lista de questões corrigidas nesta versão:  
- O Windows Admin Center não consegue visualizar a versão do agente e a configuração de ponto final do servidor nos servidores que têm o agente Dom Sincronia do Ficheiro Azure na versão 6.0 instalada.

## <a name="agent-version-6000"></a>Versão do agente 6.0.0.0
As seguintes notas de lançamento são para a versão 6.0.0.0 do agente Azure File Sync (lançado a 22 de abril de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e questões que são corrigidas

- Suporte de atualização automática do agente
  - Ouvimos o seu feedback e adicionámos uma funcionalidade de atualização automática no servidor do Servidor Azure File Sync. Para mais informações, consulte a política de atualização do [agente Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Suporte para acLs de partilha de ficheiros Azure
  - O Azure File Sync sempre apoiou a sincronização de ACLs entre os pontos finais do servidor, mas os ACLs não foram sincronizados com o ponto final da nuvem (partilha de ficheiros Azure). Esta versão adiciona suporte para sincronizar ACLs entre os pontos finais do servidor e da nuvem.
- Sessões de sincronização paralelas de upload e descarregamento para um ponto final do servidor 
  - Os pontos finais do servidor suportam agora o upload e o download de ficheiros ao mesmo tempo. Não há mais espera supor que um download seja concluído para que os ficheiros possam ser enviados para a partilha de ficheiros Azure. 
- Novos cmdlets de tiering cloud para obter volume e estado de tiering
  - Dois novos cmdlets powerShell locais de servidor podem agora ser usados para obter informações de tiering em nuvem e recolha de ficheiros. Disponibilizam informações de registo de dois canais de eventos no servidor:
    - Get-StorageSyncFileTieringResult listará todos os ficheiros e seus caminhos que não tenham sido hierárquicos e reporta a razão.
    - Get-StorageSyncFileRecallResult relata todos os eventos de recolha de ficheiros. Ele lista cada ficheiro recordado e o seu caminho, bem como o sucesso ou erro para essa recolha.
  - Por predefinição, ambos os canais de eventos podem armazenar até 1 MB cada – pode aumentar a quantidade de ficheiros reportados aumentando o tamanho do canal de eventos.
- Suporte para o modo FIPS
  - O Azure File Sync suporta agora o modo FIPS de ativação nos servidores que têm o agente Dessincronizado de Ficheiros Azure instalado.
    - Antes de ativar o modo FIPS no seu servidor, instale o agente Desincronização de Ficheiros Azure e o [módulo De gestão](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) de pacotes no seu servidor. Se o FIPS já estiver ativado no servidor, [descarregue manualmente](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) o [módulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) para o seu servidor.
- Melhorias de fiabilidade diversas para o tiering e sincronização da nuvem

### <a name="evaluation-tool"></a>Ferramenta de Avaliação
Antes de implementar o Azure File Sync, deve avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação De sincronização de ficheiros Azure. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão sem suporte de SO. Para instruções de instalação e utilização, consulte a secção ferramenta de [avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planeamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para obter mais informações sobre como instalar e configurar o agente Dessincronização de ficheiros Azure com o Windows Server, consulte o Planeamento para uma implementação de Sincronização de [Ficheiros Azure](storage-sync-files-planning.md) e como implementar o [Sync de Ficheiros Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões elevadas (administradoras).
- O agente não é suportado na opção de implementação do Nano Server.
- O agente é suportado apenas no Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um MiB mínimo de 2048.
- O serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) não suporta pontos finais do servidor localizados num volume que tenha o diretório de informação de volume do sistema (SVI) comprimido. Esta configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- O antivírus, a cópia de segurança e outras aplicações que acedam a ficheiros em camadas podem causar uma revogação indesejável, a menos que respeitem o atributo offline e ignorem a leitura do conteúdo desses ficheiros. Para mais informações, consulte [Troubleshoot Azure File Sync](storage-sync-files-troubleshoot.md).
- Os ecrãs de ficheiros do Gestor de Recursos do Servidor de Ficheiros (FSRM) podem causar falhas de sincronização intermináveis quando os ficheiros são bloqueados devido ao ecrã de ficheiros.
- A sysprep de execução num servidor que tenha o agente Dessincronizado de Ficheiros Azure instalado não é suportada e pode levar a resultados inesperados. O agente Dessincronização de ficheiros Azure deve ser instalado após a implementação da imagem do servidor e a configuração de mini-configuração de sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os itens seguintes não são sincronizados, mas o restante sistema continua a funcionar normalmente:
- Ficheiros com caracteres não suportados. Consulte o guia de resolução de [problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para lista de caracteres não suportados.
- Ficheiros ou diretórios que terminam com um período.
- Caminhos com mais de 2048 carateres.
- A parte da lista de controlo de acesso discricionário (DACL) de um descritor de segurança se for superior a 2 KB. (Este problema aplica-se apenas quando tem mais do que cerca de 40 registos de controlo de acesso (ACEs) num único item.)
- A parte da lista de controlo de acesso do sistema (SACL) de um descritor de segurança utilizado para auditoria.
- Atributos expandidos.
- Fluxos de dados alternados.
- Pontos de reanálise.
- Ligações fixas.
- A compressão (se estiver definida num ficheiro de servidor) não é mantida quando as alterações são sincronizadas nesse ficheiro a partir de outros pontos finais.
- Qualquer ficheiro encriptado com EFS (ou outra encriptação do modo de utilizador) que impeça a leitura dos dados por parte do serviço.

    > [!Note]  
    > O Azure File Sync encripta sempre os dados em trânsito. Os dados são sempre encriptados quando estão inativos no Azure.
 
### <a name="server-endpoint"></a>Ponto final do servidor
- Só é possível criar um ponto final do servidor num volume NTFS. O ReFS, FAT, FAT32 e outros sistemas de ficheiros não são atualmente suportados pelo Azure File Sync.
- Os ficheiros hierárquicos tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de apagarem o ponto final do servidor. Para restaurar o acesso aos ficheiros, recrie o ponto final do servidor. Se passaram 30 dias desde que o ponto final do servidor foi eliminado ou se o ponto final da nuvem foi eliminado, os ficheiros hierárquicos que não foram recolhidos serão inutilizáveis.
- O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.
- O Clustering de Ativação Pós-falha só é suportado com discos em cluster, mas não com Volumes Partilhados de Cluster (CSVs).
- Não é possível aninhar um ponto final do servidor. Pode coexistir no mesmo volume em paralelo com outro ponto final.
- Não guarde um ficheiro de atenção de sistema operativo ou de aplicação dentro de uma localização final do servidor.
- O nome do servidor no portal não é atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efetuadas na partilha de ficheiros Azure têm primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de mudanças é iniciado para um ponto final de nuvem uma vez a cada 24 horas. Além disso, as alterações introduzidas numa partilha de ficheiros Azure sobre o protocolo REST não atualizarão o SMB no último tempo modificado e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento podem ser transferidos para um grupo de recursos diferente ou subscrição dentro do atual inquilino da AD Azure. Se a conta de armazenamento for movida, tem de dar ao Serviço de Sincronização de Ficheiros Híbridos acesso à conta de armazenamento (ver [Certifique-se](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)de que o Ficheiro Sync do Azure tem acesso à conta de armazenamento).

    > [!Note]  
    > O Azure File Sync não suporta a deslocação da subscrição para um inquilino Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os selos de tempo do ficheiro. Isto garantirá que os ficheiros mais antigos são hierárquicos mais cedo do que os ficheiros recentemente acedidos.
- Ao visualizar as propriedades do ficheiro a partir de um cliente SMB, o atributo offline pode parecer estar definido incorretamente devido à colocação em cache dos metadados de ficheiros por parte do SMB.

## <a name="agent-version-5200"></a>Versão do agente 5.2.0.0
As seguintes notas de lançamento são para a versão 5.2.0.0 do agente Azure File Sync lançado a 4 de abril de 2019. Estas notas são além das notas de lançamento listadas para a versão 5.0.2.0.

Lista de questões corrigidas nesta versão:  
- Melhorias de fiabilidade para funcionalidades de transferência de dados offline e transferência de dados
- Melhorias da telemetria sincronizada

## <a name="agent-version-5100"></a>Versão do agente 5.1.0.0
As seguintes notas de lançamento são para a versão 5.1.0.0 do agente Azure File Sync lançado a 7 de março de 2019. Estas notas são além das notas de lançamento listadas para a versão 5.0.2.0.

Lista de questões corrigidas nesta versão:  
- Os ficheiros podem falhar na sincronização com o erro 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) se a enumeração da alteração estiver a falhar no servidor
- Se uma sessão de sincronização ou ficheiro receber um erro 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), o sync irá agora voltar a tentar a operação.
- Os ficheiros podem não sincronizar com o erro 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Pode ocorrer um elevado uso da memória ao recordar ficheiros
- Melhorias da telemetria de nível de nuvem 

## <a name="agent-version-5020"></a>Versão do agente 5.0.2.0
As seguintes notas de lançamento são para a versão 5.0.2.0 do agente Azure File Sync (lançado a 12 de fevereiro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e questões que são corrigidas

- Apoio à nuvem do Governo de Azure
  - Acrescentámos apoio de pré-visualização à nuvem do Governo de Azure. Isto requer uma subscrição listada em branco e um download de um agente especial da Microsoft. Para ter acesso à pré-visualização, [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)por favor envie-nos um e-mail diretamente para .
- Suporte para Deduplicação de Dados
    - A deduplicação de dados está agora totalmente suportada com o tiering em nuvem ativado no Windows Server 2016 e no Windows Server 2019. Permitir a duplicação num volume com tiering em nuvem ativado permite-lhe guardar mais ficheiros no local sem fornecer mais armazenamento.
- Suporte para transferência de dados offline (por exemplo, via Data Box)
    - Migrar facilmente grandes quantidades de dados para o Azure File Sync através de quaisquer meios que escolha. Pode escolher a Azure Data Box, a AzCopy e até serviços de migração de terceiros. Não é necessário utilizar quantidades massivas de largura de banda para obter os seus dados no Azure, no caso da Data Box – basta enviá-lo para lá! Para saber mais, consulte [Offline Data Transfer Docs](https://aka.ms/AFS/OfflineDataTransfer).
- Melhor desempenho de sincronização
    - Os clientes com vários pontos finais do servidor no mesmo volume podem ter experimentado um desempenho de sincronização lenta antes desta versão. O Azure File Sync cria uma imagem de VSS temporária uma vez por dia no servidor para sincronizar ficheiros que tenham cabos abertos. O Sync suporta agora vários pontos finais do servidor sincronizando num volume quando uma sessão de sincronização VSS está ativa. Não há mais espera para que uma sessão de sincronização VSS termine para que a sincronização possa ser retomada em outros pontos finais do servidor no volume.
- Melhor monitorização no portal
    - Foram adicionados gráficos no portal do Serviço de Sincronização de Armazenamento para visualização:
        - Número de ficheiros sincronizados
        - Dimensão dos dados transferidos
        - Número de ficheiros que não sincronizam
        - Tamanho dos dados recordados
        - Estado de conectividade do servidor
    - Para saber mais, consulte [monitor Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Melhor escalabilidade e fiabilidade
    - O número máximo de objetos do sistema de ficheiros (diretórios e ficheiros) num diretório aumentou para 1.000.000. O limite anterior era de 200.000.
    - Sync tentará retomar a transferência de dados em vez de retransmitir quando uma transferência é interrompida para ficheiros grandes 

### <a name="evaluation-tool"></a>Ferramenta de Avaliação
Antes de implementar o Azure File Sync, deve avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação De sincronização de ficheiros Azure. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão sem suporte de SO. Para instruções de instalação e utilização, consulte a secção ferramenta de [avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planeamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para obter mais informações sobre como instalar e configurar o agente Dessincronização de ficheiros Azure com o Windows Server, consulte o Planeamento para uma implementação de Sincronização de [Ficheiros Azure](storage-sync-files-planning.md) e como implementar o [Sync de Ficheiros Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões elevadas (administradoras).
- O agente não é suportado nas opções de implementação do Windows Server Core ou Nano Server.
- O agente é suportado apenas no Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um MiB mínimo de 2048.
- O serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) não suporta pontos finais do servidor localizados num volume que tenha o diretório de informação de volume do sistema (SVI) comprimido. Esta configuração levará a resultados inesperados.
- O modo FIPS não é suportado e deve ser desativado. 

### <a name="interoperability"></a>Interoperabilidade
- O antivírus, a cópia de segurança e outras aplicações que acedam a ficheiros em camadas podem causar uma revogação indesejável, a menos que respeitem o atributo offline e ignorem a leitura do conteúdo desses ficheiros. Para mais informações, consulte [Troubleshoot Azure File Sync](storage-sync-files-troubleshoot.md).
- Os ecrãs de ficheiros do Gestor de Recursos do Servidor de Ficheiros (FSRM) podem causar falhas de sincronização intermináveis quando os ficheiros são bloqueados devido ao ecrã de ficheiros.
- A sysprep de execução num servidor que tenha o agente Dessincronizado de Ficheiros Azure instalado não é suportada e pode levar a resultados inesperados. O agente Dessincronização de ficheiros Azure deve ser instalado após a implementação da imagem do servidor e a configuração de mini-configuração de sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os itens seguintes não são sincronizados, mas o restante sistema continua a funcionar normalmente:
- Ficheiros com caracteres não suportados. Consulte o guia de resolução de [problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para lista de caracteres não suportados.
- Ficheiros ou diretórios que terminam com um período.
- Caminhos com mais de 2048 carateres.
- A parte da lista de controlo de acesso discricionário (DACL) de um descritor de segurança se for superior a 2 KB. (Este problema aplica-se apenas quando tem mais do que cerca de 40 registos de controlo de acesso (ACEs) num único item.)
- A parte da lista de controlo de acesso do sistema (SACL) de um descritor de segurança utilizado para auditoria.
- Atributos expandidos.
- Fluxos de dados alternados.
- Pontos de reanálise.
- Ligações fixas.
- A compressão (se estiver definida num ficheiro de servidor) não é mantida quando as alterações são sincronizadas nesse ficheiro a partir de outros pontos finais.
- Qualquer ficheiro encriptado com EFS (ou outra encriptação do modo de utilizador) que impeça a leitura dos dados por parte do serviço.

    > [!Note]  
    > O Azure File Sync encripta sempre os dados em trânsito. Os dados são sempre encriptados quando estão inativos no Azure.
 
### <a name="server-endpoint"></a>Ponto final do servidor
- Só é possível criar um ponto final do servidor num volume NTFS. O ReFS, FAT, FAT32 e outros sistemas de ficheiros não são atualmente suportados pelo Azure File Sync.
- Os ficheiros hierárquicos tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de apagarem o ponto final do servidor. Para restaurar o acesso aos ficheiros, recrie o ponto final do servidor. Se passaram 30 dias desde que o ponto final do servidor foi eliminado ou se o ponto final da nuvem foi eliminado, os ficheiros hierárquicos que não foram recolhidos serão inutilizáveis.
- O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.
- O Clustering de Ativação Pós-falha só é suportado com discos em cluster, mas não com Volumes Partilhados de Cluster (CSVs).
- Não é possível aninhar um ponto final do servidor. Pode coexistir no mesmo volume em paralelo com outro ponto final.
- Não guarde um ficheiro de atenção de sistema operativo ou de aplicação dentro de uma localização final do servidor.
- O nome do servidor no portal não é atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efetuadas na partilha de ficheiros Azure têm primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de mudanças é iniciado para um ponto final de nuvem uma vez a cada 24 horas. Além disso, as alterações introduzidas numa partilha de ficheiros Azure sobre o protocolo REST não atualizarão o SMB no último tempo modificado e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento podem ser transferidos para um grupo de recursos diferente ou subscrição dentro do atual inquilino da AD Azure. Se a conta de armazenamento for movida, tem de dar ao Serviço de Sincronização de Ficheiros Híbridos acesso à conta de armazenamento (ver [Certifique-se](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)de que o Ficheiro Sync do Azure tem acesso à conta de armazenamento).

    > [!Note]  
    > O Azure File Sync não suporta a deslocação da subscrição para um inquilino Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os selos de tempo do ficheiro. Isto garantirá que os ficheiros mais antigos são hierárquicos mais cedo do que os ficheiros recentemente acedidos.
- Ao visualizar as propriedades do ficheiro a partir de um cliente SMB, o atributo offline pode parecer estar definido incorretamente devido à colocação em cache dos metadados de ficheiros por parte do SMB.
