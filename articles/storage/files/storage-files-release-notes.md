---
title: Notas de lançamento para o agente Azure File Sync | Microsoft Docs
description: Leia as notas de lançamento do agente Azure File Sync, que permite centralizar as ações de ficheiros da sua organização em Ficheiros Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 4f2bdf3d5c34ffb4f3ec95c27bbdaf312adb4204
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526403"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notas de versão do agente do Azure File Sync
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. As suas instalações do Windows Server são transformadas numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS). Pode ter o número de caches que precisar em todo o mundo.

Este artigo disponibiliza as notas de versão das versões suportadas do agente do Azure File Sync.

## <a name="supported-versions"></a>Versões suportadas
As seguintes versões do agente Azure File Sync são suportadas:

| Marco | Número de versão do agente | Data da versão | Estado |
|----|----------------------|--------------|------------------|
| V11.2 Lançamento - [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 de fevereiro de 2021 | Suportado - Voo |
| V11.1 Lançamento - [KB4539951](https://support.microsoft.com/en-us/help/4539951)| 11.1.0.0 | 4 de novembro de 2020 | Suportado |
| V10.1 Lançamento - [KB4522411](https://support.microsoft.com/en-us/help/4522411)| 10.1.0.0 | 5 de junho de 2020 | Suportado |
| Rollup de atualização de maio de 2020 - [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 de maio de 2020 | Suportado |
| V10 Release - [KB4522409](https://support.microsoft.com/en-us/help/4522409)| 10.0.0.0 | 9 de abril de 2020 | Suportado |
| Rollup de atualização de dezembro de 2019 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 de dezembro de 2019 | Suportado - Versão do agente expirará a 16 de fevereiro de 2021 |
| Lançamento V9 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 de dezembro de 2019 | Suportado - Versão do agente expirará a 16 de fevereiro de 2021 |
| Lançamento V8 - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 de outubro de 2019 | Suportado - Versão do agente expirará a 12 de janeiro de 2021 |

## <a name="unsupported-versions"></a>Versões não suportadas
As seguintes versões do agente Azure File Sync expiraram e já não estão suportadas:

| Marco | Número de versão do agente | Data da versão | Estado |
|----|----------------------|--------------|------------------|
| Lançamento V7 | 7.0.0.0 - 7.2.0.0 | N/D | Não Suportado - Versões de agente expiradas a 1 de setembro de 2020 |
| Lançamento V6 | 6.0.0.0 - 6.3.0.0 | N/D | Não Suportado - Versões de agente expiradas em 21 de abril de 2020 |
| Lançamento V5 | 5.0.2.0 - 5.2.0.0 | N/D | Não Suportado - Versões de agente expiradas em 18 de março de 2020 |
| Lançamento V4 | 4.0.1.0 - 4.3.0.0 | N/D | Não Suportado - Versões de agente expiradas em 6 de novembro de 2019 |
| Lançamento V3 | 3.1.0.0 - 3.4.0.0 | N/D | Não Suportado - Versões de agente expiradas em 19 de agosto de 2019 |
| Agentes pré-GA | 1.1.0.0 - 3.0.13.0 | N/D | Não Suportado - Versões de agente expiradas a 1 de outubro de 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente do Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-11200"></a>Versão agente 11.2.0.0
As seguintes notas de lançamento são para a versão 11.2.0.0 do agente Azure File Sync lançado a 2 de fevereiro de 2021. Estas notas juntam-se às notas de lançamento listadas para a versão 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Melhorias e problemas que são corrigidos 
- Se uma sessão de sincronização for cancelada devido a um elevado número de erros por item, a sincronização pode passar por uma reconciliação quando uma nova sessão começar se o serviço Azure File Sync determinar que é necessária uma sessão de sincronização personalizada para corrigir os erros por item.
- Registar um servidor utilizando o Register-AzStorageSyncServer cmdlet pode falhar com o erro "Exceção Não Manipulado".
- Novo cmdlet PowerShell (Add-StorageSyncAllowedServerEndpointPath) para configurar os caminhos de pontos finais do servidor permitidos num servidor. Este cmdlet é útil para cenários em que a implementação do Azure File Sync é gerida por um Fornecedor de Solução Cloud (CSP) ou Fornecedor de Serviços e o cliente quer configurar caminhos de pontos finais permitidos num servidor. Ao criar um ponto final do servidor, se o caminho especificado não estiver na lista de permitir, a criação do ponto final do servidor falhará. Nota: esta é uma funcionalidade opcional e todos os caminhos suportados são permitidos por padrão ao criar um ponto final do servidor.  

    
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
    - Os utilizadores podem agora também restaurar ficheiros hierárquicos (juntamente com ficheiros em disco) utilizando a funcionalidade de versão anterior, a partir dos instantâneos VSS que são criados após a funcionalidade de restauro de autosserviço ser ativada no volume. Antes do lançamento do V9, a versão anterior não era suportada por ficheiros hierárquicos. Esta função deve ser ativada para cada volume separadamente, no qual existe um ponto final com nivelamento de nuvem ativado. Para saber mais, ver  
[Autosserviço restaurado através de versões anteriores e VSS (Serviço de Cópia sombra de volume)](./storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Suporte para tamanhos de partilha de ficheiros maiores 
    - O Azure File Sync suporta agora até 64TiB e 100 milhões de ficheiros num único espaço de nome sincronizado.  
 
- Suporte de desduplicação de dados no Servidor 2019 
    - A Deduplica de dados é agora suportada (independentemente de o tiering de nuvem estar ativado ou desativado em um ou mais pontos finais do servidor no volume) no Windows Server 2016 e no Windows Server 2019. Para suportar a desduplicação de dados em volumes com nivelamento em nuvem no Servidor 2019, a atualização do Windows [KB4520062](https://support.microsoft.com/help/4520062) deve ser instalada. 
 
- Melhorado tamanho mínimo de ficheiro para um ficheiro para tier 
    - O tamanho mínimo do ficheiro para um ficheiro para o nível baseia-se agora no tamanho do cluster do sistema de ficheiros (o dobro do tamanho do cluster do sistema de ficheiros). Por exemplo, por padrão, o tamanho do cluster do sistema de ficheiros NTFS é 4KB, o tamanho mínimo de ficheiro resultante para um ficheiro para o nível é 8KB. 
 
- Cmdlet de teste de conectividade de rede 
    - Como parte da configuração do Azure File Sync, vários pontos finais de serviço devem ser contactados. Cada um tem o seu próprio nome DNS que precisa de ser acessível ao servidor. Estes URLs também são específicos da região para a qual um servidor está registado. Uma vez registado um servidor, o cmdlet de teste de conectividade (Utilitário de Registo de Energia e Servidor) pode ser utilizado para testar comunicações com todos os URLs específicos deste servidor. Este cmdlet pode ajudar a resolver problemas quando a comunicação incompleta impede o servidor de funcionar plenamente com o Azure File Sync e pode ser usado para afinar configurações de procuração e firewall.  
 
        Para executar o teste de conectividade da rede, executar os seguintes comandos PowerShell: 
 
        Import-Module "C:\Programa Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Remova a melhoria do ponto final do servidor quando o tiering da nuvem estiver ativado 
    - Como antes, remover um ponto final do servidor não resulta na remoção de ficheiros na partilha de ficheiros Azure. No entanto, o comportamento para reparse pontos no servidor local mudou. Os pontos de reparse (ponteiros para ficheiros que não são locais no servidor) são agora eliminados ao remover um ponto final do servidor. Os ficheiros totalmente em cache permanecerão no servidor. Esta melhoria foi feita para evitar [ficheiros órfãs ao](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) remover um ponto final do servidor. Se o ponto final do servidor for recriado, os pontos de reparse para os ficheiros hierárquicos serão recriados no servidor.  
 
- Melhorias de desempenho e fiabilidade 
    - Falhas de recuperação reduzidas. O tamanho da recuperação é agora automaticamente ajustado com base na largura de banda da rede. 
    - Melhor desempenho do download ao adicionar um novo servidor a um grupo de sincronização. 
    - Ficheiros reduzidos que não sincronizam devido a conflitos de restrição. 
    - Os ficheiros não têm tier ou são inesperadamente recolhidos em certos cenários se o caminho do ponto final do servidor for um ponto de montagem de volume.
    
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
- Os ficheiros hierárquicos tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de eliminarem o ponto final do servidor. Para restaurar o acesso aos ficheiros, recrie o ponto final do servidor. Se passaram 30 dias desde que o ponto final do servidor foi eliminado ou se o ponto final da nuvem foi eliminado, os ficheiros hierárquicos que não foram recolhidos serão inutilizáveis. Para saber mais, ver [ficheiros Tiered não estão acessíveis no servidor depois de eliminar um ponto final do servidor](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.
- O Clustering de Ativação Pós-falha só é suportado com discos em cluster, mas não com Volumes Partilhados de Cluster (CSVs).
- Não é possível aninhar um ponto final do servidor. Pode coexistir no mesmo volume em paralelo com outro ponto final.
- Não guarde um ficheiro de paging de sos ou de aplicação dentro de um ponto final do servidor.
- O nome do servidor no portal não é atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efecção feitas na partilha de ficheiros Azure precisam primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de alterações é iniciado para um ponto final em nuvem uma vez a cada 24 horas. Para sincronizar imediatamente ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet Dedlet PowerShell da [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) powerShell pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Além disso, as alterações efetuadas a uma partilha de ficheiros Azure sobre o protocolo REST não atualizarão o último tempo modificado do SMB e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento pode ser transferido para um grupo de recursos diferente ou subscrição dentro do inquilino AD AZure existente. Se a conta de armazenamento for movida, tem de dar acesso ao Serviço de Sincronização de Ficheiros Híbridos à conta de armazenamento (ver [Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento).](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

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
- Os ficheiros hierárquicos tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de eliminarem o ponto final do servidor. Para restaurar o acesso aos ficheiros, recrie o ponto final do servidor. Se passaram 30 dias desde que o ponto final do servidor foi eliminado ou se o ponto final da nuvem foi eliminado, os ficheiros hierárquicos que não foram recolhidos serão inutilizáveis. Para saber mais, ver [ficheiros Tiered não estão acessíveis no servidor depois de eliminar um ponto final do servidor](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.
- O Clustering de Ativação Pós-falha só é suportado com discos em cluster, mas não com Volumes Partilhados de Cluster (CSVs).
- Não é possível aninhar um ponto final do servidor. Pode coexistir no mesmo volume em paralelo com outro ponto final.
- Não guarde um ficheiro de paging de sos ou de aplicação dentro de um ponto final do servidor.
- O nome do servidor no portal não é atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
- O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efecção feitas na partilha de ficheiros Azure precisam primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de alterações é iniciado para um ponto final em nuvem uma vez a cada 24 horas. Para sincronizar imediatamente ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet Dedlet PowerShell da [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) powerShell pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Além disso, as alterações efetuadas a uma partilha de ficheiros Azure sobre o protocolo REST não atualizarão o último tempo modificado do SMB e não serão vistas como uma alteração por sincronização.
- O serviço de sincronização de armazenamento e/ou conta de armazenamento pode ser transferido para um grupo de recursos diferente ou subscrição dentro do inquilino AD AZure existente. Se a conta de armazenamento for movida, tem de dar acesso ao Serviço de Sincronização de Ficheiros Híbridos à conta de armazenamento (ver [Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento).](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > A Azure File Sync não suporta mover a subscrição para um inquilino AD Azure diferente.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
- Se um ficheiro disposto em camadas for copiado para outra localização com o Robocopy, o ficheiro resultante não é disposto em camadas. O atributo offline pode estar definido porque o Robocopy inclui incorretamente esse atributo nas operações de cópia.
- Ao copiar ficheiros utilizando robocopia, utilize a opção /MIR para preservar os tempos de ficheiro. Isto irá garantir que os ficheiros mais antigos são tiered mais cedo do que os ficheiros recentemente acedidos.
