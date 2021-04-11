---
title: Notas de lançamento para o agente Azure File Sync | Microsoft Docs
description: Leia as notas de lançamento do agente Azure File Sync, que permite centralizar as ações de ficheiros da sua organização em Ficheiros Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/26/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 0e1cfe0ae53d1e1b35c5ec29d6c11b0891137e6d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074408"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notas de versão do agente do Azure File Sync
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. As suas instalações do Windows Server são transformadas numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS). Pode ter o número de caches que precisar em todo o mundo.

Este artigo disponibiliza as notas de versão das versões suportadas do agente do Azure File Sync.

## <a name="supported-versions"></a>Versões suportadas
As seguintes versões do agente Azure File Sync são suportadas:

| Marco | Número de versão do agente | Data da versão | Estado |
|----|----------------------|--------------|------------------|
| Lançamento V12 - [KB4568585](https://support.microsoft.com/topic/b9605f04-b4af-4ad8-86b0-2c490c535cfd)| 12.0.0.0 | 26 de março de 2021 | Suportado - Voo |
| V11.2 Lançamento - [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 de fevereiro de 2021 | Suportado |
| V11.1 Lançamento - [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 de novembro de 2020 | Suportado |
| V10.1 Lançamento - [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5 de junho de 2020 | Suportado - Versão do agente expirará a 7 de junho de 2021 |
| Rollup de atualização de maio de 2020 - [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 de maio de 2020 | Suportado - Versão do agente expirará a 7 de junho de 2021 |
| V10 Release - [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9 de abril de 2020 | Suportado - Versão do agente expirará a 7 de junho de 2021 |

## <a name="unsupported-versions"></a>Versões não suportadas
As seguintes versões do agente Azure File Sync expiraram e já não estão suportadas:

| Marco | Número de versão do agente | Data da versão | Estado |
|----|----------------------|--------------|------------------|
| Lançamento V9 | 9.0.0.0 - 9.1.0.0 | N/D | Não Suportado - Versão do agente expirou em 16 de fevereiro de 2021 |
| Lançamento V8 | 8.0.0.0 | N/D | Não Suportado - Versão do agente expirou em 12 de janeiro de 2021 |
| Lançamento V7 | 7.0.0.0 - 7.2.0.0 | N/D | Não Suportado - Versões de agente expiradas a 1 de setembro de 2020 |
| Lançamento V6 | 6.0.0.0 - 6.3.0.0 | N/D | Não Suportado - Versões de agente expiradas em 21 de abril de 2020 |
| Lançamento V5 | 5.0.2.0 - 5.2.0.0 | N/D | Não Suportado - Versões de agente expiradas em 18 de março de 2020 |
| Lançamento V4 | 4.0.1.0 - 4.3.0.0 | N/D | Não Suportado - Versões de agente expiradas em 6 de novembro de 2019 |
| Lançamento V3 | 3.1.0.0 - 3.4.0.0 | N/D | Não Suportado - Versões de agente expiradas em 19 de agosto de 2019 |
| Agentes pré-GA | 1.1.0.0 - 3.0.13.0 | N/D | Não Suportado - Versões de agente expiradas a 1 de outubro de 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente do Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-12000"></a>Versão agente 12.0.0.0
As seguintes notas de lançamento são para a versão 12.0.0.0 do agente Azure File Sync (lançado a 26 de março de 2021).

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e problemas que são corrigidos
- Nova experiência do portal para configurar a política de acesso à rede e ligações privadas de ponto final
    - Pode agora utilizar o portal para desativar o acesso ao ponto final público do Serviço de Sincronização de Armazenamento e para aprovar, rejeitar e remover ligações privadas de ponto final. Para configurar a política de acesso à rede e as ligações de ponto final privado, abra o portal de Serviço de Sincronização de Armazenamento, aceda à secção Definições e clique em Rede.
 
- Suporte de nivelamento para tamanhos de cluster de volume superiores a 64KiB
    - Cloud Tiering agora suporta tamanhos de cluster de volume até 2MiB no Servidor 2019. Para saber mais, veja [qual é o tamanho mínimo do ficheiro para um ficheiro a nível?](https://docs.microsoft.com/azure/storage/files/storage-sync-choose-cloud-tiering-policies#minimum-file-size-for-a-file-to-tier)
 
- Medir largura de banda e latência para o serviço Escíneitado de Arquivo Azure
    - O Test-StorageSyncNetworkConnectivity cmdlet pode agora ser usado para medir a latência e largura de banda na conta de serviço e armazenamento de ArquivoS Azure. A latência para o serviço Azure File Sync e a conta de armazenamento são medidas por padrão ao executar o cmdlet.  O upload e o download da largura de banda para a conta de armazenamento são medidos quando se utiliza o parâmetro "MeasureBandwidth".
 
        Por exemplo, para medir a largura de banda e a latência na conta de serviço e armazenamento Azure File Sync, executar os seguintes comandos PowerShell:
 
        ```powershell
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
        Test-StorageSyncNetworkConnectivity -MeasureBandwidth 
        ``` 
 
- Mensagens de erro melhoradas no portal quando a criação do ponto final do servidor falha
    - Ouvimos o seu feedback e melhorámos as mensagens de erro e a orientação quando a criação do ponto final do servidor falha.
 
- Melhorias de desempenho e fiabilidade diversas
    - Melhor desempenho de deteção de alterações para detetar ficheiros que mudaram na partilha de ficheiros Azure.
    - Melhorias de desempenho para sessões de sincronização de reconciliação. 
    - Sync melhorias para reduzir ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED e ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED erros.
    - Corrigiu um bug que pode fazer com que os ficheiros não se estudam no Servidor 2019 se a Desduplicação de Dados estiver ativada no volume.
    - Corrigiu um bug que pode fazer com que o AFSDiag não comprima ficheiros se um ficheiro for maior do que o 2GiB.

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implementar o Azure File Sync, deverá avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação Azure File Sync. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão OS não suportada. Para obter instruções de instalação e utilização, consulte a secção [ferramenta de avaliação](./storage-sync-files-planning.md#evaluation-cmdlet) no guia de planeamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para obter mais informações sobre como instalar e configurar o agente Azure File Sync com o Windows Server, consulte [o Planeamento de uma implementação de Azure File Sync](storage-sync-files-planning.md) e como implementar o [Azure File Sync](storage-sync-files-deployment-guide.md).

- É necessário reiniciar para servidores que tenham uma instalação existente do agente Azure File Sync.
- A embalagem de instalação do agente deve ser instalada com permissões elevadas (administração).
- O agente não é suportado na opção de implementação do Nano Server.
- O agente é suportado apenas no Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um mínimo de memória de 2048. Consulte [os recursos recomendados](./storage-sync-files-planning.md#recommended-system-resources) do sistema para obter mais informações.
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
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efecção feitas na partilha de ficheiros Azure precisam primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de alterações é iniciado para um ponto final em nuvem uma vez a cada 24 horas. Para sincronizar imediatamente ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet Dedlet PowerShell da [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) powerShell pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Além disso, as alterações efetuadas a uma partilha de ficheiros Azure sobre o protocolo REST não atualizarão o último tempo modificado do SMB e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento pode ser transferido para um grupo de recursos diferente, subscrição ou inquilino AD AZure. Depois de o serviço de sincronização de armazenamento ou a conta de armazenamento ser movido, é necessário dar à aplicação Microsoft.StorageSync acesso à conta de armazenamento (ver [Ensure Azure File Sync tem acesso à conta de armazenamento).](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > Ao criar o ponto final da nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento devem estar no mesmo inquilino AD AZure. Uma vez criado o ponto final da nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento podem ser transferidos para diferentes inquilinos AD AZure.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os tempos de ficheiro. Isto irá garantir que os ficheiros mais antigos são tiered mais cedo do que os ficheiros recentemente acedidos.
    > [!Warning]  
    > O interruptor Robocopy /B não é suportado com Azure File Sync. Utilizando o interruptor Robocopy /B com um ponto de terminação do servidor Azure File Sync, uma vez que a fonte pode levar à corrupção de ficheiros.

## <a name="agent-version-11200"></a>Versão agente 11.2.0.0
As seguintes notas de lançamento são para a versão 11.2.0.0 do agente Azure File Sync lançado a 2 de fevereiro de 2021. Estas notas juntam-se às notas de lançamento listadas para a versão 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e problemas que são corrigidos 
- Se uma sessão de sincronização for cancelada devido a um elevado número de erros por item, a sincronização pode passar por uma reconciliação quando uma nova sessão começar se o serviço Azure File Sync determinar que é necessária uma sessão de sincronização personalizada para corrigir os erros por item.
- Registar um servidor utilizando o Register-AzStorageSyncServer cmdlet pode falhar com o erro "Exceção Não Manipulado".
- Novo cmdlet PowerShell (Add-StorageSyncAllowedServerEndpointPath) para configurar os caminhos de pontos finais do servidor permitidos num servidor. Este cmdlet é útil para cenários em que a implementação do Azure File Sync é gerida por um Fornecedor de Solução Cloud (CSP) ou Fornecedor de Serviços e o cliente quer configurar caminhos de pontos finais permitidos num servidor. Ao criar um ponto final do servidor, se o caminho especificado não estiver na lista de admissões, a criação do ponto final do servidor falhará. Nota: esta é uma funcionalidade opcional e todos os caminhos suportados são permitidos por padrão ao criar um ponto final do servidor.  

    
    - Para adicionar um caminho de ponto final do servidor que é permitido, executar os seguintes comandos PowerShell no servidor:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Para obter a lista de caminhos suportados, executar o seguinte comando PowerShell:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Para remover um caminho, executar o seguinte comando PowerShell:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>Versão agente 11.1.0.0
As seguintes notas de lançamento são para a versão 11.1.0.0 do agente Azure File Sync (lançado a 4 de novembro de 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e problemas que são corrigidos
- Novos modos de tiering em nuvem para controlar o download inicial e a recolha proativa
    - Modo de descarregamento inicial: pode agora escolher como pretende que os seus ficheiros sejam inicialmente descarregados no seu novo ponto final do servidor. Pretende que todos os seus ficheiros seja transferido ou o maior número possível de ficheiros descarregados no seu servidor através da última hora modificada? Podes fazer isso! Não pode usar o nível das nuvens? Pode agora optar por evitar ficheiros hierárquicos no seu sistema. Para saber mais, consulte Criar uma secção [de ponto final](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) do servidor na documentação 'Implementar Azure File Sync'.
    - Modo de recolha proactivo: sempre que um ficheiro é criado ou modificado, pode reencamê-lo proactivamente para servidores que especifique dentro do mesmo grupo de sincronização. Isto torna o ficheiro facilmente disponível para consumo em cada servidor especificado. Tem equipas em todo o mundo a trabalhar nos mesmos dados? Habilitar a recolha proativa para que quando a equipa chegar na manhã seguinte, todos os ficheiros atualizados por uma equipa num fuso horário diferente sejam descarregados e prontos para ir! Para saber mais, consulte [Proactivamente recordar novos ficheiros e alterar ficheiros a partir de uma secção de partilha de ficheiros Azure](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) na documentação Deploy Azure File Sync.

- Excluir aplicações do rastreio do último tempo de acesso em nuvem Pode agora excluir aplicações do último rastreio do tempo de acesso. Quando uma aplicação acede a um ficheiro, a última hora de acesso ao ficheiro é atualizada na base de dados de tiering de nuvem. As aplicações que digitalizam o sistema de ficheiros como antivírus fazem com que todos os ficheiros tenham a mesma última hora de acesso que impacta quando os ficheiros são nivelados.

    Para excluir as aplicações do rastreio do tempo de acesso do último, adicione o nome do processo à definição de registo HeatTrackingProcessNameExclusionList que está localizada sob HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

    Exemplo: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

    > [!Note]  
    > Os processos de Deduplicação de Dados e Gestor de Recursos do Servidor de Ficheiros (FSRM) são excluídos por padrão (codificado em duração) e a lista de exclusão do processo é atualizada a cada 5 minutos.

- Melhorias de desempenho e fiabilidade diversas
    - Melhor desempenho de deteção de alterações para detetar ficheiros que mudaram na partilha de ficheiros Azure.
    - Melhor desempenho do upload de sincronização.
    - O upload inicial é agora realizado a partir de um snapshot VSS que reduz erros por item e falhas de sessão de sincronização.
    - Sync melhorias de fiabilidade para certos padrões de E/S.
    - Corrigiu um erro para evitar que a base de dados de sincronização recuasse no tempo em clusters de falha quando ocorre uma falha.
    - Melhor desempenho de recordação ao aceder a um ficheiro hierárquico.

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implementar o Azure File Sync, deverá avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação Azure File Sync. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão OS não suportada. Para obter instruções de instalação e utilização, consulte a secção [ferramenta de avaliação](./storage-sync-files-planning.md#evaluation-cmdlet) no guia de planeamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para obter mais informações sobre como instalar e configurar o agente Azure File Sync com o Windows Server, consulte [o Planeamento de uma implementação de Azure File Sync](storage-sync-files-planning.md) e como implementar o [Azure File Sync](storage-sync-files-deployment-guide.md).

- A embalagem de instalação do agente deve ser instalada com permissões elevadas (administração).
- O agente não é suportado na opção de implementação do Nano Server.
- O agente é suportado apenas no Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um mínimo de memória de 2048. Consulte [os recursos recomendados](./storage-sync-files-planning.md#recommended-system-resources) do sistema para obter mais informações.
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
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efecção feitas na partilha de ficheiros Azure precisam primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de alterações é iniciado para um ponto final em nuvem uma vez a cada 24 horas. Para sincronizar imediatamente ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet Dedlet PowerShell da [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) powerShell pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Além disso, as alterações efetuadas a uma partilha de ficheiros Azure sobre o protocolo REST não atualizarão o último tempo modificado do SMB e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento pode ser transferido para um grupo de recursos diferente, subscrição ou inquilino AD AZure. Depois de o serviço de sincronização de armazenamento ou a conta de armazenamento ser movido, é necessário dar à aplicação Microsoft.StorageSync acesso à conta de armazenamento (ver [Ensure Azure File Sync tem acesso à conta de armazenamento).](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > Ao criar o ponto final da nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento devem estar no mesmo inquilino AD AZure. Uma vez criado o ponto final da nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento podem ser transferidos para diferentes inquilinos AD AZure.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os tempos de ficheiro. Isto irá garantir que os ficheiros mais antigos são tiered mais cedo do que os ficheiros recentemente acedidos.
    > [!Warning]  
    > O interruptor Robocopy /B não é suportado com Azure File Sync. Utilizando o interruptor Robocopy /B com um ponto de terminação do servidor Azure File Sync, uma vez que a fonte pode levar à corrupção de ficheiros.

## <a name="agent-version-10100"></a>Versão agente 10.1.0.0
As seguintes notas de lançamento são para a versão 10.1.0.0 do agente Azure File Sync lançado a 5 de junho de 2020. Estas notas juntam-se às notas de lançamento listadas para as versões 10.0.0.0 e 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e problemas que são corrigidos

- Suporte de ponto final privado Azure
    - O tráfego de sincronização para o Serviço de Sincronização de Armazenamento pode agora ser enviado para um ponto final privado. Isto permite fazer um túnel sobre uma ligação ExpressRoute ou VPN. Para saber mais, consulte [pontos finais da rede De Sincronização de Ficheiros Azure.](./storage-sync-files-networking-endpoints.md)
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

- Suporte para mover o Serviço de Sincronização de Armazenamento e/ou conta de armazenamento para um inquilino diferente do Azure Ative Directory
    - O Azure File Sync suporta agora a deslocação do Serviço de Sincronização de Armazenamento e/ou conta de armazenamento para um grupo de recursos diferente, subscrição ou inquilino AD AZure.
    
- Melhorias de desempenho e fiabilidade diversas
    - A deteção de alterações na partilha de ficheiros Azure pode falhar se as regras de rede virtual (VNET) e firewall estiverem configuradas na conta de armazenamento.
    - Redução do consumo de memória associado à recuperação. 
    - Melhor desempenho ao utilizar o [cmdlet Invoke-AzStorageSyncChangeDetection.](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
    - Outras melhorias de fiabilidade diversas. 
    
### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implementar o Azure File Sync, deverá avaliar se é compatível com o seu sistema utilizando a ferramenta de avaliação Azure File Sync. Esta ferramenta é um cmdlet Azure PowerShell que verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão OS não suportada. Para obter instruções de instalação e utilização, consulte a secção [ferramenta de avaliação](./storage-sync-files-planning.md#evaluation-cmdlet) no guia de planeamento. 

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
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efecção feitas na partilha de ficheiros Azure precisam primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de alterações é iniciado para um ponto final em nuvem uma vez a cada 24 horas. Para sincronizar imediatamente ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet Dedlet PowerShell da [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) powerShell pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Além disso, as alterações efetuadas a uma partilha de ficheiros Azure sobre o protocolo REST não atualizarão o último tempo modificado do SMB e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento pode ser transferido para um grupo de recursos diferente, subscrição ou inquilino AD AZure. Depois de o serviço de sincronização de armazenamento ou a conta de armazenamento ser movido, é necessário dar à aplicação Microsoft.StorageSync acesso à conta de armazenamento (ver [Ensure Azure File Sync tem acesso à conta de armazenamento).](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > Ao criar o ponto final da nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento devem estar no mesmo inquilino AD AZure. Uma vez criado o ponto final da nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento podem ser transferidos para diferentes inquilinos AD AZure.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os tempos de ficheiro. Isto irá garantir que os ficheiros mais antigos são tiered mais cedo do que os ficheiros recentemente acedidos.
