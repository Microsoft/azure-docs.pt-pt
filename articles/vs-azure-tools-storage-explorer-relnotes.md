---
title: Notas de lançamento do Microsoft Azure Storage Explorer
description: Notas de lançamento para o Microsoft Azure Storage Explorer
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 986da8980a569583ef454833957ace85dd1bfbb6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80351063"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Notas de lançamento do Microsoft Azure Storage Explorer

Este artigo contém as mais recentes notas de lançamento para o Azure Storage Explorer, bem como notas de lançamento para versões anteriores. 

O [Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados do Armazenamento Azure no Windows, macOS e Linux.

Para descarregar versões anteriores do Storage Explorer, pode visitar a página de [Lançamentos](https://github.com/microsoft/AzureStorageExplorer/releases) do nosso repo GitHub.

## <a name="version-1110"></a>Versão 1.11.0
11/4/2019

### <a name="new"></a>Novo
* As operações para Blobs, ADLS Gen2 e Disquetes Geridos utilizam o AzCopy integrado. Mais especificamente, as seguintes operações são feitas com recurso ao AzCopy:
   * Blobs
      * Aberto para edição + Upload
      * Upload, incluindo arrastar & queda
      * Transferência
      * Copiar & pasta #1249
      * Eliminar
   * ADLS Gen2 Blobs
      * Upload, incluindo arrastar & queda
      * Transferência
      * Copiar pasta &
      * Eliminar, incluindo a eliminação de pasta
   * Managed Disks
      * Carregar
      * Transferência
      * Copiar pasta &

   Adicionalmente, várias funcionalidades frequentemente solicitadas foram adicionadas à experiência Integrada AzCopy:
   * Resoluções de conflitos - serão solicitados durante as transferências para resolver conflitos. #1455
   * Faça upload como blobs de página - pode escolher se a AzCopy carrega ou não ficheiros .vhd e .vhdx como blobs de página. #1164 e #1601
   * Parâmetros AzCopy configuráveis - Várias definições foram adicionadas para afinar o desempenho e o uso de recursos da AzCopy. Veja mais detalhes abaixo.

* Para permitir o acesso multi-protocolo da ADLS Gen2 e blobs e melhorar ainda mais as experiências aDLS Gen2, adicionámos as seguintes funcionalidades para as contas ADLS Gen2:
   * Pesquisar usando nomes amigáveis para definir permissões ACL
   * Ver recipientes escondidos, como $logs e $web
   * Adquirir e quebrar o aluguer de contentores
   * Adquirir e quebrar #848 de arrendamento Blob
   * Gerir as políticas de acesso a contentores
   * Configure os níveis de acesso blob
   * Copiar & Pastas Blobs

* Neste lançamento, estamos a pré-visualizar 17 línguas adicionais. Pode mudar para um idioma à sua escolha na página de definições em "Aplicação" → "Definições Regionais" → "Idioma (Pré-visualização)". Continuamos a trabalhar arduamente na tradução de cordas adicionais e na melhoria da qualidade da tradução. Se tiver algum feedback sobre uma tradução, ou se notar uma cadeia que ainda não está traduzida, por favor [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* Em cada lançamento, tentamos embarcar em algumas configurações para permitir a fina viragem do Storage Explorer. Nesta versão, adicionámos configurações para configurar ainda mais o AzCopy, bem como para ocultar os nós de serviço:
   * Limite de largura de banda AzCopy - ajuda a controlar a quantidade de rede que a AzCopy utiliza. Pode encontrar esta definição em "Transferências" → "AzCopy" → "Taxa máxima de transferência". #1099
   * Verificação AzCopy MD5 - permite configurar se e quão estritamente AzCopy verifica para hashes MD5 no download. Pode encontrar esta definição em "Transfers" → "AzCopy" → "Check MD5".
   * AzCopy concurrency e o tamanho do amortecedor de memória - por padrão, a AzCopy analisará a sua máquina para determinar valores de padrão razoáveis para estas definições. Mas se tiver problemas de desempenho, estas definições avançadas podem ser usadas para adaptar ainda mais a forma como a AzCopy funciona no seu computador. Pode encontrar estas definições em "Transfers" → "AzCopy". #994
   * Mostrar e ocultar os nódosos de serviço - estas definições dão-lhe as opções para exibir ou esconder qualquer um dos serviços Azure que o Storage Explorer suporta. Pode encontrar estas definições na secção "Serviços". #1877

* Ao criar um Instantâneo de um Disco Gerido, um nome predefinido é agora fornecido. #1847
* Ao ligar-se ao Azure AD, se anexar um recipiente ADLS Gen2 Blob, então "(ADLS Gen2)" será mostrado ao lado do nó. #1861

### <a name="fixes"></a>Correções
* Ao copiar, carregar ou descarregar discos grandes, o Storage Explorer por vezes não revogao o acesso aos discos envolvidos na operação. Este problema foi corrigido. #2048
* As estatísticas de tabelas falharam ao visualizar uma consulta de chave de partição. Este problema foi corrigido. #1886

### <a name="known-issues"></a>Problemas Conhecidos
* O Storage Explorer 1.11.0 necessita agora de um ponto final DFS (como "myaccount.dfs.core.windows.net") para fixar aos recipientes ADLS Gen2. Versões anteriores do Storage Explorer permitiram-lhe usar um ponto final blob. Estes anexos podem deixar de funcionar após a atualização para 1.11.0. Se encontrar este problema, recoloque utilizando o ponto final dfS.
* As definições numéricas não são verificadas para saber se se encontram num intervalo válido.#2140
* A cópia dos recipientes de bolha de uma conta de armazenamento para outra na vista da árvore pode falhar. Estamos a investigar o assunto.#2124
* A definição de Atualização automática ainda não afeta todas as operações no Blob Explorer.
* As funcionalidades do Disco Gerido não são suportadas no Azure Stack.
* Se um carregamento ou pasta de disco falhar e um novo Disco tiver sido criado antes da falha, o Storage Explorer não eliminará o Disco para si.
* Dependendo de quando cancela um upload ou pasta de disco, é possível deixar o novo Disco num estado corrompido. Se isto acontecer, ou é necessário apagar o novo Disco ou ligar manualmente para as APIs do Disco para substituir o conteúdo do Disco de modo a que já não esteja corrompido.
* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o guia de resolução de [problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para mais informações.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolha, pode causar um erro que impede que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão. Para contornar este problema ao carregar ou descarregar a partir de um recipiente de blob, pode utilizar a funcionalidade Experimental AzCopy.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* O Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Eliminação de Forma Recuperável
   * ADLS Gen2
   * Managed Disks
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Executar o Explorador de Armazenamento em Linux requer que certas dependências sejam instaladas primeiro. Consulte o guia de resolução de [problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) do Explorador de Armazenamento para obter mais informações.

## <a name="previous-releases"></a>Versões anteriores

* [Versão 1.10.1](#version-1101)
* [Versão 1.10.0](#version-1100)
* [Versão 1.9.0](#version-190)
* [Versão 1.8.1](#version-181)
* [Versão 1.8.0](#version-180)
* [Versão 1.7.0](#version-170)
* [Versão 1.6.2](#version-162)
* [Versão 1.6.1](#version-161)
* [Versão 1.6.0](#version-160)
* [Versão 1.5.0](#version-150)
* [Versão 1.4.4](#version-144)
* [Versão 1.4.3](#version-143)
* [Versão 1.4.2](#version-142)
* [Versão 1.4.1](#version-141)
* [Versão 1.3.0](#version-130)
* [Versão 1.2.0](#version-120)
* [Versão 1.1.0](#version-110)
* [Versão 1.0.0](#version-100)
* [Versão 0.9.6](#version-096)
* [Versão 0.9.5](#version-095)
* [Versão 0.9.4 e 0.9.3](#version-094-and-093)
* [Versão 0.9.2](#version-092)
* [Versão 0.9.1 e 0.9.0](#version-091-and-090)
* [Versão 0.8.16](#version-0816)
* [Versão 0.8.14](#version-0814)
* [Versão 0.8.13](#version-0813)
* [Versão 0.8.12 e 0.8.11 e 0.8.10](#version-0812-and-0811-and-0810)
* [Versão 0.8.9 e 0.8.8](#version-089-and-088)
* [Versão 0.8.7](#version-087)
* [Versão 0.8.6](#version-086)
* [Versão 0.8.5](#version-085)
* [Versão 0.8.4](#version-084)
* [Versão 0.8.3](#version-083)
* [Versão 0.8.2](#version-082)
* [Versão 0.8.0](#version-080)
* [Versão 0.7.20160509.0](#version-07201605090)
* [Versão 0.7.20160325.0](#version-07201603250)
* [Versão 0.7.20160129.1](#version-07201601291)
* [Versão 0.7.20160105.0](#version-07201601050)
* [Versão 0.7.20151116.0](#version-07201511160)

## <a name="version-1101"></a>Versão 1.10.1
9/19/2019

### <a name="hotfix"></a>Hotfix
* Alguns utilizadores encontraram um erro em 1.10.0 enquanto tentavam visualizar os seus dados nas suas contas ADLS Gen 1. Este erro impediu que o painel explorador se tornasse correto. Este problema foi corrigido. #1853 #1865

### <a name="new"></a>Novo
* O Storage Explorer tem agora um UI de Definições dedicado. Pode aceder-lhe a partir de Definições Editar → ou clicando no ícone Definições (a engrenagem) na barra de ferramentas vertical esquerda. Esta funcionalidade é o primeiro passo que estamos a dar para fornecer uma variedade de [configurações solicitadas](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate)pelo utilizador. A partir desta versão são suportadas as seguintes definições:
  * Tema
  * Proxy
  * Início de sessão no #6 de saída
  * Ativar o sinal de fluxo de código do dispositivo
  * Atualização automática #1526
  * Ativar a AzCopy
  * Duração do AzCopy SAS Se houver outras definições que gostaria de ver adicionadas, por favor [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) descrevendo a definição que pretende ver.
* O Storage Explorer agora suporta discos geridos. Pode:
  * Faça upload de um VHD no local para um novo Disco
  * Baixar um Disco
  * Copiar e colar discos entre grupos e regiões de recursos
  * Eliminar discos
  * Criar um instantâneo de um disco

O upload, download e cópia transversal dos discos são alimentados pela AzCopy v10.
* O Storage Explorer pode agora ser instalado através da loja Snap em Linux. Quando instalar através da loja Snap, todas as dependências são instaladas para si, incluindo .NET Core! Atualmente, verificamos que o Storage Explorer funciona bem em Ubuntu e CentOS. Se encontrar problemas instalados na loja Snap em outros distros Linux, por favor [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Para saber mais sobre a instalação na loja Snap, consulte o nosso [guia de arranque.](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux) #68
* Foram feitas duas grandes alterações para anexar ao Azure Ative Directory (Azure AD) que se destinam a tornar a funcionalidade mais útil para os utilizadores da ADLS Gen2:
  * Agora seleciona o inquilino em que o recurso que está a anexar está. Isto significa que já não precisa de ter acesso rBAC à subscrição do recurso.
  * Se estiver a anexar um recipiente de bolhas ADLS Gen2, pode agora ser ligado a um caminho específico no recipiente.
* Ao gerir OS ACLs para ficheiros e pastas ADLS Gen2, o Storage Explorer irá agora mostrar os nomes amigáveis para entidades da ACL. #957
* Ao adicionar via OID a um ADLS Gen2 ACL, o Storage Explorer irá agora validar que o OID pertence a uma entidade válida no seu inquilino. #1603
* Os atalhos de teclado para navegar entre separadores utilizam agora combinações de chaves mais padrão. #1018
* O clique do meio num separador vai agora fechá-lo. #1348
* Se uma transferência AzCopy contiver saltos e sem falhas, o Storage Explorer irá agora mostrar um ícone de aviso para realçar que ocorreram saltos. #1490
* O AzCopy integrado foi atualizado para a versão 10.2.1. Além disso, pode agora ver a versão do AzCopy instalada no diálogo Sobre. #1343

### <a name="fixes"></a>Correções
* Muitos utilizadores encontraram vários erros "não conseguem ler a versão de erros indefinidos" ou "não conseguem ler a ligação de erros indefinidos" ao trabalhar em Contas de Armazenamento anexadas. Embora ainda continuemos a investigar a causa principal desta questão, em 1.10.0 melhorámos o manuseamento de erros em torno do carregamento de Contas de Armazenamento anexadas. #1626, #985 e #1532
* Era possível que a árvore exploradora (lado esquerdo) entrasse num estado onde o foco saltava para o nó superior repetidamente. Este problema foi corrigido. #1596
* Ao gerir as fotos de uma bolha, os leitores de ecrã não leriam o carimbo de tempo associado ao instantâneo. Este problema foi corrigido. #1202
* A definição de procuração no macOS não estava a ser definida a tempo de o processo de autenticação os utilizar. Este problema foi corrigido. #1567
* Se uma conta de armazenamento numa nuvem soberana fosse anexada usando nome e chave, a AzCopy não funcionaria. Este problema foi corrigido. #1544
* Ao ligar através de uma cadeia de ligação, o Storage Explorer irá agora remover espaços de trailing. #1387

### <a name="known-issues"></a>Problemas Conhecidos
* A definição de Atualização automática ainda não afeta todas as operações no Blob Explorer.
* As funcionalidades do Disco Gerido não são suportadas no Azure Stack.
* Se um carregamento ou pasta de disco falhar e um novo Disco tiver sido criado antes da falha, o Storage Explorer não eliminará o Disco para si.
* Dependendo de quando cancela um upload ou pasta de disco, é possível deixar o novo Disco num estado corrompido. Se isto acontecer, ou é necessário apagar o novo Disco ou ligar manualmente para as APIs do Disco para substituir o conteúdo do Disco de modo a que já não esteja corrompido.
* Dependendo de quando cancela um upload ou pasta de disco, é possível deixar o novo Disco num estado corrompido. Se isto acontecer, ou é necessário apagar o novo Disco ou ligar manualmente para as APIs do Disco para substituir o conteúdo do Disco de modo a que já não esteja corrompido.
* Ao efetuar um download não-AzCopy Blob, o MD5 para ficheiros grandes não está a ser verificado. Isto deve-se a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o guia de resolução de [problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para mais informações.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolha, pode causar um erro que impede que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão. Para contornar este problema ao carregar ou descarregar a partir de um recipiente de blob, pode utilizar a funcionalidade Experimental AzCopy.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* O Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Eliminação de Forma Recuperável
   * ADLS Gen2
   * Managed Disks
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Executar o Explorador de Armazenamento em Linux requer que certas dependências sejam instaladas primeiro. Consulte o guia de resolução de [problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) do Explorador de Armazenamento para obter mais informações.


## <a name="version-1100"></a>Versão 1.10.0
9/12/2019

### <a name="new"></a>Novo

* O Storage Explorer tem agora um UI de Definições dedicado. Pode aceder-lhe a partir de Definições Editar → ou clicando no ícone Definições (a engrenagem) na barra de ferramentas vertical esquerda. Esta funcionalidade é o primeiro passo que estamos a dar para fornecer uma variedade de [configurações solicitadas](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate)pelo utilizador. A partir desta versão são suportadas as seguintes definições:
    * Tema
    * Proxy
    * Início de sessão no [#6 de](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6) saída
    * Ativar o sinal de fluxo de código do dispositivo
    * Atualização [automática #1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526)
    * Ativar a AzCopy
    * Duração do AzCopy SAS

    Se houver outras definições que gostaria de ver adicionadas, por favor [abra um problema no GitHub descrevendo a configuração que deseja ver](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* O Storage Explorer agora suporta discos geridos. Pode:
    * Faça upload de um VHD no local para um novo Disco
    * Baixar um Disco
    * Copiar e colar discos entre grupos e regiões de recursos
    * Eliminar discos
    * Criar um instantâneo de um disco

    O upload, download e cópia transversal dos discos são alimentados pela AzCopy v10.
* O Storage Explorer pode agora ser instalado através da loja Snap em Linux. Quando instalar através da loja Snap, todas as dependências são instaladas para si, incluindo .NET Core! Atualmente, verificamos que o Storage Explorer funciona bem em Ubuntu e CentOS. Se encontrar problemas instalados na loja Snap em outros distros Linux, por favor [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Para saber mais sobre a instalação na loja Snap, consulte o nosso [guia de arranque.](https://aka.ms/storageexplorer/snapinformation) [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Foram feitas duas grandes alterações para anexar com o Azure Ative Directory (Azure AD) que se destinam a tornar a funcionalidade mais útil para os utilizadores da ADLS Gen2: * Agora selecione o inquilino em que está a anexar. Isto significa que já não precisa de ter acesso rBAC à subscrição do recurso.
        * Se estiver a prender um recipiente de bolhas ADLS Gen2, pode agora ser ligado a um caminho específico no recipiente.
* Ao gerir OS ACLs para ficheiros e pastas ADLS Gen2, o Storage Explorer irá agora mostrar os nomes amigáveis para entidades da ACL. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Ao adicionar via OID a um ADLS Gen2 ACL, o Storage Explorer irá agora validar que o OID pertence a uma entidade válida no seu inquilino. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* Os atalhos de teclado para navegar entre separadores utilizam agora combinações de chaves mais padrão. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* O clique do meio num separador vai agora fechá-lo. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Se uma transferência AzCopy contiver saltos e sem falhas, o Storage Explorer irá agora mostrar um ícone de aviso para realçar que ocorreram saltos. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* O AzCopy integrado foi atualizado para a versão 10.2.1. Além disso, pode agora ver a versão do AzCopy instalada no diálogo Sobre. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Correções

* Muitos utilizadores encontraram vários erros "não conseguem ler a versão de erros indefinidos" ou "não conseguem ler a ligação de erros indefinidos" ao trabalhar em Contas de Armazenamento anexadas. Embora ainda continuemos a investigar a causa principal desta questão, em 1.10.0 melhorámos o manuseamento de erros em torno do carregamento de Contas de Armazenamento anexadas. [#1626,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626) [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)e [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Era possível que a árvore exploradora (lado esquerdo) entrasse num estado onde o foco saltava para o nó superior repetidamente. Este problema foi corrigido. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Ao gerir as fotos de uma bolha, os leitores de ecrã não leriam o carimbo de tempo associado ao instantâneo. Este problema foi corrigido. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* A definição de procuração no macOS não estava a ser definida a tempo de o processo de autenticação os utilizar. Este problema foi corrigido. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Se uma conta de armazenamento numa nuvem soberana fosse anexada usando nome e chave, a AzCopy não funcionaria. Este problema foi corrigido. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Ao ligar através de uma cadeia de ligação, o Storage Explorer irá agora remover espaços de trailing. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Problemas Conhecidos

* A definição de Atualização automática ainda não afeta todas as operações no Blob Explorer.
* As funcionalidades do Disco Gerido não são suportadas no Azure Stack.
* Se um carregamento ou pasta de disco falhar e um novo Disco tiver sido criado antes da falha, o Storage Explorer não eliminará o Disco para si.
* Dependendo de quando cancela um upload ou pasta de disco, é possível deixar o novo Disco num estado corrompido. Se isto acontecer, ou é necessário apagar o novo Disco ou ligar manualmente para as APIs do Disco para substituir o conteúdo do Disco de modo a que já não esteja corrompido.
* Ao efetuar um download não-AzCopy Blob, o MD5 para ficheiros grandes não está a ser verificado. Isto deve-se a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o guia de resolução de [problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para mais informações.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolha, pode causar um erro que impede que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão. Para contornar este problema ao carregar ou descarregar a partir de um recipiente de blob, pode utilizar a funcionalidade Experimental AzCopy.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* O Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Eliminação de Forma Recuperável
   * ADLS Gen2
   * Managed Disks
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Executar o Explorador de Armazenamento em Linux requer que certas dependências sejam instaladas primeiro. Consulte o guia de resolução de [problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) do Explorador de Armazenamento para obter mais informações.

## <a name="version-190"></a>Versão 1.9.0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Baixar Azure Storage Explorer 1.9.0
- [Explorador de armazenamento Azure 1.9.0 para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Explorador de Armazenamento Azure 1.9.0 para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Explorador de Armazenamento Azure 1.9.0 para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Novo

* Agora pode anexar recipientes Blob através de Azure AD (permissões RBAC ou ACL). Esta funcionalidade destina-se a ajudar os utilizadores que tenham acesso aos contentores, mas não as Contas de Armazenamento em que os contentores se encontram. Consulte o nosso Guia Iniciar a Informação para mais informações sobre esta funcionalidade.
* Adquirir e quebrar o arrendamento agora trabalhar com o RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* Gerir as políticas de acesso e fixar o nível de acesso público agora trabalha com o RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* Apagar pastas blob agora funcionam com o RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* A alteração do nível de acesso à bolha funciona agora com o RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Agora pode repor rapidamente o Acesso Rápido através de "Ajuda" → "Reset". [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Funcionalidades de Pré-visualização

* O sinal de fluxo de código do dispositivo está agora disponível para pré-visualização. Para o ativar, aceda a "Preview" → "Use device Code Flow Sign-in". Encorajamos todos os utilizadores que tenham tido problemas com janelas de início de sessão em branco a experimentaresta funcionalidade, uma vez que pode revelar-se uma forma mais fiável de iniciar sessão.
* O Storage Explorer integrado com o AzCopy está atualmente disponível para pré-visualização. Para o ativar, vá a "Preview" → "Use a AzCopy para melhor upload e download de blob". As transferências blob concluídas com a AzCopy devem ser mais rápidas e performantes.

### <a name="fixes"></a>Correções

* Fixamente incapaz de carregar mais de 50 assinaturas para uma conta. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Fixe o botão "Iniciar sessão" não funcionando na barra de informação que aparece quando um link direto falha. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Corrigido não ser para carregar ficheiros .app no macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Fixo "Retry All" não funcionando para um nome de bolha falhado. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Fixo "Cancelar" não funcionar durante a abertura de uma bolha. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Problemas de ortografia e ponta de ferramenta seleções em todo o produto. Muito obrigado a todos os que relataram estes problemas! [#1303,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303) [#1328,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328) [#1329 #1331,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329) [#1336,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331) [#1352,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352) [#1368,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368) [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395) [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336)

### <a name="known-issues"></a>Problemas Conhecidos

* Ao efetuar um download não-AzCopy Blob, o MD5 para ficheiros grandes não está a ser verificado. Isto deve-se a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o guia de resolução de [problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para mais informações.
* Tentar aceder a ADLS Gen2 Blobs quando atrás de um proxy pode falhar.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolha, pode causar um erro que impede que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão. Para contornar este problema ao carregar ou descarregar a partir de um recipiente de blob, pode utilizar a funcionalidade Experimental AzCopy.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* O Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Eliminação de Forma Recuperável
   * ADLS Gen2
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Executar o Explorador de Armazenamento em Linux requer que certas dependências sejam instaladas primeiro. Consulte o guia de resolução de [problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) do Explorador de Armazenamento para obter mais informações.

## <a name="version-181"></a>Versão 1.8.1
5/13/2019

### <a name="hotfixes"></a>Correções
* Em alguns casos, clicar em "Carregar mais" ao nível dos recursos não devolveria a página seguinte dos recursos. Este problema foi corrigido. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* No Windows, os downloads do AzCopy falhariam se um único ficheiro ou pasta estivesse a ser descarregado e o nome do ficheiro ou pasta tivesse um personagem que fosse inválido para um caminho windows. Este problema foi corrigido. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* Em casos extremamente raros, ao realizar um nome de uma Partilha de Ficheiros ou um nome numa Partilha de Ficheiros, se as cópias para o renome falharam, ou se o Storage Explore não foi capaz de confirmar o sucesso das cópias com o Azure, havia o potencial para o Storage Explorer apagar os ficheiros originais antes da cópia ter terminado. Este problema foi corrigido.

### <a name="new"></a>Novo

* A versão Integrada aZCopy foi atualizada para a versão 10.1.0.
* Ctrl/Cmd+R pode agora ser usado para refrescar o editor atualmente focado. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* A versão API de Armazenamento de Pilhas Azure foi alterada para 2017-04-17.
* O diálogo Manage Access para ADLS Gen2 irá agora manter a Máscara sincronizada de uma forma semelhante a outras ferramentas de permissões POSIX. O UI também irá avisá-lo se for feita uma alteração que faça com que as permissões de um utilizador ou grupo excedam os limites da Máscara. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Para uploads AzCopy, a bandeira para calcular e definir o haxixe MD5 está agora ativada. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funcionalidades de Pré-visualização

* O sinal de fluxo de código do dispositivo está agora disponível para pré-visualização. Para o ativar, aceda a "Preview" → "Use device Code Flow Sign-in". Encorajamos todos os utilizadores que tenham tido problemas com janelas de início de sessão em branco a experimentaresta funcionalidade, uma vez que pode revelar-se uma forma mais fiável de iniciar sessão.
* O Storage Explorer integrado com o AzCopy está atualmente disponível para pré-visualização. Para o ativar, vá a "Preview" → "Use a AzCopy para melhor upload e download de blob". As transferências blob concluídas com a AzCopy devem ser mais rápidas e performantes.

### <a name="fixes"></a>Correções

* O diálogo das Políticas de Acesso deixará de fixar uma data de validade nas Políticas de Acesso ao Armazenamento que não tenham caducidade. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Foram feitas algumas alterações no diálogo Generate SAS para garantir que as Políticas de Acesso Armazenadas são utilizadas corretamente ao gerar um SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Ao tentar enviar um ficheiro não-512 alinhado para uma página Blob, o Storage Explorer irá agora expor um erro mais relevante. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Copiar um recipiente Blob que utilizasse um nome de exibição falharia. Agora, o nome verdadeiro do recipiente Blob é usado. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Tentar executar certas ações numa pasta ADLS Gen2 que tivesse caracteres unicódigo em seu nome falharia. Todas as ações devem agora funcionar. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Problemas Conhecidos

* Ao efetuar um download não-AzCopy Blob, o MD5 para ficheiros grandes não está a ser verificado. Isto deve-se a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o guia de resolução de [problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para mais informações.
* Tentar aceder a ADLS Gen2 Blobs quando atrás de um proxy pode falhar.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolha, pode causar um erro que impede que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão. Para contornar este problema ao carregar ou descarregar a partir de um recipiente de blob, pode utilizar a funcionalidade Experimental AzCopy.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* O Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Eliminação de Forma Recuperável
   * ADLS Gen2
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Executar o Explorador de Armazenamento em Linux requer que certas dependências sejam instaladas primeiro. Consulte o guia de resolução de [problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) do Explorador de Armazenamento para obter mais informações.

## <a name="version-180"></a>Versão 1.8.0
01/05/2019

### <a name="new"></a>Novo

* A versão Integrada aZCopy foi atualizada para a versão 10.1.0.
* Ctrl/Cmd+R pode agora ser usado para refrescar o editor atualmente focado. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* A versão API de Armazenamento de Pilhas Azure foi alterada para 2017-04-17.
* O diálogo Manage Access para ADLS Gen2 irá agora manter a Máscara sincronizada de uma forma semelhante a outras ferramentas de permissões POSIX. O UI também irá avisá-lo se for feita uma alteração que faça com que as permissões de um utilizador ou grupo excedam os limites da Máscara. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Para uploads AzCopy, a bandeira para calcular e definir o haxixe MD5 está agora ativada. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funcionalidades de Pré-visualização

* O sinal de fluxo de código do dispositivo está agora disponível para pré-visualização. Para o ativar, aceda a "Preview" → "Use device Code Flow Sign-in". Encorajamos todos os utilizadores que tenham tido problemas com janelas de início de sessão em branco a experimentaresta funcionalidade, uma vez que pode revelar-se uma forma mais fiável de iniciar sessão.
* O Storage Explorer integrado com o AzCopy está atualmente disponível para pré-visualização. Para o ativar, vá a "Preview" → "Use a AzCopy para melhor upload e download de blob". As transferências blob concluídas com a AzCopy devem ser mais rápidas e performantes.

### <a name="fixes"></a>Correções

* O diálogo das Políticas de Acesso deixará de fixar uma data de validade nas Políticas de Acesso ao Armazenamento que não tenham caducidade. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Foram feitas algumas alterações no diálogo Generate SAS para garantir que as Políticas de Acesso Armazenadas são utilizadas corretamente ao gerar um SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Ao tentar enviar um ficheiro não-512 alinhado para uma página Blob, o Storage Explorer irá agora expor um erro mais relevante. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Copiar um recipiente Blob que utilizasse um nome de exibição falharia. Agora, o nome verdadeiro do recipiente Blob é usado. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Tentar executar certas ações numa pasta ADLS Gen2 que tivesse caracteres unicódigo em seu nome falharia. Todas as ações devem agora funcionar. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Problemas Conhecidos

* Ao efetuar um download não-AzCopy Blob, o MD5 para ficheiros grandes não está a ser verificado. Isto deve-se a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o guia de resolução de [problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para mais informações.
* Tentar aceder a ADLS Gen2 Blobs quando atrás de um proxy pode falhar.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolha, pode causar um erro que impede que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão. Para contornar este problema ao carregar ou descarregar a partir de um recipiente de blob, pode utilizar a funcionalidade Experimental AzCopy.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* O Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Eliminação de Forma Recuperável
   * ADLS Gen2
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Executar o Explorador de Armazenamento em Linux requer que certas dependências sejam instaladas primeiro. Consulte o guia de resolução de [problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) do Explorador de Armazenamento para obter mais informações.

## <a name="version-170"></a>Versão 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Baixar Azure Storage Explorer 1.7.0
- [Explorador de armazenamento Azure 1.7.0 para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Explorador de Armazenamento Azure 1.7.0 para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Explorador de Armazenamento Azure 1.7.0 para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Novo

* Agora pode alterar o proprietário e o grupo proprietário ao gerir o acesso a um recipiente, ficheiro ou pasta ADLS Gen2.
* No Windows, atualizar o Storage Explorer a partir do interior do produto é agora uma instalação incremental. Isto deve resultar numa experiência de atualização mais rápida. Se preferir uma instalação limpa, pode descarregar o [instalador](https://azure.microsoft.com/features/storage-explorer/) e depois instalar manualmente. #1089

### <a name="preview-features"></a>Funcionalidades de Pré-visualização

* O sinal de fluxo de código do dispositivo está agora disponível para pré-visualização. Para o ativar, aceda a "Preview" → "Use device Code Flow Sign-in". Encorajamos todos os utilizadores que tenham tido problemas com janelas de início de sessão em branco a experimentaresta funcionalidade, uma vez que pode revelar-se uma forma mais fiável de iniciar sessão. #938
* O Storage Explorer integrado com o AzCopy está atualmente disponível para pré-visualização. Para o ativar, vá a "Preview" → "Use a AzCopy para melhor upload e download de blob". As transferências blob concluídas com a AzCopy devem ser mais rápidas e performantes.

### <a name="fixes"></a>Correções

* Agora pode escolher o tipo de bolha que pretende carregar quando o AzCopy está ativado. #1111
* Anteriormente, se tivesse ativado websites estáticos para uma conta de Armazenamento ADLS Gen2 e depois ligado com nome e chave, o Storage Explorer não teria detetado que o espaço de nome hierárquico estava ativado. Este problema foi corrigido. #1081
* No editor blob, a triagem por dias de retenção restantes ou o estatuto foi quebrado. Este problema foi corrigido. #1106
* Após 1.5.0, o Storage Explorer já não esperou que as cópias laterais do servidor terminassem antes de relatar o sucesso durante um renome ou copiar & pasta. Este problema foi corrigido. #976
* Ao utilizar a função Experimental AzCopy, o comando copiado depois de clicar em "Copiar comando para clipboard" nem sempre foi executado por si só. Agora, todos os comandos necessários para executar a transferência manualmente serão copiados. #1079
* Anteriormente, as bolhas ADLS Gen2 não eram acessíveis se estivesse atrás de um proxy. Isto deveu-se a um bug numa nova biblioteca de rede usada pelo Storage SDK. Em 1.7.0, foi feita uma tentativa de mitigar esta questão, mas algumas pessoas podem continuar a ver problemas. Uma correção completa será lançada numa futura atualização. #1090
* Em 1.7.0, o diálogo de ficheiros de salvamento lembra-se agora corretamente da última localização a que guardou um ficheiro. #16
* No painel de propriedades, o nível SKU de uma conta de Armazenamento estava sendo mostrado como o tipo da conta. Este problema foi corrigido. #654
* Às vezes, era impossível quebrar o arrendamento de uma bolha, mesmo que tivesse entrado corretamente no nome da bolha. Este problema foi corrigido. #1070

### <a name="known-issues"></a>Problemas Conhecidos

* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o guia de resolução de [problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para mais informações.
* Tentar aceder a ADLS Gen2 Blobs quando atrás de um proxy pode falhar.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolha, pode causar um erro que impede que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolha, pode causar um erro que impede que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Para mais informações, consulte #537.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão. Para contornar este problema ao carregar ou descarregar a partir de um recipiente de blob, pode utilizar a funcionalidade Experimental AzCopy.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* O Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Eliminação de Forma Recuperável
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Versão 1.6.2
1/9/2019

### <a name="hotfixes"></a>Correções
* Em 1.6.1, as entidades adicionadas aos ADLS Gen2 ACLs pela ObjectId, que não eram utilizadores, foram sempre adicionadas como grupos. Agora, apenas grupos são adicionados como grupos, e entidades como aplicações empresariais e diretores de serviço são adicionados como utilizadores. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Se uma conta de armazenamento ADLS Gen2 não tivesse contentores e estivesse anexada com nome e chave, então o Storage Explorer não detetaria que a Conta de Armazenamento era ADLS Gen2. Este problema foi corrigido. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* Em 1.6.0, os conflitos durante a cópia e a pasta não levariam a uma resolução. Em vez disso, a cópia conflituosa simplesmente falharia. Agora, no primeiro conflito, ser-lhe-ão perguntados como gostaria que fosse resolvido. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Devido às limitações da API, todas as validações de ObjectIds no diálogo Manage Access foram desativadas. A validação ocorrerá agora apenas para as UPNs do utilizador. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* No diálogo ADLS Gen2 Manage Access, as permissões para um grupo não puderam ser modificadas. Este problema foi corrigido. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Adicione suporte de arrastar e largar upload para o editor da ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* A propriedade URL no diálogo de propriedades para ficheiros e pastas ADLS Gen2 às vezes faltava um '/'. Este problema foi corrigido. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Se obter as permissões atuais para um recipiente, ficheiro ou pasta ADLS Gen2 falhar, então o erro é agora corretamente apresentado no registo de atividade. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* O caminho temporário criado para a abertura de ficheiros foi encurtado para reduzir a possibilidade de criar um caminho que seja mais longo do que MAX_PATH no Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* O diálogo Connect aparece agora corretamente quando não há assinado nos utilizadores e não foram anexados recursos. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* Em 1.6.0, a poupança de propriedades para blobs e ficheiros não HNS codificaria o valor de cada imóvel. Isto resultou numa codificação desnecessária de valores que continham apenas caracteres ASCII. Agora, os valores só serão codificados se contiverem caracteres não-ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* O upload de uma pasta para um recipiente blob não HNS falharia se fosse utilizado um SAS e o SAS não tivesse lido permissões. Este problema foi corrigido. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* O cancelamento de uma transferência AzCopy não funcionou. Este problema foi corrigido. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* A AzCopy falharia ao tentar descarregar uma pasta de um recipiente ADLS Gen2 Blob se a pasta tivesse espaços em seu nome. Este problema foi corrigido. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* O editor da CosmosDB foi quebrado em 1.6.0. Está agora arranjado. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Novo

* Agora pode utilizar o Storage Explorer para aceder aos seus dados Blob via [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se estiver inscrito e o Storage Explorer não conseguir recuperar as chaves da sua conta de Armazenamento, então será utilizado um símbolo OAuth para autenticar quando interagir com os seus dados.
* O Storage Explorer suporta agora as contas de armazenamento da ADLS Gen2. Quando o Storage Explorer detetar que o espaço de nome hierárquico está ativado para uma conta de Armazenamento, verá "(ADLS Gen2 Preview)" ao lado do nome da sua conta de Armazenamento. O Storage Explorer é capaz de detetar se o espaço de nome hierárquico está ou não ativado quando está inscrito ou se anexou a sua Conta de Armazenamento com nome e chave. Para contas de armazenamento ADLS Gen2, pode utilizar o Storage Explorer para:
  * Criar e eliminar contentores
  * Gerir propriedades e permissões de contentores (lado esquerdo)
  * Ver e navegar dados dentro de contentores
  * Criar novas pastas
  * Carregar, descarregar, mudar o nome e eliminar ficheiros e pastas
  * Gerir propriedades e permissões de ficheiros e pastas (lado direito).
    
    Outras funcionalidades típicas de Blob, como Soft Delete, e Snapshots, não estão atualmente disponíveis. A gestão de permissões também só está disponível quando assinada. Além disso, ao trabalhar numa conta de Armazenamento ADLS Gen2, o Storage Explorer utilizará o AzCopy para todos os uploads e downloads e padrão para usar o nome e as credenciais-chave para todas as operações, se disponível.
* Após um forte feedback do utilizador, o break lease pode ser usado mais uma vez para quebrar arrendamentos em várias bolhas ao mesmo tempo.

### <a name="known-issues"></a>Problemas Conhecidos

* Ao descarregar a partir de uma conta de Armazenamento ADLS Gen2, se um dos ficheiros que estão a ser transferidos já existe, então a AzCopy por vezes irá falhar. Isto será fixado num hotfix próximo.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolha, pode causar um erro que impede que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Para mais informações, consulte #537.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão. Para contornar este problema ao carregar ou descarregar a partir de um recipiente de blob, pode utilizar a funcionalidade Experimental AzCopy.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* O Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Eliminação de Forma Recuperável
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>Versão 1.6.1
12/18/2018

### <a name="hotfixes"></a>Correções
* Devido às limitações da API, todas as validações de ObjectIds no diálogo Manage Access foram desativadas. A validação ocorrerá agora apenas para as UPNs do utilizador. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* No diálogo ADLS Gen2 Manage Access, as permissões para um grupo não puderam ser modificadas. Este problema foi corrigido. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Adicione suporte de arrastar e largar upload para o editor da ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* A propriedade URL no diálogo de propriedades para ficheiros e pastas ADLS Gen2 às vezes faltava um '/'. Este problema foi corrigido. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Se obter as permissões atuais para um recipiente, ficheiro ou pasta ADLS Gen2 falhar, então o erro é agora corretamente apresentado no registo de atividade. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* O caminho temporário criado para a abertura de ficheiros foi encurtado para reduzir a possibilidade de criar um caminho que seja mais longo do que MAX_PATH no Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* O diálogo Connect aparece agora corretamente quando não há assinado nos utilizadores e não foram anexados recursos. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* Em 1.6.0, a poupança de propriedades para blobs e ficheiros não HNS codificaria o valor de cada imóvel. Isto resultou numa codificação desnecessária de valores que continham apenas caracteres ASCII. Agora, os valores só serão codificados se contiverem caracteres não-ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* O upload de uma pasta para um recipiente blob não HNS falharia se fosse utilizado um SAS e o SAS não tivesse lido permissões. Este problema foi corrigido. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* O cancelamento de uma transferência AzCopy não funcionou. Este problema foi corrigido. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* A AzCopy falharia ao tentar descarregar uma pasta de um recipiente ADLS Gen2 Blob se a pasta tivesse espaços em seu nome. Este problema foi corrigido. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* O editor da CosmosDB foi quebrado em 1.6.0. Está agora arranjado. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Novo

* Agora pode utilizar o Storage Explorer para aceder aos seus dados Blob via [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se estiver inscrito e o Storage Explorer não conseguir recuperar as chaves da sua conta de Armazenamento, então será utilizado um símbolo OAuth para autenticar quando interagir com os seus dados.
* O Storage Explorer suporta agora as contas de armazenamento da ADLS Gen2. Quando o Storage Explorer detetar que o espaço de nome hierárquico está ativado para uma conta de Armazenamento, verá "(ADLS Gen2 Preview)" ao lado do nome da sua conta de Armazenamento. O Storage Explorer é capaz de detetar se o espaço de nome hierárquico está ou não ativado quando está inscrito ou se anexou a sua Conta de Armazenamento com nome e chave. Para contas de armazenamento ADLS Gen2, pode utilizar o Storage Explorer para:
  * Criar e eliminar contentores
  * Gerir propriedades e permissões de contentores (lado esquerdo)
  * Ver e navegar dados dentro de contentores
  * Criar novas pastas
  * Carregar, descarregar, mudar o nome e eliminar ficheiros e pastas
  * Gerir propriedades e permissões de ficheiros e pastas (lado direito).
    
    Outras funcionalidades típicas de Blob, como Soft Delete, e Snapshots, não estão atualmente disponíveis. A gestão de permissões também só está disponível quando assinada. Além disso, ao trabalhar numa conta de Armazenamento ADLS Gen2, o Storage Explorer utilizará o AzCopy para todos os uploads e downloads e padrão para usar o nome e as credenciais-chave para todas as operações, se disponível.
* Após um forte feedback do utilizador, o break lease pode ser usado mais uma vez para quebrar arrendamentos em várias bolhas ao mesmo tempo.

### <a name="known-issues"></a>Problemas Conhecidos

* Ao descarregar a partir de uma conta de Armazenamento ADLS Gen2, se um dos ficheiros que estão a ser transferidos já existe, então a AzCopy por vezes irá falhar. Isto será fixado num hotfix próximo.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolha, pode causar um erro que impede que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Para mais informações, consulte #537.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão. Para contornar este problema ao carregar ou descarregar a partir de um recipiente de blob, pode utilizar a funcionalidade Experimental AzCopy.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* O Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Eliminação de Forma Recuperável
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>Versão 1.6.0
05/12/2018

### <a name="new"></a>Novo

* Agora pode utilizar o Storage Explorer para aceder aos seus dados Blob via [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se estiver inscrito e o Storage Explorer não conseguir recuperar as chaves da sua conta de Armazenamento, então será utilizado um símbolo OAuth para autenticar quando interagir com os seus dados.
* O Storage Explorer suporta agora as contas de armazenamento da ADLS Gen2. Quando o Storage Explorer detetar que o espaço de nome hierárquico está ativado para uma conta de Armazenamento, verá "(ADLS Gen2 Preview)" ao lado do nome da sua conta de Armazenamento. O Storage Explorer é capaz de detetar se o espaço de nome hierárquico está ou não ativado quando está inscrito ou se anexou a sua Conta de Armazenamento com nome e chave. Para contas de armazenamento ADLS Gen2, pode utilizar o Storage Explorer para:
  * Criar e eliminar contentores
  * Gerir propriedades e permissões de contentores (lado esquerdo)
  * Ver e navegar dados dentro de contentores
  * Criar novas pastas
  * Carregar, descarregar, mudar o nome e eliminar ficheiros e pastas
  * Gerir propriedades e permissões de ficheiros e pastas (lado direito).
    
    Outras funcionalidades típicas de Blob, como Soft Delete, e Snapshots, não estão atualmente disponíveis. A gestão de permissões também só está disponível quando assinada. Além disso, ao trabalhar numa conta de Armazenamento ADLS Gen2, o Storage Explorer utilizará o AzCopy para todos os uploads e downloads e padrão para usar o nome e as credenciais-chave para todas as operações, se disponível.
* Após um forte feedback do utilizador, o break lease pode ser usado mais uma vez para quebrar arrendamentos em várias bolhas ao mesmo tempo.

### <a name="known-issues"></a>Problemas Conhecidos

* Ao descarregar a partir de uma conta de Armazenamento ADLS Gen2, se um dos ficheiros que estão a ser transferidos já existe, então a AzCopy por vezes irá falhar. Isto será fixado num hotfix próximo.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolha, pode causar um erro que impede que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Para mais informações, consulte #537.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão. Para contornar este problema ao carregar ou descarregar a partir de um recipiente de blob, pode utilizar a funcionalidade Experimental AzCopy.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* O Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Eliminação de Forma Recuperável
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Versão 1.5.0
10/29/2018

### <a name="new"></a>Novo

* Agora pode utilizar [o AzCopy v10 (Preview)](https://github.com/Azure/azure-storage-azcopy) para fazer o upload e o download de Blobs. Para ativar esta funcionalidade vá para o menu "Experimental" e, em seguida, clique em "Use AzCopy para melhor upload e download blob". Quando ativado, o AzCopy será utilizado nos seguintes cenários:
   * Upload de pastas e ficheiros para recipientes blob, seja através da barra de ferramentas ou arrastar e largar.
   * Descarregamento de pastas e ficheiros, seja através da barra de ferramentas ou do menu de contexto.

* Adicionalmente, ao utilizar o AzCopy:
   * Pode copiar o comando AzCopy utilizado para executar a transferência para a sua área de transferência. Basta clicar em "Copy AzCopy Command to Clipboard" no registo de atividade.
   * Terá de refrescar manualmente o editor blob após o upload.
   * O upload de ficheiros para apêndice blobs não é suportado, e os ficheiros VHD serão carregados como blobs de página, e todos os outros ficheiros serão carregados como blocos de bolhas.
   * Erros e conflitos que ocorram durante o upload ou download só surgirão depois de um upload ou download estar terminado.

Finalmente, o suporte para a utilização do AzCopy com Partilhas de Ficheiros virá no futuro.
* O Storage Explorer está agora a usar a versão 2.0.11 do Eletrão.
* A quebra de contratos de arrendamento só pode ser realizada numa bolha de cada vez. Além disso, você tem que inserir o nome da bolha cujo contrato de arrendamento você está quebrando. Esta alteração foi feita para reduzir a probabilidade de quebrar acidentalmente um contrato de arrendamento, especialmente para os VMs. #394
* Se alguma vez encontrar problemas de inscrição, pode agora tentar reiniciar a autenticação. Vá ao menu "Ajuda" e clique em "Reset" para aceder a esta capacidade. #419

### <a name="fix"></a>Correção

* Após um forte feedback do utilizador, o nó do emulador predefinido foi reativado. Ainda pode adicionar ligações adicionais de emulador através do diálogo Connect, mas se o emulador estiver configurado para utilizar as portas predefinidas, também pode utilizar o nó "Emulator * Portas Predefinidas" em "Contas Locais & Anexadas/Armazenamento". #669
* O Storage Explorer deixará de permitir definir valores de metadados blob que têm espaço branco líder ou de rasto. #760
* O botão "Iniciar a sessão" foi sempre ativado nas mesmas páginas do diálogo Connect. Está agora desativada quando apropriado. #761
* O Quick Access deixará de gerar um erro na consola quando não forem adicionados itens quick Access.

### <a name="known-issues"></a>Problemas Conhecidos

* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolha, pode causar um erro que impede que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Para mais informações, consulte #537.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão. Para contornar este problema ao carregar ou descarregar a partir de um recipiente de blob, pode utilizar a funcionalidade Experimental AzCopy.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* O Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Eliminação de Forma Recuperável
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Versão 1.4.4
10/15/2018

### <a name="hotfixes"></a>Correções
* A versão API de Gestão de Recursos Azure foi relançada para desbloquear utilizadores do Governo dos EUA. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Os rodapés estão agora a usar animações CSS para reduzir a quantidade de GPU utilizada pelo Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Novo
* Os acessórios de recursos externos, tais como ligações SAS e emuladores, foram significativamente melhorados. Agora pode:
   * Personalize o nome do ecrã do recurso que está a anexar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexar a vários emuladores locais utilizando portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicione recursos anexados ao Acesso Rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* O Storage Explorer suporta agora o Soft Delete. Pode:
   * Configure uma política soft Delete clicando no nó blob Containers para a sua conta de Armazenamento.
   * Ver bolhas suaves apagadas no Editor Blob selecionando "Bolhas ativas e apagadas" na queda ao lado da barra de navegação.
   * Não elimine bolhas apagadas macias.

### <a name="fixes"></a>Correções
* A ação "Configurar as Definições CORS" já não está disponível nas contas de Armazenamento Premium porque as contas de Armazenamento Premium não suportam o CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Existe agora uma propriedade De Acesso Partilhado Signature para Serviços Anexos SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação "set Default Access Tier" já está disponível para contas de armazenamento Blob e GPV2 que foram fixadas ao Quick Access. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Storage Explorer não mostraria as contas clássicas de armazenamento. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao utilizar emuladores, como o Emulador de Armazenamento Azure ou o Azurite, terá de os fazer ouvir ligações nas suas portas padrão. Caso contrário, o Storage Explorer não poderá ligar-se a eles.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, por favor comentou [este assunto.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a utilizar o trabalho do filtro de cancelamento [aqui](https://github.com/Azure/azure-storage-node/issues/317)descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Versão 1.4.3
10/11/2018

### <a name="hotfixes"></a>Correções
* A versão API de Gestão de Recursos Azure foi relançada para desbloquear utilizadores do Governo dos EUA. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Os rodapés estão agora a usar animações CSS para reduzir a quantidade de GPU utilizada pelo Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Novo
* Os acessórios de recursos externos, tais como ligações SAS e emuladores, foram significativamente melhorados. Agora pode:
   * Personalize o nome do ecrã do recurso que está a anexar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexar a vários emuladores locais utilizando portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicione recursos anexados ao Acesso Rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* O Storage Explorer suporta agora o Soft Delete. Pode:
   * Configure uma política soft Delete clicando no nó blob Containers para a sua conta de Armazenamento.
   * Ver bolhas suaves apagadas no Editor Blob selecionando "Bolhas ativas e apagadas" na queda ao lado da barra de navegação.
   * Não elimine bolhas apagadas macias.

### <a name="fixes"></a>Correções
* A ação "Configurar as Definições CORS" já não está disponível nas contas de Armazenamento Premium porque as contas de Armazenamento Premium não suportam o CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Existe agora uma propriedade De Acesso Partilhado Signature para Serviços Anexos SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação "set Default Access Tier" já está disponível para contas de armazenamento Blob e GPV2 que foram fixadas ao Quick Access. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Storage Explorer não mostraria as contas clássicas de armazenamento. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao utilizar emuladores, como o Emulador de Armazenamento Azure ou o Azurite, terá de os fazer ouvir ligações nas suas portas padrão. Caso contrário, o Storage Explorer não poderá ligar-se a eles.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, por favor comentou [este assunto.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a utilizar o trabalho do filtro de cancelamento [aqui](https://github.com/Azure/azure-storage-node/issues/317)descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Versão 1.4.2
09/24/2018

### <a name="hotfixes"></a>Correções
* Atualizar a versão API de Gestão de Recursos Azure para 2018-07-01 para adicionar suporte para novos tipos de Conta de Armazenamento Azure. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Novo
* Os acessórios de recursos externos, tais como ligações SAS e emuladores, foram significativamente melhorados. Agora pode:
   * Personalize o nome do ecrã do recurso que está a anexar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexar a vários emuladores locais utilizando portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicione recursos anexados ao Acesso Rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* O Storage Explorer suporta agora o Soft Delete. Pode:
   * Configure uma política soft Delete clicando no nó blob Containers para a sua conta de Armazenamento.
   * Ver bolhas suaves apagadas no Editor Blob selecionando "Bolhas ativas e apagadas" na queda ao lado da barra de navegação.
   * Não elimine bolhas apagadas macias.

### <a name="fixes"></a>Correções
* A ação "Configurar as Definições CORS" já não está disponível nas contas de Armazenamento Premium porque as contas de Armazenamento Premium não suportam o CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Existe agora uma propriedade De Acesso Partilhado Signature para Serviços Anexos SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação "set Default Access Tier" já está disponível para contas de armazenamento Blob e GPV2 que foram fixadas ao Quick Access. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Storage Explorer não mostraria as contas clássicas de armazenamento. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao utilizar emuladores, como o Emulador de Armazenamento Azure ou o Azurite, terá de os fazer ouvir ligações nas suas portas padrão. Caso contrário, o Storage Explorer não poderá ligar-se a eles.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, por favor comentou [este assunto.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a utilizar o trabalho do filtro de cancelamento [aqui](https://github.com/Azure/azure-storage-node/issues/317)descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Versão 1.4.1
08/28/2018

### <a name="hotfixes"></a>Correções
* No primeiro lançamento, o Storage Explorer não conseguiu gerar a chave utilizada para encriptar dados sensíveis. Isto causaria problemas ao utilizar o Quick Access e a anexar recursos. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Se a sua conta não exigisse MFA para o seu inquilino, mas o fizesse para outros inquilinos, o Storage Explorer não poderia listar subscrições. Agora, depois de iniciar sessão com tal conta, o Storage Explorer pedir-lhe-á que reintroduza as suas credenciais e execute o MFA. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* O Storage Explorer não conseguiu anexar recursos da Azure Germany e do Governo dos EUA. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Se você assinou em duas contas que tinham o mesmo endereço de e-mail, O Explorador de Armazenamento às vezes deixaria de mostrar os seus recursos na vista da árvore. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Nas máquinas Windows mais lentas, o ecrã de salpicos demoraria, por vezes, a aparecer uma quantidade significativa de tempo. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* O diálogo de ligação apareceria mesmo que houvesse contas ou serviços anexados. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Novo
* Os acessórios de recursos externos, tais como ligações SAS e emuladores, foram significativamente melhorados. Agora pode:
   * Personalize o nome do ecrã do recurso que está a anexar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexar a vários emuladores locais utilizando portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicione recursos anexados ao Acesso Rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* O Storage Explorer suporta agora o Soft Delete. Pode:
   * Configure uma política soft Delete clicando no nó blob Containers para a sua conta de Armazenamento.
   * Ver bolhas suaves apagadas no Editor Blob selecionando "Bolhas ativas e apagadas" na queda ao lado da barra de navegação.
   * Não elimine bolhas apagadas macias.

### <a name="fixes"></a>Correções
* A ação "Configurar as Definições CORS" já não está disponível nas contas de Armazenamento Premium porque as contas de Armazenamento Premium não suportam o CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Existe agora uma propriedade De Acesso Partilhado Signature para Serviços Anexos SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação "set Default Access Tier" já está disponível para contas de armazenamento Blob e GPV2 que foram fixadas ao Quick Access. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Storage Explorer não mostraria as contas clássicas de armazenamento. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao utilizar emuladores, como o Emulador de Armazenamento Azure ou o Azurite, terá de os fazer ouvir ligações nas suas portas padrão. Caso contrário, o Storage Explorer não poderá ligar-se a eles.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, por favor comentou [este assunto.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a utilizar o trabalho do filtro de cancelamento [aqui](https://github.com/Azure/azure-storage-node/issues/317)descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Versão 1.3.0
07/09/2018

### <a name="new"></a>Novo
* O acesso às $web recipientes utilizados pelos Sites Estáticos é agora suportado. Isto permite-lhe carregar e gerir facilmente ficheiros e pastas utilizados pelo seu website. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* A barra de aplicações no macOS foi reorganizada. As alterações incluem um menu de Ficheiros, algumas alterações na chave de atalho e vários novos comandos no menu da aplicação. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* O ponto final da autoridade para a assinatura no Governo dos EUA Azure foi alterado parahttps://login.microsoftonline.us/
* Acessibilidade: Quando um leitor de ecrã está ativo, a navegação por teclado funciona agora com as tabelas utilizadas para exibir itens no lado direito. Pode utilizar as teclas de seta para navegar em linhas e colunas, entrar para invocar ações predefinidas, a chave do menu de contexto para abrir o menu de contexto para um item, e Mudar ou Controlar para multiselecção. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Correções
*  Em algumas máquinas, os processos infantis demoravam muito tempo a começar. Quando isso aconteceria, um erro de "processo infantil não começou atempadamente". O tempo atribuído para o início do processo infantil foi agora aumentado de 20 para 90 segundos. Se ainda for afetado por este problema, por favor comentou a questão do GitHub ligado. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Ao utilizar um SAS que não tivesse lido permissões, não era possível carregar uma grande bolha. A lógica para o upload foi modificada para funcionar neste cenário. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* A fixação do nível de acesso público a um contentor eliminaria todas as políticas de acesso, e vice-versa. Agora, o nível de acesso público e as políticas de acesso são preservados ao definir qualquer um dos dois. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" foi truncado no diálogo Properties. Este problema foi corrigido. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* Faltava o prefixo "Microsoft Azure Storage Explorer -" no diálogo Create New Directory. Este problema foi corrigido. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Acessibilidade: O diálogo da Entidade Adicionar foi difícil de navegar ao utilizar o VoiceOver. Foram introduzidas melhorias. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Acessibilidade: A cor de fundo do botão de colapso/expansão para o painel de Ações e Propriedades foi inconsistente com controlos ui semelhantes no tema High Contrast Black. A cor foi mudada. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Acessibilidade: No tema High Contrast Black, o estilo de foco para o botão 'X' no diálogo Properties não era visível. Este problema foi corrigido. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Acessibilidade: Os separadores De Ações e Propriedades estavam a perder vários valores de ária, o que resultou numa experiência de leitor de ecrã subpar. Os valores da ária em falta foram agora adicionados. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Acessibilidade: Os nódosos de árvores colapsados no lado esquerdo não receberam um valor de falso. Este problema foi corrigido. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Problemas Conhecidos
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolha, pode causar um erro que impede que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte [este problema](https://github.com/Microsoft/AzureStorageExplorer/issues/537) para mais informações.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, por favor comentou [este assunto.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a utilizar o trabalho do filtro de cancelamento [aqui](https://github.com/Azure/azure-storage-node/issues/317)descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* O Azure Stack não suporta as seguintes funcionalidades e tentar usá-las enquanto trabalha com o Azure Stack pode resultar em erros inesperados:
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Eliminação de Forma Recuperável
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Versão 1.2.0
06/12/2018

### <a name="new"></a>Novo
* Se o Storage Explorer não carregar subscrições a partir de apenas um subconjunto dos seus inquilinos, então quaisquer subscrições carregadas com sucesso serão mostradas juntamente com uma mensagem de erro especificamente para os inquilinos que falharam. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* No Windows, quando uma atualização estiver disponível, pode agora optar por "Atualizar em Perto". Quando esta opção for escolhida, o instalador para a atualização será executado depois de fechar o Storage Explorer. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Restaurar o Snapshot foi adicionado ao menu de contexto do editor de partilha de ficheiros ao visualizar um instantâneo de partilha de ficheiros. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* O botão Clear Queue está agora sempre ativado. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* O suporte para a inscrição no ADFS Azure Stack foi reativado. É necessária a versão 1804 ou superior do Azure Stack. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Correções
* Se visualizasse instantâneos para uma parte de ficheiro cujo nome era um prefixo de outra parte de ficheiro na mesma conta de armazenamento, então as fotos para a outra parte do ficheiro também seriam listadas. Esta questão foi corrigida. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Quando ligado via SAS, restaurar um ficheiro a partir de um instantâneo de partilha de ficheiros resultaria num erro. Esta questão foi corrigida. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Ao visualizar instantâneos para uma bolha, a ação Promote Snapshot foi ativada quando a bolha base e um único instantâneo foram selecionados. A ação só está ativada se for selecionado um único instantâneo. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Se um único trabalho (como o download de uma bolha) fosse iniciado e mais tarde falhado, não voltaria a tentar automaticamente até que iniciasse outro trabalho do mesmo tipo. Todos os empregos devem agora ser auto-try, independentemente do número de empregos que tenha feito fila.
* Os editores abriram para os recém-criados contentores blob nas contas GPV2 e Blob Storage não tinham uma coluna Access Tier. Esta questão foi corrigida. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Por vezes, não aparece uma coluna access Tier quando uma conta de armazenamento ou um recipiente de bolha foi fixado através do SAS. A coluna será agora sempre mostrada, mas com um valor vazio se não houver um conjunto de Access Tier. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* A definição do Nível de Acesso de uma bolha de bloco recém-carregada foi desativada. Esta questão foi corrigida. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Se o botão "Keep Tab Open" fosse invocado através do teclado, então o foco do teclado perder-se-ia. Agora, o foco vai passar para a conta que foi mantida aberta. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Para uma consulta no Construtor de Consultas, o VoiceOver não estava a dar uma descrição utilizável do operador atual. Agora é mais descritivo. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* As ligações de paginação para os vários editores não eram descritivas. Foram alterados para serem mais descritivos. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* No diálogo Da Entidade Add, o VoiceOver não estava a anunciar em que coluna um elemento de entrada fazia parte. O nome da coluna atual está agora incluído na descrição do elemento. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Os botões de rádio e as caixas de verificação não tinham uma fronteira visível quando se concentravam. Esta questão foi corrigida. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao utilizar emuladores, como o Emulador de Armazenamento Azure ou o Azurite, terá de os fazer ouvir ligações nas suas portas padrão. Caso contrário, o Storage Explorer não poderá ligar-se a eles.
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, por favor comentou [este assunto.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a utilizar o trabalho do filtro de cancelamento [aqui](https://github.com/Azure/azure-storage-node/issues/317)descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Versão 1.1.0
05/09/2018

### <a name="new"></a>Novo
* O Storage Explorer suporta agora a utilização do Azurite. Nota: a ligação à Azurite está codificada para os pontos finais de desenvolvimento por defeito.
* O Storage Explorer suporta agora os Níveis de Acesso para Contas de Armazenamento Blob Only e GPV2. Saiba mais sobre os Níveis de Acesso [aqui.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)
* Já não é necessária uma hora de início para gerar um SAS.

### <a name="fixes"></a>Correções
* A recuperação de assinaturas para contas do Governo dos EUA foi quebrada. Esta questão foi corrigida. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* O prazo de validade das políticas de acesso não estava a ser corretamente poupado. Esta questão foi corrigida. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Ao gerar um URL SAS para um item num recipiente, o nome do item não estava a ser anexado ao URL. Esta questão foi corrigida. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Ao criar um SAS, os tempos de validade que estão no passado seriam, por vezes, o valor padrão. Isto deveu-se ao Storage Explorer utilizando o último tempo de início e expiração usado como valores predefinidos. Agora, sempre que abre o diálogo SAS, um novo conjunto de valores padrão é gerado. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Ao copiar entre Contas de Armazenamento, gera-se um SAS de 24 horas. Se a cópia durasse mais de 24 horas, então a cópia falharia. Aumentámos o SAS's para a última semana para reduzir a hipótese de uma cópia falhar devido a um SAS expirado. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Para algumas atividades clicando em "Cancelar" nem sempre funcionaria. Esta questão foi corrigida. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Para algumas atividades, a velocidade de transferência estava errada. Esta questão foi corrigida. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* A ortografia de "Anterior" no menu View estava errada. Está agora devidamente escrito. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* A página final do instalador do Windows tinha um botão "Next". Foi alterado para um botão "Finish". [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* O foco do separador não era visível para botões em diálogos ao utilizar o tema HC Black. Agora é visível. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* O invólucro de "Auto-Resolve" para ações no registo de atividade estava errado. Está agora correto. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Ao apagar uma entidade de uma mesa, o diálogo a pedir-lhe confirmação apresentou um ícone de erro. O diálogo usa agora um ícone de aviso. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Problemas Conhecidos
* Se utilizar vs para Mac e já tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sessão. Para contornar o problema, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, por favor comentou [este assunto.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* A Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* O upload da sua pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não integrado no Eletrão.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a utilizar o trabalho do filtro de cancelamento [aqui](https://github.com/Azure/azure-storage-node/issues/317)descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Versão 1.0.0
04/16/2018

### <a name="new"></a>Novo
* Autenticação melhorada que permite ao Storage Explorer utilizar a mesma loja de contas que o Visual Studio 2017. Para utilizar esta funcionalidade, terá de voltar a fazer login nas suas contas e redefinir as suas subscrições filtradas.
* Para contas Azure Stack apoiadas por AAD, o Storage Explorer irá agora recuperar as subscrições do Azure Stack quando o 'Target Azure Stack' estiver ativado. Já não precisas de criar um ambiente de login personalizado.
* Foram adicionados vários atalhos para permitir uma navegação mais rápida. Estes incluem a toggling vários painéis e a mudança entre editores. Consulte o menu 'Ver' para mais detalhes.
* O feedback do Explorador de Armazenamento agora vive no GitHub. Pode chegar à nossa página de problemas clicando no [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues)botão Feedback na parte inferior esquerda ou indo para . Sinta-se livre para fazer sugestões, relatar questões, fazer perguntas ou deixar qualquer outra forma de feedback.
* Se estiver a deparar com problemas de Certificado TLS/SSL e não conseguir encontrar o certificado `--ignore-certificate-errors` ofensivo, pode agora lançar o Storage Explorer a partir da linha de comando com a bandeira. Quando lançado com esta bandeira, o Storage Explorer ignorará os erros de certificado TLS/SSL.
* Existe agora uma opção de 'Download' no menu de contexto para itens blob e ficheiros.
* Melhor acessibilidade e suporte ao leitor de ecrã. Se você confiar em funcionalidades de acessibilidade, consulte a nossa [documentação de acessibilidade](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) para mais informações.
* O Explorador de Armazenamento usa agora o Eletrão 1.8.3

### <a name="breaking-changes"></a>Alterações Interruptivas
* O Storage Explorer mudou para uma nova biblioteca de autenticação. Como parte da mudança para a biblioteca, terá de voltar a fazer login nas suas contas e redefinir as suas subscrições filtradas
* O método utilizado para encriptar dados sensíveis mudou. Isto pode resultar em alguns dos seus itens de Acesso Rápido que precisam de ser readicionados e/ou alguns de vocês recursos anexados que precisam de ser recolocados.

### <a name="fixes"></a>Correções
* Alguns utilizadores por trás de proxies teriam uploads ou downloads de blob de grupo interrompidos por uma mensagem de erro "Incapaz de resolver". Esta questão foi corrigida.
* Se o registo fosse necessário durante a utilização de um link direto, clicar na solicitação 'Sign-In' apareceria um diálogo em branco. Esta questão foi corrigida.
* No Linux, se o Storage Explorer não conseguir ser lançado devido a uma falha no processo de GPU, será agora informado da queda, avisado para utilizar o interruptor '---desactivação-gpu', e o Storage Explorer reiniciará automaticamente com o interruptor ativado.
* As políticas de acesso inválidos eram difíceis de identificar no diálogo das Políticas de Acesso. As iDs da política de acesso inválida estão agora delineadas a vermelho para obter mais visibilidade.
* O registo de atividade às vezes teria grandes áreas de espaço branco entre as diferentes partes de uma atividade. Esta questão foi corrigida.
* No editor de consulta de tabela, se deixasse uma cláusula de carimbo de tempo num estado inválido e tentasse modificar outra cláusula, o editor congelaria. O editor irá agora restaurar a cláusula de carimbo de tempo para o seu último estado válido quando for detetada uma alteração noutra cláusula.
* Se fizesse uma pausa enquanto escrevia na sua consulta de pesquisa na vista da árvore, a pesquisa começaria e o foco seria roubado da caixa de texto. Agora, deve começar a pesquisar explicitamente premindo a tecla 'Enter', ou clicando no botão de pesquisa inicial.
* O comando 'Obter Assinatura de Acesso Partilhado' seria por vezes desativado quando clicava corretamente num ficheiro numa Partilha de Ficheiros. Esta questão foi corrigida.
* Se o nó da árvore de recursos com foco foi filtrado durante a busca, não poderia agiss em direção à árvore de recursos e utilizar as teclas de seta para navegar na árvore de recursos. Agora, se o nó de árvore de recursos focado estiver escondido, o primeiro nó na árvore de recursos será automaticamente focado.
* Um separador extra às vezes seria visível na barra de ferramentas do editor. Esta questão foi corrigida.
* A caixa de texto de migalhas de pão às vezes transbordava. Esta questão foi corrigida.
* Os editores blob e File Share às vezes atualizavam constantemente ao carregar muitos ficheiros ao mesmo tempo. Esta questão foi corrigida.
* A função 'Estatísticas das Pastas' não tinha qualquer propósito na visão de Gestão de Snapshots de Partilha de Ficheiros. Foi agora desativado.
* No Linux, o menu File não apareceu. Esta questão foi corrigida.
* Ao carregar uma pasta para uma Partilha de Ficheiros, por padrão, apenas o conteúdo da pasta foi carregado. Agora, o comportamento predefinido é fazer o upload do conteúdo da pasta para uma pasta correspondente na Partilha de Ficheiros.
* A encomenda de botões em vários diálogos tinha sido invertida. Esta questão foi corrigida.
* Várias correções relacionadas com segurança.

### <a name="known-issues"></a>Problemas Conhecidos
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para descentrar o foco, pode refrescar tudo.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Versão 0.9.6
02/28/2018

### <a name="fixes"></a>Correções
* Um problema impediu que as bolhas/ficheiros esperados fossem listados no editor. Esta questão foi corrigida.
* Um problema causou a troca entre as vistas instantâneas para exibir incorretamente os itens. Esta questão foi corrigida.

### <a name="known-issues"></a>Problemas Conhecidos
* O Storage Explorer não suporta contas ADFS.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a utilizar o trabalho do filtro de cancelamento [aqui](https://github.com/Azure/azure-storage-node/issues/317)descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* O painel de definições da conta pode mostrar que precisa de reintroduzir credenciais para filtrar subscrições.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Versão 0.9.5
02/06/2018

### <a name="new"></a>Novo

* Suporte para instantâneos de partilhas de ficheiros:
    * Crie e gere snapshots para as suas Partilhas de Ficheiros.
    * Alterne facilmente as vistas entre as fotos das suas Partilhas de Ficheiros enquanto explora.
    * Restaure as versões anteriores dos seus ficheiros.
* Suporte de pré-visualização para Azure Data Lake Store:
    * Ligue-se aos seus recursos ADLS através de várias contas.
    * Ligue-se e partilhe os recursos ADLS utilizando URIs ADL.
    * Executar as operações básicas de ficheiro/pasta de forma recorrente.
    * Pin as pastas individuais para acesso rápido.
    * Mostrar estatísticas de pastas.

### <a name="fixes"></a>Correções
* Melhorias no desempenho da startup.
* Várias correções de insetos.

### <a name="known-issues"></a>Problemas Conhecidos
* O Storage Explorer não suporta contas ADFS.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* O painel de definições da conta pode mostrar que precisa de reintroduzir credenciais para filtrar subscrições.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Versão 0.9.4 e 0.9.3
01/21/2018

### <a name="new"></a>Novo
* A janela existente do Storage Explorer será reutilizada quando:
    * Abertura de ligações diretas geradas no Storage Explorer.
    * Opening Storage Explorer a partir do portal.
    * Opening Storage Explorer a partir da extensão do código VS de armazenamento azure (em breve).
* Capacidade adicional de abrir uma nova janela do Storage Explorer a partir do Storage Explorer.
    * Para windows, existe uma opção 'Nova Janela' no Menu de Ficheiros e no menu de contexto da barra de tarefas.
    * Para o Mac, existe uma opção "Nova Janela" no Menu app.

### <a name="fixes"></a>Correções
* Resolveu um problema de segurança. Por favor, atualize para 0.9.4 o mais rápido possível.
* As atividades antigas não estavam a ser devidamente limpas. Isto afetou o desempenho de trabalhos de longa duração. Estão agora a ser limpos corretamente.
* Ações que envolvam um grande número de ficheiros e diretórios fariam ocasionalmente congelar o Storage Explorer. Os pedidos ao Azure para ações de ficheiros estão agora acelerados para limitar o uso de recursos do sistema.

### <a name="known-issues"></a>Problemas Conhecidos
* O Storage Explorer não suporta contas ADFS.
* As teclas de atalho para "Ver Explorer" e "View Account Management" devem ser CTRL/Cmd+Shift+E e CTRL/Cmd+Shift+A, respectivamente.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* O painel de definições da conta pode mostrar que precisa de reintroduzir credenciais para filtrar subscrições.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Versão 0.9.2
11/01/2017

### <a name="hotfixes"></a>Correções
* Foram possíveis alterações inesperadas de dados na edição dos valores edm.DateTime para entidades de tabela, dependendo do fuso horário local. O editor usa agora uma caixa de texto simples, dando um controlo preciso e consistente sobre os valores edm.DateTime.
* O upload/download de um grupo de bolhas quando ligados com nome e chave não começariam. Esta questão foi corrigida.
* Anteriormente, o Storage Explorer apenas o levaria a reautenticar uma conta velha se uma ou mais subscrições da conta fossem selecionadas. Agora o Storage Explorer irá pedir-lhe mesmo que a conta esteja totalmente filtrada.
* O domínio dos pontos finais do Governo dos ESTADOS Unido estava errado. Foi arranjado.
* O botão de aplicação no painel 'Gerir contas' foi, por vezes, difícil de clicar. Isto não devia mais acontecer.

### <a name="new"></a>Novo
* Suporte de pré-visualização para Azure Cosmos DB:
    * [Documentação Online](./cosmos-db/storage-explorer.md)
    * Criar bases de dados e coleções
    * Manipular dados
    * Consulta, criar ou apagar documentos
    * Atualizar procedimentos armazenados, funções definidas pelo utilizador ou gatilhos
    * Utilize cordas de ligação para ligar e gerir as suas bases de dados
* Melhorou o desempenho do upload/download de muitas pequenas bolhas.
* Adicione uma ação "Retry All" se houver falhas num grupo de upload de blob ou num grupo de descarregamento de blob.
* O Storage Explorer irá agora parar a iteração durante o upload/download da blob se detetar que a sua ligação de rede foi perdida. Em seguida, pode retomar a iteração uma vez que a ligação de rede tenha sido restabelecida.
* Acrescentou a capacidade de "Fechar tudo", "Fechar Outros" e "Fechar" os separadores através do menu de contexto.
* O Storage Explorer agora usa diálogos nativos e menus de contexto nativo.
* O Storage Explorer está agora mais acessível. As melhorias incluem:
    * Suporte melhorado para leitor de ecrã, para NVDA no Windows, e para VoiceOver no Mac
    * Melhor estão a ter uma temática de alto contraste
    * Fixações de tabbing de teclado e teclado

### <a name="fixes"></a>Correções
* Se tentasse abrir ou descarregar uma bolha com um nome de ficheiro windows inválido, a operação falharia. O Storage Explorer irá agora detetar se um nome blob é inválido e perguntar se você gostaria de codificar ou saltar a bolha. O Storage Explorer também detetará se um nome de ficheiro parece estar codificado e perguntar-lhe se pretende descodificá-lo antes de o carregar.
* Durante o upload da bolha, o editor do recipiente de bolhas alvo às vezes não refrescava corretamente. Esta questão foi corrigida.
* O suporte para várias formas de cordas de ligação e URIs SAS regrediu. Abordámos todas as questões conhecidas, mas por favor envie feedback se encontrar mais questões.
* A notificação de atualização foi quebrada para alguns utilizadores em 0.9.0. Este problema foi corrigido e, para os afetados pelo bug, pode descarregar manualmente a versão mais recente do Storage Explorer [aqui](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Problemas Conhecidos
* O Storage Explorer não suporta contas ADFS.
* As teclas de atalho para "Ver Explorer" e "View Account Management" devem ser CTRL/Cmd+Shift+E e CTRL/Cmd+Shift+A, respectivamente.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* O painel de definições da conta pode mostrar que precisa de reintroduzir credenciais para filtrar subscrições.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Versão 0.9.1 e 0.9.0
10/20/2017
### <a name="new"></a>Novo
* Suporte de pré-visualização para Azure Cosmos DB:
    * [Documentação Online](./cosmos-db/storage-explorer.md)
    * Criar bases de dados e coleções
    * Manipular dados
    * Consulta, criar ou apagar documentos
    * Atualizar procedimentos armazenados, funções definidas pelo utilizador ou gatilhos
    * Utilize cordas de ligação para ligar e gerir as suas bases de dados
* Melhorou o desempenho do upload/download de muitas pequenas bolhas.
* Adicione uma ação "Retry All" se houver falhas num grupo de upload de blob ou num grupo de descarregamento de blob.
* O Storage Explorer irá agora parar a iteração durante o upload/download da blob se detetar que a sua ligação de rede foi perdida. Em seguida, pode retomar a iteração uma vez que a ligação de rede tenha sido restabelecida.
* Acrescentou a capacidade de "Fechar tudo", "Fechar Outros" e "Fechar" os separadores através do menu de contexto.
* O Storage Explorer agora usa diálogos nativos e menus de contexto nativo.
* O Storage Explorer está agora mais acessível. As melhorias incluem:
    * Suporte melhorado para leitor de ecrã, para NVDA no Windows, e para VoiceOver no Mac
    * Melhor estão a ter uma temática de alto contraste
    * Fixações de tabbing de teclado e teclado

### <a name="fixes"></a>Correções
* Se tentasse abrir ou descarregar uma bolha com um nome de ficheiro windows inválido, a operação falharia. O Storage Explorer irá agora detetar se um nome blob é inválido e perguntar se você gostaria de codificar ou saltar a bolha. O Storage Explorer também detetará se um nome de ficheiro parece estar codificado e perguntar-lhe se pretende descodificá-lo antes de o carregar.
* Durante o upload da bolha, o editor do recipiente de bolhas alvo às vezes não refrescava corretamente. Esta questão foi corrigida.
* O suporte para várias formas de cordas de ligação e URIs SAS regrediu. Abordámos todas as questões conhecidas, mas por favor envie feedback se encontrar mais questões.
* A notificação de atualização foi quebrada para alguns utilizadores em 0.9.0. Este problema foi corrigido, e para os afetados pelo bug, você pode baixar manualmente a versão mais recente do Storage Explorer [aqui](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Problemas Conhecidos
* O Storage Explorer não suporta contas ADFS.
* As teclas de atalho para "Ver Explorer" e "View Account Management" devem ser CTRL/Cmd+Shift+E e CTRL/Cmd+Shift+A, respectivamente.
* Ao direcionar o Azure Stack, carregar certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho do filtro de cancelamento aqui descrito.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* O painel de definições da conta pode mostrar que precisa de reintroduzir credenciais para filtrar subscrições.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* A concha electron usada pelo Storage Explorer tem problemas com alguma aceleração de hardware de GPU (unidade de processamento gráfico). Se o Storage Explorer estiver a exibir uma janela principal em branco (vazia), pode tentar lançar o `--disable-gpu` Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o interruptor:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Versão 0.8.16
8/21/2017

### <a name="new"></a>Novo
* Quando abrir uma bolha, o Storage Explorer irá pedir-lhe para carregar o ficheiro descarregado se for detetada uma alteração
* Experiência de entrada de entrada de Azure Stack melhorada
* Melhorou o desempenho do upload/download de muitos pequenos ficheiros ao mesmo tempo


### <a name="fixes"></a>Correções
* Para alguns tipos de blob, optar por "substituir" durante um conflito de upload resultaria, por vezes, no reinício do upload.
* Na versão 0.8.15, os uploads às vezes paravam nos 99%.
* Ao enviar ficheiros para uma partilha de ficheiros, se optar por fazer o upload para um diretório que ainda não existia, o seu upload falharia.
* O Storage Explorer estava a gerar incorretamente selos de tempo para assinaturas de acesso partilhado e consultas de mesa.


### <a name="known-issues"></a>Problemas Conhecidos
* A utilização de um nome e de uma cadeia de ligação chave não funciona atualmente. Será fixado no próximo lançamento. Até lá, pode utilizar o anexo com o nome e a chave.
* Se tentar abrir um ficheiro com um nome de ficheiro Windows inválido, o download resultará num erro não encontrado.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Esta é uma limitação da biblioteca do Nó de Armazenamento Azure.
* Depois de completar um upload de blob, o separador que iniciou o upload é atualizado. Esta é uma mudança em comparação com o comportamento anterior, e também fará com que seja levado de volta para a raiz do recipiente em que se encontra.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça essa decisão.
* O painel de definições da conta pode mostrar que precisa de reintroduzir credenciais para filtrar subscrições.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o CcG está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores em Ubuntu 17.04, terá de instalar o GConf - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Versão 0.8.14
06/22/2017

### <a name="new"></a>Novo

* Versão atualizada da Electron para 1.7.2 para tirar partido de várias atualizações críticas de segurança
* Agora pode aceder rapidamente ao guia de resolução de problemas online a partir do menu de ajuda
* [Guia][2] de resolução de problemas do explorador de armazenamento
* [Instruções][3] sobre a ligação a uma subscrição do Azure Stack

### <a name="known-issues"></a>Problemas Conhecidos

* Os botões no diálogo de confirmação da pasta de exclusão não se registam com os cliques do rato no Linux. trabalhar em torno é usar a chave Enter
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça a decisão
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo pode causar erros
* O painel de definições de conta pode mostrar que precisa de reintroduzir credenciais para filtrar subscrições
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* Ubuntu 14.04 instalações precisa de versão gcc atualizada ou atualizada – as etapas para atualizar estão abaixo:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Versão 0.8.13
05/12/2017

#### <a name="new"></a>Novo

* [Guia][2] de resolução de problemas do explorador de armazenamento
* [Instruções][3] sobre a ligação a uma subscrição do Azure Stack

#### <a name="fixes"></a>Correções

* Fixo: O upload de ficheiros tinha uma alta probabilidade de causar um erro de memória
* Fixo: Já pode iniciar sessão com PIN/Smartcard
* Fixo: Aberto no Portal agora trabalha com Azure China 21Vianet, Azure Alemanha, Governo Azure DOS EUA e Azure Stack
* Fixo: Ao carregar uma pasta para um recipiente de bolhas, por vezes ocorreria um erro de "operação ilegal".
* Fixo: Selecione tudo foi desativado enquanto geria instantâneos
* Fixo: Os metadados da bolha base podem ser substituídos após visualizar as propriedades dos seus instantâneos

#### <a name="known-issues"></a>Problemas Conhecidos

* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Storage Explorer esqueça a decisão
* Enquanto zoom dentro ou fora, o nível de zoom pode momentaneamente repor para o nível padrão
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo pode causar erros
* O painel de definições de conta pode mostrar que precisa de reintroduzir credenciais para filtrar subscrições
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* Ubuntu 14.04 instalações precisa de versão gcc atualizada ou atualizada – as etapas para atualizar estão abaixo:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Versão 0.8.12 e 0.8.11 e 0.8.10
04/07/2017

#### <a name="new"></a>Novo

* O Explorer de Armazenamento irá agora fechar automaticamente quando instalar uma atualização a partir da notificação da atualização
* O acesso rápido no local proporciona uma experiência melhorada para trabalhar com os seus recursos frequentemente acedidos
* No editor blob container, você pode agora ver a qual máquina virtual uma bolha alugada pertence a
* Agora pode colapsar o painel do lado esquerdo
* Discovery agora corre ao mesmo tempo que download
* Utilize estatísticas no recipiente blob, file share e editores de tabela para ver o tamanho do seu recurso ou seleção
* Pode agora inscrever-se nas contas Azure Ative Directory (AAD) baseadas em Azure.
* Agora pode enviar ficheiros de arquivo sobre contas de armazenamento Premium de 32MB para Premium
* Melhor apoio à acessibilidade
* Pode agora adicionar certificados De Base-64 codificados de confiança X.509 TLS/SSL indo para editar -&gt; Certificados SSL -&gt; Certificados de Importação

#### <a name="fixes"></a>Correções

* Fixo: depois de refrescar as credenciais de uma conta, a vista da árvore às vezes não iria automaticamente refrescar
* Fixo: gerar um SAS para filas e tabelas de emuladores resultaria num URL inválido
* Fixa: as contas de armazenamento premium podem agora ser expandidas enquanto um proxy está ativado
* Fixo: o botão de aplicação na página de gestão de contas não funcionaria se tivesse 1 ou 0 contas selecionadas
* Fixo: o upload de bolhas que requerem resoluções de conflitopode falhar - fixado em 0.8.11
* Fixo: o envio de feedback foi quebrado em 0.8.11 - fixado em 0.8.12

#### <a name="known-issues"></a>Problemas Conhecidos

* Depois de atualizar para 0.8.10, terá de renovar todas as suas credenciais.
* Enquanto é ampliado para dentro ou para fora, o nível de zoom pode repor momentaneamente para o nível padrão.
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo pode causar erros.
* O painel de definições da conta pode mostrar que precisa de reintroduzir credenciais para filtrar as subscrições.
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome.
* Embora o Azure Stack não suporte atualmente as Ações de Ficheiros, um nó de Partilhas de Ficheiros ainda aparece sob uma conta de armazenamento Azure Stack anexada.
* Ubuntu 14.04 instalações precisa de versão gcc atualizada ou atualizada – as etapas para atualizar estão abaixo:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Versão 0.8.9 e 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Novo

* O Storage Explorer 0.8.9 irá descarregar automaticamente a versão mais recente para atualizações.
* Hotfix: usar um portal gerado SAS URI para anexar uma conta de armazenamento resultaria num erro.
* Agora pode criar, gerir e promover imagens blob.
* Pode agora inscrever-se nas contas azure China 21Vianet, Azure Germany e Azure Us Government.
* Agora pode alterar o nível de zoom. Utilize as opções no menu 'Ver' para ampliar, zoom para fora e redefinir zoom.
* Os caracteres Unicode são agora suportados em metadados de utilizadores para bolhas e ficheiros.
* Melhorias na acessibilidade.
* As notas de lançamento da próxima versão podem ser visualizadas a partir da notificação da atualização. Também pode ver as notas de lançamento atuais do menu Ajuda.

#### <a name="fixes"></a>Correções

* Fixo: o número da versão está agora corretamente apresentado no Painel de Controlo no Windows
* Fixo: a procura já não se limita a 50.000 nós
* Fixo: upload para uma partilha de ficheiros girado para sempre se o diretório de destino já não existisse
* Fixo: estabilidade melhorada para uploads e downloads longos

#### <a name="known-issues"></a>Problemas Conhecidos

* Enquanto é ampliado para dentro ou para fora, o nível de zoom pode repor momentaneamente para o nível padrão.
* O Quick Access só funciona com itens baseados em subscrição. Os recursos locais ou recursos anexados através da chave ou do token SAS não são suportados nesta versão.
* Pode levar alguns segundos para navegar para o recurso alvo, dependendo de quantos recursos você tem.
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo pode causar erros.

12/16/2016
### <a name="version-087"></a>Versão 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Novo

* Pode escolher como resolver conflitos no início de uma sessão de atualização, download ou cópia na janela Atividades
* Pairar sobre um separador para ver o caminho completo do recurso de armazenamento
* Quando clica num separador, sincroniza com a sua localização no painel de navegação do lado esquerdo

#### <a name="fixes"></a>Correções

* Fixo: O Storage Explorer é agora uma aplicação de confiança no Mac
* Fixo: Ubuntu 14.04 é novamente suportado
* Fixo: Às vezes, a conta de adição UI pisca ao carregar subscrições
* Fixo: Às vezes nem todos os recursos de armazenamento foram listados no painel de navegação do lado esquerdo
* Fixo: O painel de ação às vezes apresentava ações vazias
* Fixo: O tamanho da janela da última sessão fechada está agora retido
* Fixo: Pode abrir vários separadores para o mesmo recurso utilizando o menu de contexto

#### <a name="known-issues"></a>Problemas Conhecidos

* O Quick Access só funciona com itens baseados em subscrição. Os recursos locais ou recursos anexados através da chave ou token SAS não são suportados nesta versão
* Pode levar alguns segundos para navegar para o recurso alvo, dependendo de quantos recursos você tem
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo pode causar erros
* As pegas de pesquisa procuram cerca de 50.000 nós - depois disso, o desempenho pode ser impactado ou pode causar exceção não tratada
* Pela primeira vez utilizando o Storage Explorer no macOS, poderá ver várias solicitações pedindo a permissão do utilizador para aceder ao porta-chaves. Sugerimos que selecione Always Allow para que o pedido não apareça novamente

11/18/2016
### <a name="version-086"></a>Versão 0.8.6

#### <a name="new"></a>Novo

* Agora pode fixar serviços mais utilizados no Quick Access para uma navegação fácil
* Agora pode abrir vários editores em diferentes separadores. Clique único para abrir um separador temporário; clique em dobro para abrir um separador permanente. Também pode clicar no separador temporário para torná-lo um separador permanente
* Fizemos melhorias de desempenho e estabilidade visíveis para uploads e downloads, especialmente para grandes ficheiros em máquinas rápidas
* Pastas "virtuais" vazias podem agora ser criadas em recipientes de blob
* Reintroduzimos a pesquisa de âmbito com a nossa nova pesquisa de subcordas melhorada, por isso tem agora duas opções para pesquisar:
    * Pesquisa global - basta inserir um termo de pesquisa na caixa de texto de pesquisa
    * Pesquisa de telescópios - clique no ícone de lupa ao lado de um nó, em seguida, adicione um termo de pesquisa para o final do caminho, ou clique à direita e selecione "Search from Here"
* Adicionámos vários temas: Luz (padrão), Preto, Alto Contraste Preto e Branco de Alto Contraste. Ir a&gt; Editar - Temas para alterar a sua preferência temática
* Pode modificar propriedades blob e ficheiros
* Agora apoiamos mensagens codificadas (base64) e não codificadas
* Em Linux, um Sistema operativo de 64 bits é agora necessário. Para esta versão, apenas apoiamos Ubuntu 16.04.1 LTS
* Atualizámos o nosso logótipo!

#### <a name="fixes"></a>Correções

* Fixo: Problemas de congelação de ecrã
* Fixo: Segurança reforçada
* Fixo: Às vezes podem aparecer contas anexas duplicadas
* Fixo: Uma bolha com um tipo de conteúdo indefinido poderia gerar uma exceção
* Fixo: A abertura do Painel de Consulta sobre uma mesa vazia não foi possível
* Fixo: Varia bugs em Pesquisa
* Fixo: Aumentou o número de recursos carregados de 50 para 100 ao clicar em "Carregar Mais"
* Fixo: Na primeira execução, se uma conta for assinada, agora selecionamos todas as subscrições para essa conta por padrão

#### <a name="known-issues"></a>Problemas Conhecidos

* Este lançamento do Explorador de Armazenamento não funciona em Ubuntu 14.04
* Para abrir vários separadores para o mesmo recurso, não clique continuamente no mesmo recurso. Clique noutro recurso e depois volte e clique no recurso original para abri-lo novamente em outro separador
* O Quick Access só funciona com itens baseados em subscrição. Os recursos locais ou recursos anexados através da chave ou token SAS não são suportados nesta versão
* Pode levar alguns segundos para navegar para o recurso alvo, dependendo de quantos recursos você tem
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo pode causar erros
* As pegas de pesquisa procuram cerca de 50.000 nós - depois disso, o desempenho pode ser impactado ou pode causar exceção não tratada

10/03/2016
### <a name="version-085"></a>Versão 0.8.5

#### <a name="new"></a>Novo

* Pode agora usar chaves SAS geradas pelo Portal para anexar a Contas e recursos de Armazenamento

#### <a name="fixes"></a>Correções

* Fixo: condição de raça durante a busca por vezes fez com que os nós se tornassem não expansíveis
* Fixo: "Use HTTP" não funciona quando se conecta às Contas de Armazenamento com nome de conta e chave
* Fixa: As teclas SAS (especialmente as geradas pelo Portal) devolvem um erro de "trailing slash"
* Fixo: questões de importação de quadros
    * Às vezes, a chave da divisória e a chave da linha foram invertidas
    * Incapaz de ler chaves de partição "nulas"

#### <a name="known-issues"></a>Problemas Conhecidos

* As pegas de pesquisa procuram cerca de 50.000 nós - depois disso, o desempenho pode ser impactado
* O Azure Stack não suporta ficheiros, por isso tentar expandir ficheiros mostrará um erro

09/12/2016
### <a name="version-084"></a>Versão 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Novo

* Gere links diretos para contas de armazenamento, contentores, filas, tabelas ou partilhas de ficheiros para partilha e fácil acesso aos seus recursos - Suporte windows e Mac OS
* Procure os seus recipientes de blob, mesas, filas, partilhas de ficheiros ou contas de armazenamento a partir da caixa de pesquisa
* Agora pode agrupar cláusulas no construtor de consultas de mesa
* Renomear e copiar/colar recipientes blob, partilhas de ficheiros, tabelas, bolhas, pastas blob, ficheiros e diretórios dentro de contas e contentores anexados à SAS
* Renomeação e cópia de contentores blob e partilhas de ficheiros agora preservam propriedades e metadados

#### <a name="fixes"></a>Correções

* Fixo: não pode editar entidades de mesa se contiverem propriedades booleanas ou binárias

#### <a name="known-issues"></a>Problemas Conhecidos

* As pegas de pesquisa procuram cerca de 50.000 nós - depois disso, o desempenho pode ser impactado

08/03/2016
### <a name="version-083"></a>Versão 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Novo

* Renomear contentores, tabelas, partilhas de ficheiros
* Experiência melhorada do construtor de consultas
* Capacidade de guardar e carregar consultas
* Links diretos para contas de armazenamento ou contentores, filas, tabelas ou partilha de ficheiros para partilha e acesso fácil aos seus recursos (apenas para windows - suporte macOS em breve!)
* Capacidade de gerir e configurar regras cors

#### <a name="fixes"></a>Correções

* Fixo: As Contas Microsoft requerem reautenticação a cada 8-12 horas

#### <a name="known-issues"></a>Problemas Conhecidos

* Às vezes, a UI pode parecer congelada - maximizar a janela ajuda a resolver esta questão
* a instalação do macOS pode exigir permissões elevadas
* O painel de definições de conta pode mostrar que precisa de reintroduzir credenciais para filtrar subscrições
* Renomear ações de ficheiros, contentores e tabelas não preserva metadados ou outras propriedades no contentor, tais como quotas de partilha de ficheiros, nível de acesso público ou políticas de acesso
* Renomear bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um nome de renome
* Copiar ou renomear recursos não funciona dentro de contas anexadas à SAS

07/07/2016
### <a name="version-082"></a>Versão 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Novo

* As Contas de Armazenamento são agruparadas por assinaturas; armazenamento de desenvolvimento e recursos anexados através de chave ou SAS são mostrados sob o nó (Local e Anexo)
* Inscreva-se nas contas no painel "Definições de Conta Azure"
* Configure as definições de proxy para ativar e gerir o início de sessão
* Criar e quebrar arrendamentos de blob
* Abrir recipientes de bolhas, filas, mesas e ficheiros com um clique único

#### <a name="fixes"></a>Correções

* Fixo: as mensagens de fila inseridas nas bibliotecas .NET ou Java não estão devidamente descodificadas a partir da base64
* Fixo: tabelas $metrics não são apresentadas para contas de armazenamento blob
* Fixo: o nó das mesas não funciona para armazenamento local (Desenvolvimento)

#### <a name="known-issues"></a>Problemas Conhecidos

* a instalação do macOS pode exigir permissões elevadas

06/15/2016
### <a name="version-080"></a>Versão 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Novo

* Suporte de partilha de ficheiros: visualização, upload, download, cópia de ficheiros e diretórios, UrIs SAS (criar e ligar)
* Melhor experiência do utilizador para a ligação ao Armazenamento com URIs SAS ou chaves de conta
* Resultados da consulta da tabela de exportação
* Reordenação e personalização da coluna de mesa
* Visualização $logs recipientes de bolhas e tabelas de $metrics para Contas de Armazenamento com métricas ativadas
* Melhor comportamento de exportação e importação, agora inclui tipo de valor imobiliário

#### <a name="fixes"></a>Correções

* Fixo: carregar ou descarregar grandes bolhas pode resultar em uploads/downloads incompletos
* Fixo: editar, adicionar ou importar uma entidade com um valor de cadeia numérico ("1") converte-a em dobro
* Fixo: Incapaz de expandir o nó de mesa no ambiente de desenvolvimento local

#### <a name="known-issues"></a>Problemas Conhecidos

* $metrics mesas não são visíveis para contas de armazenamento blob
* As mensagens de fila adicionadas programáticamente não podem ser exibidas corretamente se as mensagens forem codificadas utilizando a codificação base64

05/17/2016
### <a name="version-07201605090"></a>Versão 0.7.20160509.0

#### <a name="new"></a>Novo

* Melhor manipulação de erros para falhas de apps

#### <a name="fixes"></a>Correções

* Bug fixo onde as mensagens infoBar às vezes não aparecem quando as credenciais de inscrição eram necessárias

#### <a name="known-issues"></a>Problemas Conhecidos

* Tabelas: Adicionar, editar ou importar uma entidade que tenha um imóvel com um valor ambíguo numérico, como `Edm.String`"1" ou "1.0", e o utilizador tenta enviá-lo como um , o valor voltará através da API cliente como um Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Versão 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Novo

* Apoio à mesa: visualização, consulta, exportação, importação e operações de CRUD para entidades
* Suporte à fila: visualização, adição, desfilar mensagens
* Geração de URIs SAS para contas de armazenamento
* Ligação a Contas de Armazenamento com URIs SAS
* Atualizar notificações para futuras atualizações para o Explorer de Armazenamento
* Olhar e sensação atualizados

#### <a name="fixes"></a>Correções

* Melhorias no desempenho e na fiabilidade

### <a name="known-issues-amp-mitigations"></a>Mitigações &amp; de Problemas Conhecidos

* O download de grandes ficheiros blob não funciona corretamente - recomendamos usar o AzCopy enquanto abordamos este problema
* As credenciais de conta não serão recuperadas nem em cache se a pasta inicial não puder ser encontrada ou não puder ser escrita para
* Se estivermos adicionando, editando ou importando uma entidade que tenha um imóvel com um valor ambíguo numérico, como "1" ou "1.0", e o utilizador tenta enviá-lo como um `Edm.String`, o valor voltará através da API cliente como um Edm.Double
* Ao importar ficheiros CSV com registos multiline, os dados podem ser cortados ou mexidos

02/03/2016

### <a name="version-07201601291"></a>Versão 0.7.20160129.1

#### <a name="fixes"></a>Correções

* Melhor desempenho geral ao carregar, descarregar e copiar bolhas

01/14/2016

### <a name="version-07201601050"></a>Versão 0.7.20160105.0

#### <a name="new"></a>Novo

* Suporte linux (características de paridade para OSX)
* Adicione recipientes blob com chave Assinaturas de Acesso Partilhado (SAS)
* Adicionar Contas de Armazenamento para Azure China 21Vianet
* Adicionar Contas de Armazenamento com pontos finais personalizados
* Abra e veja o texto do conteúdo e as bolhas de imagem
* Ver e editar propriedades blob e metadados

#### <a name="fixes"></a>Correções

* Fixo: carregar ou descarregar um grande número de bolhas (500+) pode, por vezes, fazer com que a app tenha um ecrã branco
* Fixo: ao fixar o nível de acesso público do contentor blob, o novo valor não é atualizado até que reajuste o foco no recipiente. Além disso, o diálogo sempre não se incorre em "Sem acesso público", e não no valor atual atual.
* Melhor teclado/acessibilidade geral e suporte ui
* O texto da história das migalhas de pão envolve quando é longo com espaço branco
* Diálogo SAS suporta validação de entrada
* O armazenamento local continua disponível mesmo que as credenciais de utilizador tenham expirado
* Quando um recipiente de bolhas aberto é eliminado, o explorador de bolhas do lado direito é fechado

#### <a name="known-issues"></a>Problemas Conhecidos

* A instalação do Linux necessita da versão gcc atualizada ou atualizada – as etapas para atualizar estão abaixo:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Versão 0.7.20151116.0

#### <a name="new"></a>Novo

* macOS e versões Windows
* Iniciar sessão para visualizar as suas Contas de Armazenamento – utilize a sua Conta Org, Conta Microsoft, 2FA, etc.
* Armazenamento de desenvolvimento local (utilizar emulador de armazenamento, apenas windows)
* Gestor de Recursos Azure e suporte a recursos clássicos
* Criar e eliminar bolhas, filas ou mesas
* Pesquisar por bolhas específicas, filas ou mesas
* Explore o conteúdo dos recipientes blob
* Ver e navegar através de diretórios
* Carregar, descarregar e eliminar bolhas e pastas
* Ver e editar propriedades blob e metadados
* Gerar chaves SAS
* Gerir e criar políticas de acesso armazenadas (SAP)
* Pesquisa de bolhas por prefixo
* Arraste 'n drop files para carregar ou descarregar

#### <a name="known-issues"></a>Problemas Conhecidos

* Ao definir o nível de acesso público do contentor blob, o novo valor não é atualizado até que reajuste o foco no recipiente
* Ao abrir o diálogo para definir o nível de acesso público, mostra sempre "Sem acesso público" como padrão, e não o valor atual atual
* Não pode mudar o nome de bolhas descarregadas
* As entradas de registo de atividade às vezes ficam "presas" num estado em curso quando ocorre um erro, e o erro não é apresentado
* Às vezes bate ou fica completamente branco ao tentar carregar ou descarregar um grande número de bolhas
* Às vezes cancelar uma operação de cópia não funciona
* Durante a criação de um recipiente (blob/fila/mesa), se inserir um nome inválido e proceder a criar outro sob um tipo de recipiente diferente, não pode definir o foco no novo tipo
* Não pode criar nova pasta ou pasta de renome




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
