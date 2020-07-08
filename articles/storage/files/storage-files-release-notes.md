---
title: Notas de lançamento para o agente Azure File Sync Microsoft Docs
description: Lançar notas para o agente Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 6/26/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 54a7f3f50de27747ab15f6895ebfb4f65faf5fdf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85484065"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notas de versão do agente do Azure File Sync
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. As suas instalações do Windows Server são transformadas numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS). Pode ter o número de caches que precisar em todo o mundo.

Este artigo disponibiliza as notas de versão das versões suportadas do agente do Azure File Sync.

## <a name="supported-versions"></a>Versões suportadas
São suportadas as seguintes versões para o agente do Azure File Sync:

| Marco | Número de versão do agente | Data da versão | Estado |
|----|----------------------|--------------|------------------|
| V10.1 Lançamento - [KB4522411](https://support.microsoft.com/en-us/help/4522411)| 10.1.0.0 | 5 de junho de 2020 | Suportado - Voo |
| Rollup de atualização de maio de 2020 - [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 de maio de 2020 | Suportado |
| V10 Release - [KB4522409](https://support.microsoft.com/en-us/help/4522409)| 10.0.0.0 | 9 de abril de 2020 | Suportado |
| Rollup de atualização de dezembro de 2019 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 de dezembro de 2019 | Suportado |
| Lançamento V9 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 de dezembro de 2019 | Suportado |
| Lançamento V8 - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 de outubro de 2019 | Suportado |
| Rollup de atualização de julho de 2019 - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 de julho de 2019 | Suportado - Versão do agente expirará a 1 de setembro de 2020 |
| Rollup de atualização de julho de 2019 - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 de julho de 2019 | Suportado - Versão do agente expirará a 1 de setembro de 2020 |
| V7 Release - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 de junho de 2019 | Suportado - Versão do agente expirará a 1 de setembro de 2020 |
| Lançamento V6 | 6.0.0.0 - 6.3.0.0 | N/D | Não Suportado - Versões de agente expiradas em 21 de abril de 2020 |
| Lançamento V5 | 5.0.2.0 - 5.2.0.0 | N/D | Não Suportado - Versões de agente expiradas em 18 de março de 2020 |
| Lançamento V4 | 4.0.1.0 - 4.3.0.0 | N/D | Não Suportado - Versões de agente expiradas em 6 de novembro de 2019 |
| Lançamento V3 | 3.1.0.0 - 3.4.0.0 | N/D | Não Suportado - Versões de agente expiradas em 19 de agosto de 2019 |
| Agentes pré-GA | 1.1.0.0 - 3.0.13.0 | N/D | Não Suportado - Versões de agente expiradas a 1 de outubro de 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente do Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10100"></a>Versão agente 10.1.0.0
As seguintes notas de lançamento são para a versão 10.1.0.0 do agente Azure File Sync lançado a 5 de junho de 2020. Estas notas juntam-se às notas de lançamento listadas para as versões 10.0.0.0 e 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e problemas que são corrigidos

- Suporte de ponto final privado Azure
    - O tráfego de sincronização para o Serviço de Sincronização de Armazenamento pode agora ser enviado para um ponto final privado. Isto permite fazer um túnel sobre uma ligação ExpressRoute ou VPN. Para saber mais, consulte [pontos finais da rede De Sincronização de Ficheiros Azure.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-networking-endpoints)
- A métrica sincronizada de ficheiros irá agora apresentar o progresso enquanto uma grande sincronização está em execução, em vez de no final.
- Melhorias de fiabilidade diversas para instalação de agentes, tiering de nuvens, sincronização e telemetria

## <a name="agent-version-10020"></a>Versão agente 10.0.2.0
As seguintes notas de lançamento são para a versão 10.0.2.0 do agente Azure File Sync lançado a 19 de maio de 2020. Estas notas juntam-se às notas de lançamento listadas para a versão 10.0.0.0.

Emissão corrigida nesta versão:  
- O Agente de Sincronização de Armazenamento (FileSyncSvc) falha frequentemente após a instalação do agente Azure File Sync v10.

> [!Note]  
>Esta versão não foi lançada para servidores configurados para atualizar automaticamente quando uma nova versão fica disponível. Para instalar esta atualização, utilize o Microsoft Update ou o Microsoft Update Catalog (consulte [KB4522412](https://support.microsoft.com/help/4522412) para obter instruções de instalação).

## <a name="agent-version-10000"></a>Versão agente 10.0.0.0
As seguintes notas de lançamento são para a versão 10.0.0.0 do agente Azure File Sync (lançado a 9 de abril de 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e problemas que são corrigidos

- Progresso sincronizado melhorado no portal
    - Com o lançamento do agente V10, o portal Azure começará em breve a mostrar o tipo de sessão de sincronização que está a decorrer. Por exemplo, download inicial, download regular, recolha de antecedentes (casos rápidos de recuperação de desastres) e similares. 

- Experiência melhorada do portal de nivelamento de nuvens
    - Se tiver ficheiros que não estejam a nivelar ou a recordar, pode agora ver os erros de tiering nas propriedades do ponto final do servidor.
    - Informações adicionais sobre o tiering da nuvem estão disponíveis para um ponto final do servidor:
        - Tamanho da cache local
        - Eficiência de utilização da cache
        - Detalhes da política de nivelamento da nuvem: tamanho do volume, espaço livre atual ou o último tempo acedido do ficheiro mais antigo na cache local.
    - Estas alterações serão enviadas no portal Azure pouco depois do lançamento inicial do agente V10.

- Suporte para mover o Serviço de Sincronização de Armazenamento e/ou conta de armazenamento para um inquilino diferente do Azure Ative Directory (AAD)
    - O Azure File Sync suporta agora a deslocação do Serviço de Sincronização de Armazenamento e/ou conta de armazenamento para um grupo de recursos diferente, subscrição ou inquilino AD AZure.
    
- Melhorias de desempenho e fiabilidade diversas
    - A deteção de alterações na partilha de ficheiros Azure pode falhar se as regras de rede virtual (VNET) e firewall estiverem configuradas na conta de armazenamento.
    - Redução do consumo de memória associado à recuperação. 
    - Melhor desempenho ao utilizar o [cmdlet Invoke-AzStorageSyncChangeDetection.](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
    - Outras melhorias de fiabilidade diversas. 
    
### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implementar o Azure File Sync, deverá avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação Azure File Sync. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão OS não suportada. Para obter instruções de instalação e utilização, consulte a secção [ferramenta de avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planeamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para obter mais informações sobre como instalar e configurar o agente Azure File Sync com o Windows Server, consulte [o Planeamento de uma implementação de Azure File Sync](storage-sync-files-planning.md) e como implementar o [Azure File Sync](storage-sync-files-deployment-guide.md).

- A embalagem de instalação do agente deve ser instalada com permissões elevadas (administração).
- O agente não é suportado na opção de implementação do Nano Server.
- O agente é suportado apenas no Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um mínimo de memória de 2048.
- O serviço Storage Sync Agent (FileSyncSvc) não suporta pontos finais do servidor localizados num volume que tenha o diretório de informação de volume do sistema (SVI). Esta configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- O antivírus, a cópia de segurança e outras aplicações que acedam a ficheiros em camadas podem causar uma revogação indesejável, a menos que respeitem o atributo offline e ignorem a leitura do conteúdo desses ficheiros. Para obter mais informações, consulte [Troubleshoot Azure File Sync](storage-sync-files-troubleshoot.md).
- Os ecrãs de ficheiro Do Gestor de Recursos do Servidor de Ficheiros (FSRM) podem causar falhas de sincronização intermináveis quando os ficheiros estão bloqueados por causa do ecrã de ficheiros.
- Executar sysprep num servidor que tenha o agente Azure File Sync instalado não é suportado e pode levar a resultados inesperados. O agente Azure File Sync deve ser instalado depois de ter implantado a imagem do servidor e de completar a mini-configuração do Sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os itens seguintes não são sincronizados, mas o restante sistema continua a funcionar normalmente:
- Ficheiros com caracteres não suportados. Consulte [o guia de resolução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter a lista de caracteres não apoiados.
- Arquivos ou diretórios que terminam com um período.
- Caminhos com mais de 2048 carateres.
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
- O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.
- O Clustering de Ativação Pós-falha só é suportado com discos em cluster, mas não com Volumes Partilhados de Cluster (CSVs).
- Não é possível aninhar um ponto final do servidor. Pode coexistir no mesmo volume em paralelo com outro ponto final.
- Não guarde um ficheiro de paging de sos ou de aplicação dentro de um ponto final do servidor.
- O nome do servidor no portal não é atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efecção feitas na partilha de ficheiros Azure precisam primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de alterações é iniciado para um ponto final em nuvem uma vez a cada 24 horas. Para sincronizar imediatamente ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet Dedlet PowerShell da [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) powerShell pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Além disso, as alterações efetuadas a uma partilha de ficheiros Azure sobre o protocolo REST não atualizarão o último tempo modificado do SMB e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento pode ser transferido para um grupo de recursos diferente, subscrição ou inquilino AD AZure. Depois de o serviço de sincronização de armazenamento ou a conta de armazenamento ser movido, é necessário dar à aplicação Microsoft.StorageSync acesso à conta de armazenamento (ver [Ensure Azure File Sync tem acesso à conta de armazenamento).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Ao criar o ponto final da nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento devem estar no mesmo inquilino AD AZure. Uma vez criado o ponto final da nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento podem ser transferidos para diferentes inquilinos AD AZure.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os tempos de ficheiro. Isto irá garantir que os ficheiros mais antigos são tiered mais cedo do que os ficheiros recentemente acedidos.

## <a name="agent-version-9100"></a>Versão agente 9.1.0.0
As seguintes notas de lançamento são para a versão 9.1.0.0 do agente Azure File Sync lançado a 12 de dezembro de 2019. Estas notas juntam-se às notas de lançamento listadas para a versão 9.0.0.0.

Emissão corrigida nesta versão:  
- O Sync falha com um dos seguintes erros após a atualização para a versão 9.0 do agente Azure File Sync:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Versão agente 9.0.0.0
As seguintes notas de lançamento são para a versão 9.0.0.0 do agente Azure File Sync (lançado a 2 de dezembro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e problemas que são corrigidos

- Suporte de restauração de autosserviço
    - Os utilizadores podem agora restaurar os seus ficheiros utilizando a funcionalidade de versão anterior. Antes do lançamento do V9, a versão anterior não era suportada em volumes que tivessem o tiering de nuvem ativado. Esta função deve ser ativada para cada volume separadamente, no qual existe um ponto final com nivelamento de nuvem ativado. Para saber mais, ver  
[Autosserviço restaurado através de versões anteriores e VSS (Serviço de Cópia sombra de volume)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Suporte para tamanhos de partilha de ficheiros maiores 
    - O Azure File Sync suporta agora até 64TiB e 100 milhões de ficheiros num único espaço de nome sincronizado.  
 
- Suporte de desduplicação de dados no Servidor 2019 
    - A Deduplica de Dados é agora suportada com o tiering de nuvem ativado no Windows Server 2019. Para suportar a desduplicação de dados em volumes com tiering de nuvem, a atualização do Windows [KB4520062](https://support.microsoft.com/help/4520062) deve ser instalada. 
 
- Melhorado tamanho mínimo de ficheiro para um ficheiro para tier 
    - O tamanho mínimo do ficheiro para um ficheiro para o nível baseia-se agora no tamanho do cluster do sistema de ficheiros (o dobro do tamanho do cluster do sistema de ficheiros). Por exemplo, por padrão, o tamanho do cluster do sistema de ficheiros NTFS é 4KB, o tamanho mínimo de ficheiro resultante para um ficheiro para o nível é 8KB. 
 
- Cmdlet de teste de conectividade de rede 
    - Como parte da configuração do Azure File Sync, vários pontos finais de serviço devem ser contactados. Cada um tem o seu próprio nome DNS que precisa de ser acessível ao servidor. Estes URLs também são específicos da região para a qual um servidor está registado. Uma vez registado um servidor, o cmdlet de teste de conectividade (Utilitário de Registo de Energia e Servidor) pode ser utilizado para testar comunicações com todos os URLs específicos deste servidor. Este cmdlet pode ajudar a resolver problemas quando a comunicação incompleta impede o servidor de funcionar plenamente com o Azure File Sync e pode ser usado para afinar configurações de procuração e firewall.  
 
        Para executar o teste de conectividade da rede, executar os seguintes comandos PowerShell: 
 
        Módulo de Importação "C:\Programa Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Remova a melhoria do ponto final do servidor quando o tiering da nuvem estiver ativado 
    - Como antes, remover um ponto final do servidor não resulta na remoção de ficheiros na partilha de ficheiros Azure. No entanto, o comportamento para reparse pontos no servidor local mudou. Os pontos de reparse (ponteiros para ficheiros que não são locais no servidor) são agora eliminados ao remover um ponto final do servidor. Os ficheiros totalmente em cache permanecerão no servidor. Esta melhoria foi feita para evitar [ficheiros órfãs ao](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) remover um ponto final do servidor. Se o ponto final do servidor for recriado, os pontos de reparse para os ficheiros hierárquicos serão recriados no servidor.  
 
- Melhorias de desempenho e fiabilidade 
    - Falhas de recuperação reduzidas. O tamanho da recuperação é agora automaticamente ajustado com base na largura de banda da rede. 
    - Melhor desempenho do download ao adicionar um novo servidor a um grupo de sincronização. 
    - Ficheiros reduzidos que não sincronizam devido a conflitos de restrição. 
    - Os ficheiros não têm tier ou são inesperadamente recolhidos em certos cenários se o caminho do ponto final do servidor for um ponto de montagem de volume.
    
### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implementar o Azure File Sync, deverá avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação Azure File Sync. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão OS não suportada. Para obter instruções de instalação e utilização, consulte a secção [ferramenta de avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planeamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para obter mais informações sobre como instalar e configurar o agente Azure File Sync com o Windows Server, consulte [o Planeamento de uma implementação de Azure File Sync](storage-sync-files-planning.md) e como implementar o [Azure File Sync](storage-sync-files-deployment-guide.md).

- A embalagem de instalação do agente deve ser instalada com permissões elevadas (administração).
- O agente não é suportado na opção de implementação do Nano Server.
- O agente é suportado apenas no Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um mínimo de memória de 2048.
- O serviço Storage Sync Agent (FileSyncSvc) não suporta pontos finais do servidor localizados num volume que tenha o diretório de informação de volume do sistema (SVI). Esta configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- O antivírus, a cópia de segurança e outras aplicações que acedam a ficheiros em camadas podem causar uma revogação indesejável, a menos que respeitem o atributo offline e ignorem a leitura do conteúdo desses ficheiros. Para obter mais informações, consulte [Troubleshoot Azure File Sync](storage-sync-files-troubleshoot.md).
- Os ecrãs de ficheiro Do Gestor de Recursos do Servidor de Ficheiros (FSRM) podem causar falhas de sincronização intermináveis quando os ficheiros estão bloqueados por causa do ecrã de ficheiros.
- Executar sysprep num servidor que tenha o agente Azure File Sync instalado não é suportado e pode levar a resultados inesperados. O agente Azure File Sync deve ser instalado depois de ter implantado a imagem do servidor e de completar a mini-configuração do Sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os itens seguintes não são sincronizados, mas o restante sistema continua a funcionar normalmente:
- Ficheiros com caracteres não suportados. Consulte [o guia de resolução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter a lista de caracteres não apoiados.
- Arquivos ou diretórios que terminam com um período.
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
- Os ficheiros hierárquicos tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de eliminarem o ponto final do servidor. Para restaurar o acesso aos ficheiros, recrie o ponto final do servidor. Se passaram 30 dias desde que o ponto final do servidor foi eliminado ou se o ponto final da nuvem foi eliminado, os ficheiros hierárquicos que não foram recolhidos serão inutilizáveis. Para saber mais, ver [ficheiros Tiered não estão acessíveis no servidor depois de eliminar um ponto final do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.
- O Clustering de Ativação Pós-falha só é suportado com discos em cluster, mas não com Volumes Partilhados de Cluster (CSVs).
- Não é possível aninhar um ponto final do servidor. Pode coexistir no mesmo volume em paralelo com outro ponto final.
- Não guarde um ficheiro de paging de sos ou de aplicação dentro de um ponto final do servidor.
- O nome do servidor no portal não é atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efecção feitas na partilha de ficheiros Azure precisam primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de alterações é iniciado para um ponto final em nuvem uma vez a cada 24 horas. Para sincronizar imediatamente ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet Dedlet PowerShell da [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) powerShell pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Além disso, as alterações efetuadas a uma partilha de ficheiros Azure sobre o protocolo REST não atualizarão o último tempo modificado do SMB e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento pode ser transferido para um grupo de recursos diferente ou subscrição dentro do inquilino AD AZure existente. Se a conta de armazenamento for movida, tem de dar acesso ao Serviço de Sincronização de Ficheiros Híbridos à conta de armazenamento (ver [Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > A Azure File Sync não suporta mover a subscrição para um inquilino AD Azure diferente.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os tempos de ficheiro. Isto irá garantir que os ficheiros mais antigos são tiered mais cedo do que os ficheiros recentemente acedidos.
- Os ficheiros podem não ter nivelamento se o pagefile.sys estiver localizado num volume que tenha o nível de nuvem ativado. O pagefile.sys deve ser localizado num volume que tenha o nível de nuvem desativado.

## <a name="agent-version-8000"></a>Versão agente 8.0.0.0
As seguintes notas de lançamento são para a versão 8.0.0.0 do agente Azure File Sync (lançado a 8 de outubro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e problemas que são corrigidos

- Restaurar melhorias no desempenho
    - Tempos de recuperação mais rápidos para a recuperação feito através do Azure Backup. Os ficheiros restaurados sincronizar-se-ão com os servidores Azure File Sync muito mais rapidamente. 
- Experiência melhorada do portal de nivelamento de nuvens  
    - Se tiver ficheiros hierarquizados que não estejam a ser recolhidos, pode agora ver os erros de recuperação nas propriedades do ponto final do servidor. Além disso, a saúde do ponto final do servidor irá agora mostrar um erro e passos de atenuação se o controlador de filtro de nivelamento da nuvem não estiver carregado no servidor.
- Instalação de agente mais simples
    - O módulo Az\AzureRM PowerShell já não é necessário para registar o servidor tornando a instalação mais simples e rápida.
- Melhorias de desempenho e fiabilidade diversas

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implementar o Azure File Sync, deverá avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação Azure File Sync. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão OS não suportada. Para obter instruções de instalação e utilização, consulte a secção [ferramenta de avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planeamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para obter mais informações sobre como instalar e configurar o agente Azure File Sync com o Windows Server, consulte [o Planeamento de uma implementação de Azure File Sync](storage-sync-files-planning.md) e como implementar o [Azure File Sync](storage-sync-files-deployment-guide.md).

- A embalagem de instalação do agente deve ser instalada com permissões elevadas (administração).
- O agente não é suportado na opção de implementação do Nano Server.
- O agente é suportado apenas no Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um mínimo de memória de 2048.
- O serviço Storage Sync Agent (FileSyncSvc) não suporta pontos finais do servidor localizados num volume que tenha o diretório de informação de volume do sistema (SVI). Esta configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- O antivírus, a cópia de segurança e outras aplicações que acedam a ficheiros em camadas podem causar uma revogação indesejável, a menos que respeitem o atributo offline e ignorem a leitura do conteúdo desses ficheiros. Para obter mais informações, consulte [Troubleshoot Azure File Sync](storage-sync-files-troubleshoot.md).
- Os ecrãs de ficheiro Do Gestor de Recursos do Servidor de Ficheiros (FSRM) podem causar falhas de sincronização intermináveis quando os ficheiros estão bloqueados por causa do ecrã de ficheiros.
- Executar sysprep num servidor que tenha o agente Azure File Sync instalado não é suportado e pode levar a resultados inesperados. O agente Azure File Sync deve ser instalado depois de ter implantado a imagem do servidor e de completar a mini-configuração do Sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os itens seguintes não são sincronizados, mas o restante sistema continua a funcionar normalmente:
- Ficheiros com caracteres não suportados. Consulte [o guia de resolução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter a lista de caracteres não apoiados.
- Arquivos ou diretórios que terminam com um período.
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
- Os ficheiros hierárquicos tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de eliminarem o ponto final do servidor. Para restaurar o acesso aos ficheiros, recrie o ponto final do servidor. Se passaram 30 dias desde que o ponto final do servidor foi eliminado ou se o ponto final da nuvem foi eliminado, os ficheiros hierárquicos que não foram recolhidos serão inutilizáveis. Para saber mais, ver [ficheiros Tiered não estão acessíveis no servidor depois de eliminar um ponto final do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.
- O Clustering de Ativação Pós-falha só é suportado com discos em cluster, mas não com Volumes Partilhados de Cluster (CSVs).
- Não é possível aninhar um ponto final do servidor. Pode coexistir no mesmo volume em paralelo com outro ponto final.
- Não guarde um ficheiro de paging de sos ou de aplicação dentro de um ponto final do servidor.
- O nome do servidor no portal não é atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efecção feitas na partilha de ficheiros Azure precisam primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de alterações é iniciado para um ponto final em nuvem uma vez a cada 24 horas. Para sincronizar imediatamente ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet Dedlet PowerShell da [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) powerShell pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Além disso, as alterações efetuadas a uma partilha de ficheiros Azure sobre o protocolo REST não atualizarão o último tempo modificado do SMB e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento pode ser transferido para um grupo de recursos diferente ou subscrição dentro do inquilino AD AZure existente. Se a conta de armazenamento for movida, tem de dar acesso ao Serviço de Sincronização de Ficheiros Híbridos à conta de armazenamento (ver [Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > A Azure File Sync não suporta mover a subscrição para um inquilino AD Azure diferente.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os tempos de ficheiro. Isto irá garantir que os ficheiros mais antigos são tiered mais cedo do que os ficheiros recentemente acedidos.

## <a name="agent-version-7200"></a>Versão agente 7.2.0.0
As seguintes notas de lançamento são para a versão 7.2.0.0 do agente Azure File Sync lançado a 24 de julho de 2019. Estas notas juntam-se às notas de lançamento listadas para a versão 7.0.0.0.

Lista de questões corrigidas nesta versão:  
- O Agente de Sincronização de Armazenamento (FileSyncSvc) falha se a configuração de procuração for nula.
- O ponto final do servidor iniciará o BCDR (erro 0x80c80257 - ECS_E_BCDR_IN_PROGRESS) se vários pontos finais no servidor tiverem o mesmo nome.
- Melhorias na fiabilidade do nível da nuvem.

## <a name="agent-version-7100"></a>Versão agente 7.1.0.0
As seguintes notas de lançamento são para a versão 7.1.0.0 do agente Azure File Sync lançado a 12 de julho de 2019. Estas notas juntam-se às notas de lançamento listadas para a versão 7.0.0.0.

Lista de questões corrigidas nesta versão:  
- Aceder ou navegar na localização do ponto final do servidor sobre o SMB é lento no Windows Server 2012 R2. 
- Maior utilização do CPU após a instalação do agente Azure File Sync v6.
- Melhorias na telemetria de nivelamento de nuvem.
- Melhorias de fiabilidade diversas para o tiering e sincronização da nuvem.

## <a name="agent-version-7000"></a>Versão agente 7.0.0.0
As seguintes notas de lançamento são para a versão 7.0.0.0 do agente Azure File Sync (lançado a 19 de junho de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e problemas que são corrigidos

- Suporte para tamanhos de partilha de ficheiros maiores
    - Com a pré-visualização de maiores ações de ficheiros Azure, estamos a aumentar os nossos limites de suporte para sincronização de ficheiros também. Neste primeiro passo, o Azure File Sync suporta agora até 25 ficheiros TB e 50 milhões de ficheiros num único espaço de nome sincronizado. Para se candidatar à pré-visualização da grande partilha de ficheiros, preencha este formulário https://aka.ms/azurefilesatscalesurvey . 
- Suporte para firewall e definição de rede virtual em contas de armazenamento
    - O Azure File Sync suporta agora a definição de firewall e rede virtual nas contas de armazenamento. Para configurar a sua implementação para trabalhar com a firewall e a definição de rede virtual, consulte as definições de [firewall Configure e rede virtual](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- PowerShell cmdlet para sincronizar imediatamente ficheiros alterados na partilha de ficheiros Azure
    - Para sincronizar imediatamente ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet Dedlet PowerShell da Invoke-AzStorageSyncChangeDetection powerShell pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Este cmdlet destina-se a cenários em que algum tipo de processo automatizado está a fazer alterações na partilha de ficheiros Azure ou as alterações são feitas por um administrador (como mover ficheiros e diretórios para a partilha). Para alterações no utilizador final, a recomendação é instalar o agente Azure File Sync num IaaS VM e fazer com que os utilizadores finais acedam à partilha de ficheiros através do IaaS VM. Desta forma, todas as alterações irão rapidamente sincronizar com outros agentes sem a necessidade de utilizar o cmdlet Invoke-AzStorageSyncChangeDetection. Para saber mais, consulte a documentação [Invoke-AzStorageSyncChangeDetection.](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
- Experiência melhorada do portal se encontrar ficheiros que não estejam a sincronizar
    - Se tiver ficheiros que não estão a sincronizar, agora diferenciamos entre erros transitórios e persistentes no portal. Os erros transitórios geralmente resolvem-se sem a necessidade de ação de administração. Por exemplo, um ficheiro que está atualmente a ser utilizado não sincronizará até que a pega do ficheiro esteja fechada. Para erros persistentes, mostramos agora o número de ficheiros afetados por cada erro. A contagem de erros persistente também é apresentada nos ficheiros que não sincronizam a coluna de todos os pontos finais do servidor num grupo de sincronização.
- Restauro melhorado do nível de ficheiro Azure Backup
    - Os ficheiros individuais restaurados através do Azure Backup são agora detetados e sincronizados no ponto final do servidor mais rapidamente.
- Melhor nível de nuvem recorda fiabilidade do cmdlet 
    - O cmdlet Invoke-StorageSyncFileRecall permite agora que os clientes especifiquem por contagem de retícula de ficheiro e por atraso de retícula de ficheiro semelhante ao robocopia. Anteriormente, este cmdlet recordaria todos os ficheiros hierárquicos sob um determinado caminho por ordem aleatória. Com o novo parâmetro de encomenda, este cmdlet recordará primeiro os dados mais quentes e honrará a política de nivelamento em nuvem (pare de recordar se a política da data é cumprida ou se o espaço livre de volume é cumprido; o que acontecer primeiro).
- Suporte apenas para TLS 1.2 (TLS 1.0 e 1.1 está desativado)
    - O Azure File Sync agora suporta a utilização do TLS 1.2 apenas em servidores que tenham TLS 1.0 e 1.1 desativado. Antes desta melhoria, o registo do servidor falharia se o TLS 1.0 e o 1.1 fossem desativado no servidor.
- Melhorias de desempenho e fiabilidade diversas para sincronização e nivelamento em nuvem
    - Existem várias melhorias de fiabilidade e desempenho nesta versão. Alguns deles são direcionados para tornar o nível de nuvem mais eficiente e o Azure File Sync como um todo funciona melhor nessas situações quando você tem um calendário de estrangulamento de largura de banda definido.

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implementar o Azure File Sync, deverá avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação Azure File Sync. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão OS não suportada. Para obter instruções de instalação e utilização, consulte a secção [ferramenta de avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planeamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para obter mais informações sobre como instalar e configurar o agente Azure File Sync com o Windows Server, consulte [o Planeamento de uma implementação de Azure File Sync](storage-sync-files-planning.md) e como implementar o [Azure File Sync](storage-sync-files-deployment-guide.md).

- A embalagem de instalação do agente deve ser instalada com permissões elevadas (administração).
- O agente não é suportado na opção de implementação do Nano Server.
- O agente é suportado apenas no Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um mínimo de memória de 2048.
- O serviço Storage Sync Agent (FileSyncSvc) não suporta pontos finais do servidor localizados num volume que tenha o diretório de informação de volume do sistema (SVI). Esta configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- O antivírus, a cópia de segurança e outras aplicações que acedam a ficheiros em camadas podem causar uma revogação indesejável, a menos que respeitem o atributo offline e ignorem a leitura do conteúdo desses ficheiros. Para obter mais informações, consulte [Troubleshoot Azure File Sync](storage-sync-files-troubleshoot.md).
- Os ecrãs de ficheiro Do Gestor de Recursos do Servidor de Ficheiros (FSRM) podem causar falhas de sincronização intermináveis quando os ficheiros estão bloqueados por causa do ecrã de ficheiros.
- Executar sysprep num servidor que tenha o agente Azure File Sync instalado não é suportado e pode levar a resultados inesperados. O agente Azure File Sync deve ser instalado depois de ter implantado a imagem do servidor e de completar a mini-configuração do Sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os itens seguintes não são sincronizados, mas o restante sistema continua a funcionar normalmente:
- Ficheiros com caracteres não suportados. Consulte [o guia de resolução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter a lista de caracteres não apoiados.
- Arquivos ou diretórios que terminam com um período.
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
- Os ficheiros hierárquicos tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de eliminarem o ponto final do servidor. Para restaurar o acesso aos ficheiros, recrie o ponto final do servidor. Se passaram 30 dias desde que o ponto final do servidor foi eliminado ou se o ponto final da nuvem foi eliminado, os ficheiros hierárquicos que não foram recolhidos serão inutilizáveis.
- O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.
- O Clustering de Ativação Pós-falha só é suportado com discos em cluster, mas não com Volumes Partilhados de Cluster (CSVs).
- Não é possível aninhar um ponto final do servidor. Pode coexistir no mesmo volume em paralelo com outro ponto final.
- Não guarde um ficheiro de paging de sos ou de aplicação dentro de um ponto final do servidor.
- O nome do servidor no portal não é atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efecção feitas na partilha de ficheiros Azure precisam primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de alterações é iniciado para um ponto final em nuvem uma vez a cada 24 horas. Além disso, as alterações efetuadas a uma partilha de ficheiros Azure sobre o protocolo REST não atualizarão o último tempo modificado do SMB e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento pode ser transferido para um grupo de recursos diferente ou subscrição dentro do inquilino AD AZure existente. Se a conta de armazenamento for movida, tem de dar acesso ao Serviço de Sincronização de Ficheiros Híbridos à conta de armazenamento (ver [Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > A Azure File Sync não suporta mover a subscrição para um inquilino AD Azure diferente.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os tempos de ficheiro. Isto irá garantir que os ficheiros mais antigos são tiered mais cedo do que os ficheiros recentemente acedidos.

## <a name="agent-version-6300"></a>Versão agente 6.3.0.0
As seguintes notas de lançamento são para a versão 6.3.0.0 do agente Azure File Sync lançado a 27 de junho de 2019. Estas notas juntam-se às notas de lançamento listadas para a versão 6.0.0.0.

Lista de questões corrigidas nesta versão:  
- Aceder ou navegar na localização do ponto final do servidor sobre o SMB é lento no Windows Server 2012 R2 
- Maior utilização do CPU após a instalação do agente Azure File Sync v6
- Melhorias da telemetria de nivelamento em nuvem

## <a name="agent-version-6200"></a>Versão agente 6.2.0.0
As seguintes notas de lançamento são para a versão 6.2.0.0 do agente Azure File Sync lançado a 13 de junho de 2019. Estas notas juntam-se às notas de lançamento listadas para a versão 6.0.0.0.

Lista de questões corrigidas nesta versão:  
- Depois de criar um ponto final do servidor, a utilização do CPU elevado pode ocorrer quando a chamada de fundo está a descarregar ficheiros para o servidor
- As operações de sincronização e de nivelamento em nuvem podem falhar com ECS_E_SERVER_CREDENTIAL_NEEDED de erro devido à expiração do token
- Relembrou um ficheiro pode falhar se o URL para descarregar o ficheiro contiver caracteres reservados 

## <a name="agent-version-6100"></a>Versão agente 6.1.0.0
As seguintes notas de lançamento são para a versão 6.1.0.0 do agente Azure File Sync lançado a 6 de maio de 2019. Estas notas juntam-se às notas de lançamento listadas para a versão 6.0.0.0.

Lista de questões corrigidas nesta versão:  
- O Windows Admin Center não consegue exibir a versão do agente e a configuração do ponto final do servidor nos servidores que têm a versão 6.0 do agente Azure File Sync.

## <a name="agent-version-6000"></a>Versão agente 6.0.0.0
As seguintes notas de lançamento são para a versão 6.0.0.0 do agente Azure File Sync (lançado a 22 de abril de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e problemas que são corrigidos

- Suporte de atualização automática do agente
  - Ouvimos o seu feedback e adicionámos uma funcionalidade de atualização automática ao agente do servidor Azure File Sync. Para obter mais informações, consulte [a política de atualização do agente Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Suporte para acLs de partilha de ficheiros Azure
  - O Azure File Sync sempre apoiou a sincronização de ACLs entre pontos finais do servidor, mas os ACLs não foram sincronizados com o ponto final da cloud (partilha de ficheiros Azure). Esta versão adiciona suporte para sincronizar ACLs entre servidores e pontos finais de nuvem.
- Upload paralelo e descarregar sessões de sincronização para um ponto final do servidor 
  - Os pontos finais do servidor suportam agora o upload e o descarregamento de ficheiros ao mesmo tempo. Não há mais espera que um download seja concluído para que os ficheiros possam ser enviados para a partilha de ficheiros Azure. 
- Novos cmdlets cloud tiering para obter o estado de volume e tiering
  - Dois novos cmdlets PowerShell locais do servidor podem agora ser usados para obter informações de tiering de nuvem e recolha de ficheiros. Disponibilizam informações de registo de dois canais de eventos no servidor:
    - O Get-StorageSyncFileTieringResult irá listar todos os ficheiros e os seus caminhos que não tenham sido classificados e informará sobre o motivo.
    - Get-StorageSyncFileRecallResult reporta todos os eventos de recolha de ficheiros. Ele lista todos os ficheiros recordados e o seu caminho, bem como o sucesso ou erro para essa recordação.
  - Por predefinição, ambos os canais de eventos podem armazenar até 1 MB cada – pode aumentar a quantidade de ficheiros relatados aumentando o tamanho do canal de eventos.
- Suporte para o modo FIPS
  - O Azure File Sync suporta agora o modo FIPS em servidores que tenham o agente Azure File Sync instalado.
    - Antes de ativar o modo FIPS no seu servidor, instale o agente Azure File Sync e [o módulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) no seu servidor. Se o FIPS já estiver ativado no servidor, [descarregue manualmente](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) o [módulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) para o seu servidor.
- Melhorias de fiabilidade diversas para o tiering e sincronização da nuvem

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implementar o Azure File Sync, deverá avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação Azure File Sync. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão OS não suportada. Para obter instruções de instalação e utilização, consulte a secção [ferramenta de avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planeamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para obter mais informações sobre como instalar e configurar o agente Azure File Sync com o Windows Server, consulte [o Planeamento de uma implementação de Azure File Sync](storage-sync-files-planning.md) e como implementar o [Azure File Sync](storage-sync-files-deployment-guide.md).

- A embalagem de instalação do agente deve ser instalada com permissões elevadas (administração).
- O agente não é suportado na opção de implementação do Nano Server.
- O agente é suportado apenas no Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um mínimo de memória de 2048.
- O serviço Storage Sync Agent (FileSyncSvc) não suporta pontos finais do servidor localizados num volume que tenha o diretório de informação de volume do sistema (SVI). Esta configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- O antivírus, a cópia de segurança e outras aplicações que acedam a ficheiros em camadas podem causar uma revogação indesejável, a menos que respeitem o atributo offline e ignorem a leitura do conteúdo desses ficheiros. Para obter mais informações, consulte [Troubleshoot Azure File Sync](storage-sync-files-troubleshoot.md).
- Os ecrãs de ficheiro Do Gestor de Recursos do Servidor de Ficheiros (FSRM) podem causar falhas de sincronização intermináveis quando os ficheiros estão bloqueados por causa do ecrã de ficheiros.
- Executar sysprep num servidor que tenha o agente Azure File Sync instalado não é suportado e pode levar a resultados inesperados. O agente Azure File Sync deve ser instalado depois de ter implantado a imagem do servidor e de completar a mini-configuração do Sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os itens seguintes não são sincronizados, mas o restante sistema continua a funcionar normalmente:
- Ficheiros com caracteres não suportados. Consulte [o guia de resolução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter a lista de caracteres não apoiados.
- Arquivos ou diretórios que terminam com um período.
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
- Os ficheiros hierárquicos tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de eliminarem o ponto final do servidor. Para restaurar o acesso aos ficheiros, recrie o ponto final do servidor. Se passaram 30 dias desde que o ponto final do servidor foi eliminado ou se o ponto final da nuvem foi eliminado, os ficheiros hierárquicos que não foram recolhidos serão inutilizáveis.
- O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.
- O Clustering de Ativação Pós-falha só é suportado com discos em cluster, mas não com Volumes Partilhados de Cluster (CSVs).
- Não é possível aninhar um ponto final do servidor. Pode coexistir no mesmo volume em paralelo com outro ponto final.
- Não guarde um ficheiro de paging de sos ou de aplicação dentro de um ponto final do servidor.
- O nome do servidor no portal não é atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efecção feitas na partilha de ficheiros Azure precisam primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de alterações é iniciado para um ponto final em nuvem uma vez a cada 24 horas. Além disso, as alterações efetuadas a uma partilha de ficheiros Azure sobre o protocolo REST não atualizarão o último tempo modificado do SMB e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento pode ser transferido para um grupo de recursos diferente ou subscrição dentro do inquilino AD AZure existente. Se a conta de armazenamento for movida, tem de dar acesso ao Serviço de Sincronização de Ficheiros Híbridos à conta de armazenamento (ver [Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > A Azure File Sync não suporta mover a subscrição para um inquilino AD Azure diferente.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os tempos de ficheiro. Isto irá garantir que os ficheiros mais antigos são tiered mais cedo do que os ficheiros recentemente acedidos.
- Ao visualizar as propriedades do ficheiro a partir de um cliente SMB, o atributo offline pode parecer estar definido incorretamente devido à colocação em cache dos metadados de ficheiros por parte do SMB.

## <a name="agent-version-5200"></a>Versão agente 5.2.0.0
As seguintes notas de lançamento são para a versão 5.2.0.0 do agente Azure File Sync lançado a 4 de abril de 2019. Estas notas juntam-se às notas de lançamento listadas para a versão 5.0.2.0.

Lista de questões corrigidas nesta versão:  
- Melhorias de fiabilidade para transferência de dados offline e funcionalidades de retoma de transferência de dados
- Melhorias da telemetria sincronizada

## <a name="agent-version-5100"></a>Versão agente 5.1.0.0
As seguintes notas de lançamento são para a versão 5.1.0.0 do agente Azure File Sync lançado a 7 de março de 2019. Estas notas juntam-se às notas de lançamento listadas para a versão 5.0.2.0.

Lista de questões corrigidas nesta versão:  
- Os ficheiros podem não sincronizar com o erro 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) se a enumeração de alteração estiver a falhar no servidor
- Se uma sessão de sincronização ou ficheiro receber um erro 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), a sincronização voltará a tentar a operação
- Os ficheiros podem não sincronizar com o erro 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Pode ocorrer uma utilização de alta memória ao recordar ficheiros
- Melhorias da telemetria de nivelamento em nuvem 

## <a name="agent-version-5020"></a>Versão agente 5.0.2.0
As seguintes notas de lançamento são para a versão 5.0.2.0 do agente Azure File Sync (lançado a 12 de fevereiro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e problemas que são corrigidos

- Apoio à nuvem do Governo de Azure
  - Adicionámos apoio de pré-visualização à nuvem do Governo Azure. Isto requer uma subscrição de lista branca e um download de agente especial da Microsoft. Para ter acesso à pré-visualização, por favor envie-nos um e-mail diretamente para [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com) .
- Suporte à Desduplicação de Dados
    - A Deduplica de Dados é agora totalmente suportada com o tiering da nuvem ativado no Windows Server 2016 e Windows Server 2019. Permitir a desduplicação num volume com nivelamento de nuvem ativado permite cache de mais ficheiros no local sem armazenar mais armazenamento.
- Suporte para transferência de dados offline (por exemplo, via Caixa de Dados)
    - Migrar facilmente grandes quantidades de dados para o Azure File Sync através de qualquer meio que escolha. Pode escolher Azure Data Box, AzCopy e até mesmo serviços de migração de terceiros. Não é necessário utilizar quantidades massivas de largura de banda para obter os seus dados no Azure, no caso da Data Box – basta enviá-lo para lá! Para saber mais, consulte [os Docs de Transferência de Dados Offline](https://aka.ms/AFS/OfflineDataTransfer).
- Melhor desempenho da sincronização
    - Os clientes com vários pontos finais do servidor no mesmo volume podem ter experimentado um desempenho de sincronização lenta antes desta versão. O Azure File Sync cria uma imagem temporária do VSS uma vez por dia no servidor para sincronizar ficheiros que têm pegas abertas. O Sync suporta agora vários pontos finais do servidor sincronizando um volume quando uma sessão de sincronização VSS está ativa. Não há mais espera que uma sessão de sincronização VSS seja concluída para que a sincronização possa ser retomada em outros pontos finais do servidor no volume.
- Melhor monitorização no portal
    - Os gráficos foram adicionados no portal do Serviço de Sincronização de Armazenamento para visualizar:
        - Número de ficheiros sincronizados
        - Dimensão dos dados transferidos
        - Número de ficheiros que não sincronizam
        - Tamanho dos dados recordados
        - Estado de conectividade do servidor
    - Para saber mais, consulte [o Monitor Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Melhoria da escalabilidade e fiabilidade
    - O número máximo de objetos do sistema de ficheiros (diretórios e ficheiros) num diretório aumentou para 1.000.000. O limite anterior era de 200.000.
    - O Sync tentará retomar a transferência de dados em vez de retransmissão quando uma transferência é interrompida para ficheiros grandes 

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implementar o Azure File Sync, deverá avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação Azure File Sync. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão OS não suportada. Para obter instruções de instalação e utilização, consulte a secção [ferramenta de avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planeamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para obter mais informações sobre como instalar e configurar o agente Azure File Sync com o Windows Server, consulte [o Planeamento de uma implementação de Azure File Sync](storage-sync-files-planning.md) e como implementar o [Azure File Sync](storage-sync-files-deployment-guide.md).

- A embalagem de instalação do agente deve ser instalada com permissões elevadas (administração).
- O agente não é suportado nas opções de implementação do Windows Server Core ou Nano Server.
- O agente é suportado apenas no Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um mínimo de memória de 2048.
- O serviço Storage Sync Agent (FileSyncSvc) não suporta pontos finais do servidor localizados num volume que tenha o diretório de informação de volume do sistema (SVI). Esta configuração levará a resultados inesperados.
- O modo FIPS não é suportado e deve ser desativado. 

### <a name="interoperability"></a>Interoperabilidade
- O antivírus, a cópia de segurança e outras aplicações que acedam a ficheiros em camadas podem causar uma revogação indesejável, a menos que respeitem o atributo offline e ignorem a leitura do conteúdo desses ficheiros. Para obter mais informações, consulte [Troubleshoot Azure File Sync](storage-sync-files-troubleshoot.md).
- Os ecrãs de ficheiro Do Gestor de Recursos do Servidor de Ficheiros (FSRM) podem causar falhas de sincronização intermináveis quando os ficheiros estão bloqueados por causa do ecrã de ficheiros.
- Executar sysprep num servidor que tenha o agente Azure File Sync instalado não é suportado e pode levar a resultados inesperados. O agente Azure File Sync deve ser instalado depois de ter implantado a imagem do servidor e de completar a mini-configuração do Sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os itens seguintes não são sincronizados, mas o restante sistema continua a funcionar normalmente:
- Ficheiros com caracteres não suportados. Consulte [o guia de resolução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter a lista de caracteres não apoiados.
- Arquivos ou diretórios que terminam com um período.
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
- Os ficheiros hierárquicos tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de eliminarem o ponto final do servidor. Para restaurar o acesso aos ficheiros, recrie o ponto final do servidor. Se passaram 30 dias desde que o ponto final do servidor foi eliminado ou se o ponto final da nuvem foi eliminado, os ficheiros hierárquicos que não foram recolhidos serão inutilizáveis.
- O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.
- O Clustering de Ativação Pós-falha só é suportado com discos em cluster, mas não com Volumes Partilhados de Cluster (CSVs).
- Não é possível aninhar um ponto final do servidor. Pode coexistir no mesmo volume em paralelo com outro ponto final.
- Não guarde um ficheiro de paging de sos ou de aplicação dentro de um ponto final do servidor.
- O nome do servidor no portal não é atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efecção feitas na partilha de ficheiros Azure precisam primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de alterações é iniciado para um ponto final em nuvem uma vez a cada 24 horas. Além disso, as alterações efetuadas a uma partilha de ficheiros Azure sobre o protocolo REST não atualizarão o último tempo modificado do SMB e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento pode ser transferido para um grupo de recursos diferente ou subscrição dentro do inquilino AD AZure existente. Se a conta de armazenamento for movida, tem de dar acesso ao Serviço de Sincronização de Ficheiros Híbridos à conta de armazenamento (ver [Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > A Azure File Sync não suporta mover a subscrição para um inquilino AD Azure diferente.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os tempos de ficheiro. Isto irá garantir que os ficheiros mais antigos são tiered mais cedo do que os ficheiros recentemente acedidos.
- Ao visualizar as propriedades do ficheiro a partir de um cliente SMB, o atributo offline pode parecer estar definido incorretamente devido à colocação em cache dos metadados de ficheiros por parte do SMB.
