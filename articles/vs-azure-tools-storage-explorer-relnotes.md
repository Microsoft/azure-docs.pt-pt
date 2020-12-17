---
title: Notas de lançamento do Microsoft Azure Storage Explorer
description: Leia as notas de lançamento para a versão mais recente do Microsoft Azure Storage Explorer. As notas de lançamento para versões anteriores também são mostradas.
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
ms.openlocfilehash: 56654f09d3a92eec5b86d8c8e4cd76fec0639394
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653049"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Notas de lançamento do Microsoft Azure Storage Explorer

Este artigo contém as últimas notas de lançamento do Azure Storage Explorer, bem como notas de lançamento para versões anteriores. 

[O Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados de armazenamento do Azure no Windows, macOS e Linux.

Para baixar versões anteriores do Storage Explorer, pode visitar a [página de Lançamentos](https://github.com/microsoft/AzureStorageExplorer/releases) do nosso repo GitHub.

## <a name="version-1110"></a>Versão 1.11.0
11/4/2019

### <a name="new"></a>Novo
* As operações para Blobs, ADLS Gen2 e Discos Geridos utilizam o AzCopy integrado. Mais especificamente, as seguintes operações são feitas com recurso a AzCopy:
   * Blobs
      * Aberto para edição + Upload
      * Upload, incluindo drag & drop
      * Download
      * Copiar & pasta #1249
      * Eliminar
   * ADLS Gen2 Blobs
      * Upload, incluindo drag & drop
      * Download
      * Copiar & pasta
      * Excluir, incluindo a eliminação de pastas
   * Managed Disks
      * Carregar
      * Download
      * Copiar & pasta

   Adicionalmente, várias funcionalidades frequentemente solicitadas foram adicionadas à experiência integrada da AzCopy:
   * Resoluções de conflitos - serão solicitados durante as transferências para resolver conflitos. #1455
   * Faça upload como blobs de página - pode escolher se a AzCopy carrega ou não ficheiros .vhd e .vhdx como bolhas de página. #1164 e #1601
   * Parâmetros configurados da AzCopy - Foram adicionadas várias definições para sintonizar o desempenho e a utilização de recursos da AzCopy. Veja mais detalhes abaixo.

* Para permitir o acesso multi-protocolo da ADLS Gen2 e Blobs e melhorar ainda mais as experiências da ADLS Gen2, adicionámos as seguintes funcionalidades para as contas da ADLS Gen2 Gen2:
   * Pesquisa usando nomes amigáveis para definir permissões ACL
   * Veja os recipientes escondidos, como $logs e $web
   * Adquirir e quebrar o arrendamento de contentores
   * Adquirir e quebrar #848 de arrendamento Blob
   * Gerir as políticas de acesso a contentores
   * Configure os níveis de acesso blob
   * Copiar & pasta Blobs

* Nesta versão, estamos a visualizar 17 línguas adicionais. Pode mudar para um idioma à sua escolha na página de definições em "Aplicação" → "Configurações Regionais" → "Language (Preview)". Continuamos a trabalhar arduamente na tradução de cordas adicionais e na melhoria da qualidade da tradução. Se tiver algum feedback sobre uma tradução, ou se notar uma cadeia que ainda não está traduzida, por favor [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* Em cada lançamento, tentamos embarcar algumas definições para permitir uma boa viragem do Storage Explorer. Nesta versão, adicionámos definições para configurar ainda mais a AzCopy, bem como para ocultar nós de serviço:
   * Limite de largura de banda AzCopy - ajuda a controlar a quantidade de utilização da rede AzCopy. Pode encontrar esta definição em "Transfers" → "AzCopy" → "Taxa máxima de transferência". #1099
   * AzCopy MD5 check - permite-lhe configurar se e como estritamente a AzCopy verifica se mD5 hashes no download. Pode encontrar esta definição em "Transfers" → "AzCopy" → "Check MD5".
   * AzCopy concurrency e tamanho do tampão de memória - por padrão, a AzCopy analisará a sua máquina para determinar valores padrão razoáveis para estas definições. Mas se tiver problemas de desempenho, estas definições avançadas podem ser usadas para adaptar ainda mais a forma como o AzCopy funciona no seu computador. Pode encontrar estas definições em "Transfers" → "AzCopy". #994
   * Apresentar e ocultar nós de serviço - estas definições dão-lhe as opções para exibir ou ocultar qualquer um dos serviços Azure que o Storage Explorer suporta. Pode encontrar estas definições na secção "Serviços". #1877

* Ao criar um Instantâneo de um Disco Gerido, é agora fornecido um nome padrão. #1847
* Ao ligar-se ao Azure AD, se prender um recipiente de blob ADLS Gen2, então será mostrado "(ADLS Gen2)" ao lado do nó. #1861

### <a name="fixes"></a>Correções
* Ao copiar, carregar ou descarregar discos grandes, o Storage Explorer por vezes não conseguiria revogar o acesso aos discos envolvidos na operação. Este problema foi corrigido. #2048
* As estatísticas da tabela falharam ao visualizar uma consulta de chave de partição. Este problema foi corrigido. #1886

### <a name="known-issues"></a>Problemas Conhecidos
* O Explorador de Armazenamento 1.11.0 necessita agora de um ponto final DFS (como "myaccount.dfs.core.windows.net") para se ligar aos recipientes ADLS Gen2. Versões anteriores do Storage Explorer permitiram-lhe utilizar um ponto final blob. Estes anexos podem deixar de funcionar após a atualização para 1.11.0. Se encontrar este problema, recoloque-o utilizando o ponto final DFS.
* As definições numéricas não são verificadas para saber se se encontram numa gama válida.#2140
* Copiar recipientes blob de uma conta de armazenamento para outra na vista da árvore pode falhar. Estamos a investigar o assunto.#2124
* A definição de Atualização Automática ainda não afeta todas as operações no Blob Explorer.
* As funcionalidades do Disco Gerido não são suportadas no Azure Stack.
* Se um upload ou pasta de disco falhar e um novo Disco foi criado antes da falha, o Storage Explorer não apagará o Disco para si.
* Dependendo de quando cancela um upload ou pasta de disco, é possível deixar o novo Disco num estado corrompido. Se isto acontecer, ou é necessário eliminar o novo Disco ou ligar manualmente para as APIs do disco para substituir o conteúdo do Disco de modo a que não seja mais corrompido.
* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o [guia de resolução de problemas](./storage/common/storage-explorer-troubleshooting.md) para obter mais informações.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolhas, pode causar um erro que impeça que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão. Para contornar este problema ao fazer o upload ou o download de um recipiente de bolhas, pode utilizar a funcionalidade AzCopy experimental.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* A Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação suave
   * ADLS Gen2
   * Managed Disks
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* O Explorador de Armazenamento em Execução no Linux requer que certas dependências sejam instaladas primeiro. Consulte o [guia de resolução de problemas](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) do Storage Explorer para obter mais informações.

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
* Alguns utilizadores encontraram um erro no 1.10.0 enquanto tentavam visualizar os seus dados nas suas contas ADLS Gen 1. Este erro impediu que o painel do explorador se renderizasse corretamente. Este problema foi corrigido. #1853 #1865

### <a name="new"></a>Novo
* O Storage Explorer tem agora uma UI de Definições dedicada. Pode aceder-lhe a partir de Configurações → de edição ou clicando no ícone Definições (a engrenagem) na barra de ferramentas vertical da esquerda. Esta funcionalidade é o primeiro passo que estamos a dar para fornecer uma variedade de [configurações solicitadas pelo utilizador.](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate) A partir desta versão são suportadas as seguintes definições:
  * Tema
  * Proxy
  * Logout na #6 de saída
  * Ativar o sinal de fluxo de código do dispositivo
  * Atualizar #1526 automático
  * Ativar a AzCopy
  * Duração AzCopy SAS Se houver outras definições que deseja ver adicionadas, por favor [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) descrevendo a definição que deseja ver.
* O Storage Explorer suporta agora discos geridos. Pode:
  * Faça o upload de um VHD no local para um novo Disco
  * Faça o download de um disco
  * Copiar e colar discos em grupos de recursos e regiões
  * Eliminar Discos
  * Criar uma imagem instantânea de um disco

O upload, download e cópia transversal dos discos são alimentados pela AzCopy v10.
* O Storage Explorer pode agora ser instalado através da loja Snap no Linux. Quando instalar através da loja Snap, todas as dependências são instaladas para si, incluindo .NET Core! Atualmente, verificamos que o Storage Explorer funciona bem em Ubuntu e CentOS. Se encontrar problemas instalados na loja Snap em outros distros Linux, [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Para saber mais sobre a instalação na loja Snap, consulte o nosso [guia de arranque.](./vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux) #68
* Foram feitas duas grandes alterações para anexar com o Azure Ative Directory (Azure AD) que se destinam a tornar a funcionalidade mais útil para os utilizadores da ADLS Gen2:
  * Agora selecione o inquilino em que o recurso que está a anexar está. Isto significa que já não precisa de ter acesso rbac à subscrição do recurso.
  * Se estiver a anexar um recipiente de bolhas ADLS Gen2, pode agora fixar-se a um caminho específico no recipiente.
* Ao gerir ACLs para ficheiros e pastas ADLS Gen2, o Storage Explorer irá agora mostrar os nomes amigáveis para entidades na ACL. #957
* Ao adicionar via OID a um ADLS Gen2 ACL, o Storage Explorer irá agora validar que o OID pertence a uma entidade válida no seu inquilino. #1603
* Os atalhos de teclado para navegar entre separadores utilizam agora combinações de chaves mais padrão. #1018
* O meio clicando num separador irá agora fechá-lo. #1348
* Se uma transferência AzCopy contiver saltos e sem falhas, o Storage Explorer mostrará agora um ícone de aviso para realçar que os saltos ocorreram. #1490
* O AzCopy integrado foi atualizado para a versão 10.2.1. Além disso, pode agora ver a versão do AzCopy instalada no diálogo Sobre. #1343

### <a name="fixes"></a>Correções
* Muitos utilizadores encontraram várias "não podem ler versão de indefinidos" ou "não conseguem ler a ligação de erros indefinidos" ao trabalhar com contas de armazenamento anexadas. Embora continuemos a investigar a causa principal deste problema, em 1.10.0 melhorámos o tratamento de erros em torno do carregamento de Contas de Armazenamento anexadas. #1626, #985 e #1532
* Era possível que a árvore exploradora (do lado esquerdo) entrasse num estado em que o foco saltasse para o nó superior repetidamente. Este problema foi corrigido. #1596
* Ao gerir as fotos de uma bolha, os leitores de ecrã não leriam o timetamp associado ao instantâneo. Este problema foi corrigido. #1202
* A definição de procuração no macOS não estava a ser definida a tempo de o processo de autenticação os utilizar. Este problema foi corrigido. #1567
* Se uma Conta de Armazenamento numa nuvem soberana fosse anexada usando o nome e a chave, a AzCopy não funcionaria. Este problema foi corrigido. #1544
* Ao ligar através de uma cadeia de ligação, o Storage Explorer irá agora remover os espaços de fuga. #1387

### <a name="known-issues"></a>Problemas Conhecidos
* A definição de Atualização Automática ainda não afeta todas as operações no Blob Explorer.
* As funcionalidades do Disco Gerido não são suportadas no Azure Stack.
* Se um upload ou pasta de disco falhar e um novo Disco foi criado antes da falha, o Storage Explorer não apagará o Disco para si.
* Dependendo de quando cancela um upload ou pasta de disco, é possível deixar o novo Disco num estado corrompido. Se isto acontecer, ou é necessário eliminar o novo Disco ou ligar manualmente para as APIs do disco para substituir o conteúdo do Disco de modo a que não seja mais corrompido.
* Dependendo de quando cancela um upload ou pasta de disco, é possível deixar o novo Disco num estado corrompido. Se isto acontecer, ou é necessário eliminar o novo Disco ou ligar manualmente para as APIs do disco para substituir o conteúdo do Disco de modo a que não seja mais corrompido.
* Ao realizar um download não-AzCopy Blob, o MD5 para ficheiros grandes não está a ser verificado. Isto deve-se a um erro no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o [guia de resolução de problemas](./storage/common/storage-explorer-troubleshooting.md) para obter mais informações.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolhas, pode causar um erro que impeça que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão. Para contornar este problema ao fazer o upload ou o download de um recipiente de bolhas, pode utilizar a funcionalidade AzCopy experimental.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* A Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação suave
   * ADLS Gen2
   * Managed Disks
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* O Explorador de Armazenamento em Execução no Linux requer que certas dependências sejam instaladas primeiro. Consulte o [guia de resolução de problemas](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) do Storage Explorer para obter mais informações.


## <a name="version-1100"></a>Versão 1.10.0
9/12/2019

### <a name="new"></a>Novo

* O Storage Explorer tem agora uma UI de Definições dedicada. Pode aceder-lhe a partir de Configurações → de edição ou clicando no ícone Definições (a engrenagem) na barra de ferramentas vertical da esquerda. Esta funcionalidade é o primeiro passo que estamos a dar para fornecer uma variedade de [configurações solicitadas pelo utilizador.](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate) A partir desta versão são suportadas as seguintes definições:
    * Tema
    * Proxy
    * Logout na [#6 de](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6) saída
    * Ativar o sinal de fluxo de código do dispositivo
    * Atualizar [#1526 automático](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526)
    * Ativar a AzCopy
    * Duração AzCopy SAS

    Se houver outras definições que deseja ver adicionadas, por favor [abra um problema no GitHub descrevendo a definição que deseja ver](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* O Storage Explorer suporta agora discos geridos. Pode:
    * Faça o upload de um VHD no local para um novo Disco
    * Faça o download de um disco
    * Copiar e colar discos em grupos de recursos e regiões
    * Eliminar Discos
    * Criar uma imagem instantânea de um disco

    O upload, download e cópia transversal dos discos são alimentados pela AzCopy v10.
* O Storage Explorer pode agora ser instalado através da loja Snap no Linux. Quando instalar através da loja Snap, todas as dependências são instaladas para si, incluindo .NET Core! Atualmente, verificamos que o Storage Explorer funciona bem em Ubuntu e CentOS. Se encontrar problemas instalados na loja Snap em outros distros Linux, [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Para saber mais sobre a instalação na loja Snap, consulte o nosso [guia de arranque.](./vs-azure-tools-storage-manage-with-storage-explorer.md) [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Foram feitas duas grandes alterações para anexar com o Azure Ative Directory (Azure AD) que se destinam a tornar a funcionalidade mais útil para os utilizadores da ADLS Gen2: * Seleciona agora o inquilino em que se encontra o recurso em que se encontra. Isto significa que já não precisa de ter acesso rbac à subscrição do recurso.
        * Se estiver a prender um recipiente de bolhas ADLS Gen2, pode agora fixar-se a um caminho específico no recipiente.
* Ao gerir ACLs para ficheiros e pastas ADLS Gen2, o Storage Explorer irá agora mostrar os nomes amigáveis para entidades na ACL. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Ao adicionar via OID a um ADLS Gen2 ACL, o Storage Explorer irá agora validar que o OID pertence a uma entidade válida no seu inquilino. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* Os atalhos de teclado para navegar entre separadores utilizam agora combinações de chaves mais padrão. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* O meio clicando num separador irá agora fechá-lo. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Se uma transferência AzCopy contiver saltos e sem falhas, o Storage Explorer mostrará agora um ícone de aviso para realçar que os saltos ocorreram. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* O AzCopy integrado foi atualizado para a versão 10.2.1. Além disso, pode agora ver a versão do AzCopy instalada no diálogo Sobre. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Correções

* Muitos utilizadores encontraram várias "não podem ler versão de indefinidos" ou "não conseguem ler a ligação de erros indefinidos" ao trabalhar com contas de armazenamento anexadas. Embora continuemos a investigar a causa principal deste problema, em 1.10.0 melhorámos o tratamento de erros em torno do carregamento de Contas de Armazenamento anexadas. [#1626,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626) [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)e [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Era possível que a árvore exploradora (do lado esquerdo) entrasse num estado em que o foco saltasse para o nó superior repetidamente. Este problema foi corrigido. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Ao gerir as fotos de uma bolha, os leitores de ecrã não leriam o timetamp associado ao instantâneo. Este problema foi corrigido. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* A definição de procuração no macOS não estava a ser definida a tempo de o processo de autenticação os utilizar. Este problema foi corrigido. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Se uma Conta de Armazenamento numa nuvem soberana fosse anexada usando o nome e a chave, a AzCopy não funcionaria. Este problema foi corrigido. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Ao ligar através de uma cadeia de ligação, o Storage Explorer irá agora remover os espaços de fuga. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Problemas Conhecidos

* A definição de Atualização Automática ainda não afeta todas as operações no Blob Explorer.
* As funcionalidades do Disco Gerido não são suportadas no Azure Stack.
* Se um upload ou pasta de disco falhar e um novo Disco foi criado antes da falha, o Storage Explorer não apagará o Disco para si.
* Dependendo de quando cancela um upload ou pasta de disco, é possível deixar o novo Disco num estado corrompido. Se isto acontecer, ou é necessário eliminar o novo Disco ou ligar manualmente para as APIs do disco para substituir o conteúdo do Disco de modo a que não seja mais corrompido.
* Ao realizar um download não-AzCopy Blob, o MD5 para ficheiros grandes não está a ser verificado. Isto deve-se a um erro no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o [guia de resolução de problemas](./storage/common/storage-explorer-troubleshooting.md) para obter mais informações.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolhas, pode causar um erro que impeça que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôss. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão. Para contornar este problema ao fazer o upload ou o download de um recipiente de bolhas, pode utilizar a funcionalidade AzCopy experimental.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* A Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação suave
   * ADLS Gen2
   * Managed Disks
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* O Explorador de Armazenamento em Execução no Linux requer que certas dependências sejam instaladas primeiro. Consulte o [guia de resolução de problemas](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) do Storage Explorer para obter mais informações.

## <a name="version-190"></a>Versão 1.9.0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Baixar Azure Storage Explorer 1.9.0
- [Azure Storage Explorer 1.9.0 para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.9.0 para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.9.0 para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Novo

* Pode agora anexar recipientes Blob através de Azure AD (permissões RBAC ou ACL). Esta funcionalidade destina-se a ajudar os utilizadores que tenham acesso a contentores, mas não as Contas de Armazenamento em que se encontram. Consulte o nosso Guia de Início para mais informações sobre esta funcionalidade.
* Adquirir e quebrar o arrendamento agora trabalhar com a RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* Gerir políticas de acesso e estabelecer o nível de acesso público agora funcionam com o RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* Eliminar as pastas blob agora funciona com o RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* Mudar o nível de acesso blob agora funciona com o RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Agora pode reiniciar rapidamente o Acesso Rápido através de "Help" → "Reset". [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Funcionalidades de Pré-visualização

* O sinal de fluxo de código do dispositivo já está disponível para pré-visualização. Para o ativar, vá a "Pré-visualização" → "Use Device Code Flow'in". Encorajamos todos os utilizadores que tenham tido problemas com janelas de inscrição em branco para experimentar esta funcionalidade, uma vez que pode revelar-se uma forma mais fiável de iniciar snus.
* O Storage Explorer integrado com o AzCopy está atualmente disponível para pré-visualização. Para o ativar, vá a "Pré-visualização" → "Use AzCopy para carregar e descarregar bolhas melhoradas". As transferências blob concluídas com a AzCopy devem ser mais rápidas e mais performantes.

### <a name="fixes"></a>Correções

* Fixo não pode carregar mais de 50 subscrições para uma conta. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Fixo o botão "Iniciar in" que não funciona na barra de informação que aparece quando uma ligação direta falha. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Fixo não ser para carregar ficheiros .app no macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Fixo "Retry All" não funcionando para um rebatizador de bolhas falhado. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Fixo "Cancelar" não funcionando durante a abertura de uma bolha. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Fixou vários problemas de ortografia e de ponta de ferramentas em todo o produto. Muito obrigado a todos os que relataram estes problemas! [#1303,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303) [#1328,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328) [#1329,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329) [#1331,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331) [#1336,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336) [#1352,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352) [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368) [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Problemas Conhecidos

* Ao realizar um download não-AzCopy Blob, o MD5 para ficheiros grandes não está a ser verificado. Isto deve-se a um erro no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o [guia de resolução de problemas](./storage/common/storage-explorer-troubleshooting.md) para obter mais informações.
* Tentar aceder a Blobs Gen2 da ADLS quando está por trás de um representante pode falhar.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolhas, pode causar um erro que impeça que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão. Para contornar este problema ao fazer o upload ou o download de um recipiente de bolhas, pode utilizar a funcionalidade AzCopy experimental.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* A Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação suave
   * ADLS Gen2
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* O Explorador de Armazenamento em Execução no Linux requer que certas dependências sejam instaladas primeiro. Consulte o [guia de resolução de problemas](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) do Storage Explorer para obter mais informações.

## <a name="version-181"></a>Versão 1.8.1
5/13/2019

### <a name="hotfixes"></a>Correções
* Em alguns casos, clicar em "Carregar mais" ao nível dos recursos não devolveria a página seguinte dos recursos. Este problema foi corrigido. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* No Windows, os downloads do AzCopy falhariam se um único ficheiro ou pasta estivesse a ser descarregado e o nome do ficheiro ou pasta tivesse um carácter inválido para um caminho do Windows. Este problema foi corrigido. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* Em casos extremamente raros, enquanto executava um rebatizador de uma Partilha de Ficheiros ou um rebatizador numa Partilha de Ficheiros, se as cópias para o rebatizador falharam, ou se o Storage Explore não foi capaz de confirmar o sucesso das cópias com o Azure, havia o potencial para o Explorador de Armazenamento apagar os ficheiros originais antes de a cópia ter terminado. Este problema foi corrigido.

### <a name="new"></a>Novo

* A versão integrada AzCopy foi atualizada para a versão 10.1.0.
* Ctrl/Cmd+R pode agora ser usado para refrescar o editor atualmente focado. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* A versão Azure Stack Storage API foi alterada para 2017-04-17.
* O Diálogo de Acesso de Gestão para ADLS Gen2 irá agora manter a Máscara sincronizada de uma forma semelhante a outras ferramentas de permissões POSIX. A UI também o avisará se for feita uma alteração que faça com que as permissões de um utilizador ou grupo excedam os limites da Máscara. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Para uploads AzCopy, a bandeira para calcular e definir o hash MD5 está agora ativada. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funcionalidades de Pré-visualização

* O sinal de fluxo de código do dispositivo já está disponível para pré-visualização. Para o ativar, vá a "Pré-visualização" → "Use Device Code Flow'in". Encorajamos todos os utilizadores que tenham tido problemas com janelas de inscrição em branco para experimentar esta funcionalidade, uma vez que pode revelar-se uma forma mais fiável de iniciar snus.
* O Storage Explorer integrado com o AzCopy está atualmente disponível para pré-visualização. Para o ativar, vá a "Pré-visualização" → "Use AzCopy para carregar e descarregar bolhas melhoradas". As transferências blob concluídas com a AzCopy devem ser mais rápidas e mais performantes.

### <a name="fixes"></a>Correções

* O diálogo 'Políticas de Acesso' deixará de definir uma data de validade nas Políticas de Acesso ao Armazenamento que não tenham caducidade. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Foram feitas algumas alterações ao diálogo Generate SAS para garantir que as Políticas de Acesso Armazenadas são utilizadas corretamente ao gerar um SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Ao tentar carregar um ficheiro alinhado não-512 para uma página Blob, o Storage Explorer irá agora expor um erro mais relevante. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Copiar um recipiente Blob que utilizasse um nome de visor falharia. Agora, o nome real do recipiente Blob é usado. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Tentar executar certas ações numa pasta ADLS Gen2 que tivesse caracteres unicodigo em seu nome falharia. Todas as ações devem agora funcionar. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Problemas Conhecidos

* Ao realizar um download não-AzCopy Blob, o MD5 para ficheiros grandes não está a ser verificado. Isto deve-se a um erro no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o [guia de resolução de problemas](./storage/common/storage-explorer-troubleshooting.md) para obter mais informações.
* Tentar aceder a Blobs Gen2 da ADLS quando está por trás de um representante pode falhar.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolhas, pode causar um erro que impeça que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão. Para contornar este problema ao fazer o upload ou o download de um recipiente de bolhas, pode utilizar a funcionalidade AzCopy experimental.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* A Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação suave
   * ADLS Gen2
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* O Explorador de Armazenamento em Execução no Linux requer que certas dependências sejam instaladas primeiro. Consulte o [guia de resolução de problemas](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) do Storage Explorer para obter mais informações.

## <a name="version-180"></a>Versão 1.8.0
01/05/2019

### <a name="new"></a>Novo

* A versão integrada AzCopy foi atualizada para a versão 10.1.0.
* Ctrl/Cmd+R pode agora ser usado para refrescar o editor atualmente focado. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* A versão Azure Stack Storage API foi alterada para 2017-04-17.
* O Diálogo de Acesso de Gestão para ADLS Gen2 irá agora manter a Máscara sincronizada de uma forma semelhante a outras ferramentas de permissões POSIX. A UI também o avisará se for feita uma alteração que faça com que as permissões de um utilizador ou grupo excedam os limites da Máscara. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Para uploads AzCopy, a bandeira para calcular e definir o hash MD5 está agora ativada. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funcionalidades de Pré-visualização

* O sinal de fluxo de código do dispositivo já está disponível para pré-visualização. Para o ativar, vá a "Pré-visualização" → "Use Device Code Flow'in". Encorajamos todos os utilizadores que tenham tido problemas com janelas de inscrição em branco para experimentar esta funcionalidade, uma vez que pode revelar-se uma forma mais fiável de iniciar snus.
* O Storage Explorer integrado com o AzCopy está atualmente disponível para pré-visualização. Para o ativar, vá a "Pré-visualização" → "Use AzCopy para carregar e descarregar bolhas melhoradas". As transferências blob concluídas com a AzCopy devem ser mais rápidas e mais performantes.

### <a name="fixes"></a>Correções

* O diálogo 'Políticas de Acesso' deixará de definir uma data de validade nas Políticas de Acesso ao Armazenamento que não tenham caducidade. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Foram feitas algumas alterações ao diálogo Generate SAS para garantir que as Políticas de Acesso Armazenadas são utilizadas corretamente ao gerar um SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Ao tentar carregar um ficheiro alinhado não-512 para uma página Blob, o Storage Explorer irá agora expor um erro mais relevante. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Copiar um recipiente Blob que utilizasse um nome de visor falharia. Agora, o nome real do recipiente Blob é usado. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Tentar executar certas ações numa pasta ADLS Gen2 que tivesse caracteres unicodigo em seu nome falharia. Todas as ações devem agora funcionar. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Problemas Conhecidos

* Ao realizar um download não-AzCopy Blob, o MD5 para ficheiros grandes não está a ser verificado. Isto deve-se a um erro no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o [guia de resolução de problemas](./storage/common/storage-explorer-troubleshooting.md) para obter mais informações.
* Tentar aceder a Blobs Gen2 da ADLS quando está por trás de um representante pode falhar.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolhas, pode causar um erro que impeça que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão. Para contornar este problema ao fazer o upload ou o download de um recipiente de bolhas, pode utilizar a funcionalidade AzCopy experimental.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* A Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação suave
   * ADLS Gen2
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* O Explorador de Armazenamento em Execução no Linux requer que certas dependências sejam instaladas primeiro. Consulte o [guia de resolução de problemas](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) do Storage Explorer para obter mais informações.

## <a name="version-170"></a>Versão 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Baixar Azure Storage Explorer 1.7.0
- [Azure Storage Explorer 1.7.0 para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Explorador de Armazenamento Azure 1.7.0 para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Explorador de Armazenamento Azure 1.7.0 para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Novo

* Pode agora alterar o proprietário e o grupo proprietário ao gerir o acesso a um contentor, ficheiro ou pasta ADLS Gen2.
* No Windows, atualizar o Storage Explorer a partir do produto é agora uma instalação incremental. Isto deve resultar numa experiência de atualização mais rápida. Se preferir uma instalação limpa, pode descarregar o [instalador](https://azure.microsoft.com/features/storage-explorer/) por si mesmo e instalar manualmente. #1089

### <a name="preview-features"></a>Funcionalidades de Pré-visualização

* O sinal de fluxo de código do dispositivo já está disponível para pré-visualização. Para o ativar, vá a "Pré-visualização" → "Use Device Code Flow'in". Encorajamos todos os utilizadores que tenham tido problemas com janelas de inscrição em branco para experimentar esta funcionalidade, uma vez que pode revelar-se uma forma mais fiável de iniciar snus. #938
* O Storage Explorer integrado com o AzCopy está atualmente disponível para pré-visualização. Para o ativar, vá a "Pré-visualização" → "Use AzCopy para carregar e descarregar bolhas melhoradas". As transferências blob concluídas com a AzCopy devem ser mais rápidas e mais performantes.

### <a name="fixes"></a>Correções

* Pode agora escolher o tipo de bolha que pretende carregar como quando o AzCopy estiver ativado. #1111
* Anteriormente, se tivesse ativado websites estáticos para uma conta de armazenamento ADLS Gen2 e depois o anexado com nome e chave, o Storage Explorer não teria detetado que o espaço hierárquico estava ativado. Este problema foi corrigido. #1081
* No editor blob, a triagem por dias de retenção restantes ou estatuto foi quebrada. Este problema foi corrigido. #1106
* Depois de 1.5.0, o Storage Explorer já não esperou que as cópias laterais do servidor terminassem antes de reportar o sucesso durante um rebatizador ou copiar & pasta. Este problema foi corrigido. #976
* Ao utilizar a função AzCopy experimental, o comando copiado depois de clicar em "Copy command to clipboard" nem sempre foi executado por si só. Agora, todos os comandos necessários para executar a transferência manualmente serão copiados. #1079
* Anteriormente, as bolhas da ADLS Gen2 não eram acessíveis se estivesse por detrás de um representante. Isto deveu-se a um bug numa nova biblioteca de rede usada pelo Storage SDK. Em 1.7.0, foi feita uma tentativa de mitigar esta questão, mas algumas pessoas podem continuar a ver problemas. Uma correção completa será lançada numa futura atualização. #1090
* Em 1.7.0, o diálogo do ficheiro de salvamento lembra-se corretamente da última localização para a qual guardou um ficheiro. #16
* No painel de propriedades, o nível SKU de uma conta de armazenamento estava sendo mostrado como o tipo da conta. Este problema foi corrigido. #654
* Às vezes, era impossível quebrar o arrendamento de uma bolha, mesmo que tivesse introduzido o nome da bolha corretamente. Este problema foi corrigido. #1070

### <a name="known-issues"></a>Problemas Conhecidos

* Ao utilizar o RBAC, o Storage Explorer requer algumas permissões de camada de gestão para aceder aos seus recursos de armazenamento. Consulte o [guia de resolução de problemas](./storage/common/storage-explorer-troubleshooting.md) para obter mais informações.
* Tentar aceder a Blobs Gen2 da ADLS quando está por trás de um representante pode falhar.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolhas, pode causar um erro que impeça que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte #537 para mais informações.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolhas, pode causar um erro que impeça que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Para mais informações, consulte #537.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão. Para contornar este problema ao fazer o upload ou o download de um recipiente de bolhas, pode utilizar a funcionalidade AzCopy experimental.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* A Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação suave
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* Em 1.6.1, entidades adicionadas aos ACLs da ADLS Gen2 pela ObjectId que não eram utilizadores foram sempre adicionadas como grupos. Agora, apenas grupos são adicionados como grupos, e entidades como Aplicações Empresariais e Principais de Serviço são adicionados como utilizadores. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Se uma conta de armazenamento da ADLS Gen2 não tivesse contentores e estivesse anexada com nome e chave, então o Explorador de Armazenamento não detetaria que a Conta de Armazenamento era ADLS Gen2. Este problema foi corrigido. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* Em 1.6.0, os conflitos durante a cópia e a pasta não levariam a uma resolução. Em vez disso, a cópia conflituosa simplesmente falharia. Agora, no primeiro conflito, perguntar-lhe-ão como gostaria que fosse resolvido. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Devido às limitações da API, toda a validação dos ObjectIds no diálogo 'Acesso de Gestão' foi desativada. A validação só irá ocorrer agora para as UPNs do utilizador. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* No diálogo ADLS Gen2 Manage Access, as permissões para um grupo não puderam ser modificadas. Este problema foi corrigido. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Adicionou suporte de drag e drop upload ao editor da ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* A propriedade URL no diálogo de propriedades para ficheiros e pastas ADLS Gen2 às vezes faltava um '/'. Este problema foi corrigido. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Se obter as permissões atuais para um recipiente, ficheiro ou pasta ADLS Gen2 falhar, então o erro é agora devidamente visualizado no registo de atividade. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* O caminho temporário criado para a abertura de ficheiros foi encurtado para reduzir a possibilidade de criar um caminho que seja mais longo do que MAX_PATH no Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* O diálogo Connect aparece agora corretamente quando não há nenhum registo nos utilizadores e não foram anexados recursos. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* Em 1.6.0, a poupança de propriedades para blobs e ficheiros não-HNS codificaria o valor de cada propriedade. Isto resultou numa codificação desnecessária de valores que continham apenas caracteres ASCII. Agora, os valores só serão codificados se contiverem caracteres não ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* O carregamento de uma pasta para um contentor blob não-HNS falharia se fosse utilizado um SAS e o SAS não tivesse permissões de leitura. Este problema foi corrigido. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Cancelar uma transferência da AzCopy não funcionou. Este problema foi corrigido. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* O AzCopy falharia ao tentar descarregar uma pasta de um contentor ADLS Gen2 Blob se a pasta tivesse espaços em seu nome. Este problema foi corrigido. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* O editor da CosmosDB foi quebrado em 1.6.0. Está agora arranjado. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Novo

* Agora pode utilizar o Storage Explorer para aceder aos seus dados Blob através [do RBAC.](./storage/common/storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) Se estiver assinado e o Storage Explorer não conseguir recuperar as chaves da sua conta de Armazenamento, então será usado um token OAuth para autenticar quando interagir com os seus dados.
* O Storage Explorer suporta agora as contas de armazenamento ADLS Gen2. Quando o Storage Explorer detetar que o espaço hierárquico está ativado para uma conta de Armazenamento, verá "(Pré-visualização ADLS Gen2)" ao lado do nome da sua conta de Armazenamento. O Storage Explorer é capaz de detetar se o espaço de nome hierárquico está ou não ativado quando está assinado ou se anexou a sua Conta de Armazenamento com nome e chave. Para contas de armazenamento ADLS Gen2, pode utilizar o Storage Explorer para:
  * Criar e apagar contentores
  * Gerir as propriedades e permissões dos contentores (do lado esquerdo)
  * Ver e navegar dados dentro de contentores
  * Criar novas pastas
  * Carregar, transferir, renomear e eliminar ficheiros e pastas
  * Gerir propriedades e permissões de ficheiros e pastas (lado direito).

    Outras funcionalidades típicas do Blob, como Soft Delete e Snapshots, não estão atualmente disponíveis. A gestão de permissões também só está disponível quando assinada. Além disso, ao trabalhar numa conta de armazenamento ADLS Gen2, o Storage Explorer utilizará o AzCopy para todos os uploads e downloads e por defeito para usar o nome e as credenciais principais para todas as operações, se disponível.
* Após um forte feedback do utilizador, o leasing de break pode ser usado mais uma vez para quebrar arrendamentos em várias bolhas ao mesmo tempo.

### <a name="known-issues"></a>Problemas Conhecidos

* Ao descarregar de uma conta de Armazenamento ADLS Gen2, se um dos ficheiros que estão a ser transferidos já existir, então o AzCopy por vezes falhará. Isto será corrigido num hotfix próximo.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolhas, pode causar um erro que impeça que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Para mais informações, consulte #537.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão. Para contornar este problema ao fazer o upload ou o download de um recipiente de bolhas, pode utilizar a funcionalidade AzCopy experimental.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* A Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação suave
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* Devido às limitações da API, toda a validação dos ObjectIds no diálogo 'Acesso de Gestão' foi desativada. A validação só irá ocorrer agora para as UPNs do utilizador. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* No diálogo ADLS Gen2 Manage Access, as permissões para um grupo não puderam ser modificadas. Este problema foi corrigido. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Adicionou suporte de drag e drop upload ao editor da ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* A propriedade URL no diálogo de propriedades para ficheiros e pastas ADLS Gen2 às vezes faltava um '/'. Este problema foi corrigido. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Se obter as permissões atuais para um recipiente, ficheiro ou pasta ADLS Gen2 falhar, então o erro é agora devidamente visualizado no registo de atividade. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* O caminho temporário criado para a abertura de ficheiros foi encurtado para reduzir a possibilidade de criar um caminho que seja mais longo do que MAX_PATH no Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* O diálogo Connect aparece agora corretamente quando não há nenhum registo nos utilizadores e não foram anexados recursos. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* Em 1.6.0, a poupança de propriedades para blobs e ficheiros não-HNS codificaria o valor de cada propriedade. Isto resultou numa codificação desnecessária de valores que continham apenas caracteres ASCII. Agora, os valores só serão codificados se contiverem caracteres não ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* O carregamento de uma pasta para um contentor blob não-HNS falharia se fosse utilizado um SAS e o SAS não tivesse permissões de leitura. Este problema foi corrigido. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Cancelar uma transferência da AzCopy não funcionou. Este problema foi corrigido. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* O AzCopy falharia ao tentar descarregar uma pasta de um contentor ADLS Gen2 Blob se a pasta tivesse espaços em seu nome. Este problema foi corrigido. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* O editor da CosmosDB foi quebrado em 1.6.0. Está agora arranjado. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Novo

* Agora pode utilizar o Storage Explorer para aceder aos seus dados Blob através [do RBAC.](./storage/common/storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) Se estiver assinado e o Storage Explorer não conseguir recuperar as chaves da sua conta de Armazenamento, então será usado um token OAuth para autenticar quando interagir com os seus dados.
* O Storage Explorer suporta agora as contas de armazenamento ADLS Gen2. Quando o Storage Explorer detetar que o espaço hierárquico está ativado para uma conta de Armazenamento, verá "(Pré-visualização ADLS Gen2)" ao lado do nome da sua conta de Armazenamento. O Storage Explorer é capaz de detetar se o espaço de nome hierárquico está ou não ativado quando está assinado ou se anexou a sua Conta de Armazenamento com nome e chave. Para contas de armazenamento ADLS Gen2, pode utilizar o Storage Explorer para:
  * Criar e apagar contentores
  * Gerir as propriedades e permissões dos contentores (do lado esquerdo)
  * Ver e navegar dados dentro de contentores
  * Criar novas pastas
  * Carregar, transferir, renomear e eliminar ficheiros e pastas
  * Gerir propriedades e permissões de ficheiros e pastas (lado direito).
    
    Outras funcionalidades típicas do Blob, como Soft Delete e Snapshots, não estão atualmente disponíveis. A gestão de permissões também só está disponível quando assinada. Além disso, ao trabalhar numa conta de armazenamento ADLS Gen2, o Storage Explorer utilizará o AzCopy para todos os uploads e downloads e por defeito para usar o nome e as credenciais principais para todas as operações, se disponível.
* Após um forte feedback do utilizador, o leasing de break pode ser usado mais uma vez para quebrar arrendamentos em várias bolhas ao mesmo tempo.

### <a name="known-issues"></a>Problemas Conhecidos

* Ao descarregar de uma conta de Armazenamento ADLS Gen2, se um dos ficheiros que estão a ser transferidos já existir, então o AzCopy por vezes falhará. Isto será corrigido num hotfix próximo.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolhas, pode causar um erro que impeça que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Para mais informações, consulte #537.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão. Para contornar este problema ao fazer o upload ou o download de um recipiente de bolhas, pode utilizar a funcionalidade AzCopy experimental.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* A Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação suave
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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

* Agora pode utilizar o Storage Explorer para aceder aos seus dados Blob através [do RBAC.](./storage/common/storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) Se estiver assinado e o Storage Explorer não conseguir recuperar as chaves da sua conta de Armazenamento, então será usado um token OAuth para autenticar quando interagir com os seus dados.
* O Storage Explorer suporta agora as contas de armazenamento ADLS Gen2. Quando o Storage Explorer detetar que o espaço hierárquico está ativado para uma conta de Armazenamento, verá "(Pré-visualização ADLS Gen2)" ao lado do nome da sua conta de Armazenamento. O Storage Explorer é capaz de detetar se o espaço de nome hierárquico está ou não ativado quando está assinado ou se anexou a sua Conta de Armazenamento com nome e chave. Para contas de armazenamento ADLS Gen2, pode utilizar o Storage Explorer para:
  * Criar e apagar contentores
  * Gerir as propriedades e permissões dos contentores (do lado esquerdo)
  * Ver e navegar dados dentro de contentores
  * Criar novas pastas
  * Carregar, transferir, renomear e eliminar ficheiros e pastas
  * Gerir propriedades e permissões de ficheiros e pastas (lado direito).
    
    Outras funcionalidades típicas do Blob, como Soft Delete e Snapshots, não estão atualmente disponíveis. A gestão de permissões também só está disponível quando assinada. Além disso, ao trabalhar numa conta de armazenamento ADLS Gen2, o Storage Explorer utilizará o AzCopy para todos os uploads e downloads e por defeito para usar o nome e as credenciais principais para todas as operações, se disponível.
* Após um forte feedback do utilizador, o leasing de break pode ser usado mais uma vez para quebrar arrendamentos em várias bolhas ao mesmo tempo.

### <a name="known-issues"></a>Problemas Conhecidos

* Ao descarregar de uma conta de Armazenamento ADLS Gen2, se um dos ficheiros que estão a ser transferidos já existir, então o AzCopy por vezes falhará. Isto será corrigido num hotfix próximo.
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolhas, pode causar um erro que impeça que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Para mais informações, consulte #537.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão. Para contornar este problema ao fazer o upload ou o download de um recipiente de bolhas, pode utilizar a funcionalidade AzCopy experimental.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* A Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação suave
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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

* Agora pode utilizar [o AzCopy v10 (Preview)](https://github.com/Azure/azure-storage-azcopy) para carregar e descarregar Blobs. Para ativar esta funcionalidade aceda ao menu "Experimental" e, em seguida, clique em "Use AzCopy para carregar e descarregar bolhas melhoradas". Quando ativado, o AzCopy será utilizado nos seguintes cenários:
   * Faça o upload de pastas e ficheiros para recipientes blob, seja através da barra de ferramentas ou arraste e deixe cair.
   * Descarregamento de pastas e ficheiros, seja através da barra de ferramentas ou do menu de contexto.

* Adicionalmente, ao utilizar a AzCopy:
   * Pode copiar o comando AzCopy utilizado para executar a transferência para a sua área de transferência. Basta clicar em "Copy AzCopy Command to Clipboard" no registo de atividade.
   * Terá de refrescar manualmente o editor blob após o upload.
   * O upload de ficheiros para apêndices não é suportado, e os ficheiros VHD serão carregados como bolhas de página, e todos os outros ficheiros serão carregados como blobs de bloco.
   * Erros e conflitos que ocorram durante o upload ou download não serão surgidos até que um upload ou download esteja terminado.

Finalmente, o suporte para a utilização do AzCopy com ações de ficheiros virá no futuro.
* O Storage Explorer está agora a utilizar a versão Eletrão 2.0.11.
* Quebrar arrendamentos agora só pode ser realizado em uma bolha de cada vez. Além disso, você tem que inserir o nome da bolha cujo contrato está quebrando. Esta alteração foi feita para reduzir a probabilidade de quebrar acidentalmente um contrato de arrendamento, especialmente para os VMs. #394
* Se alguma vez encontrar problemas de inscrição, pode agora tentar repor a autenticação. Vá ao menu "Ajuda" e clique em "Reset" para aceder a esta capacidade. #419

### <a name="fix"></a>Correção

* Após um forte feedback do utilizador, o nódulador padrão foi re-activado. Ainda pode adicionar ligações emuladoras adicionais através do diálogo Connect, mas se o seu emulador estiver configurado para utilizar as portas predefinidas, também pode utilizar o nó "Emulator * Portas Predefinidas" em "Contas locais & anexas/de armazenamento". #669
* O Storage Explorer não permitirá mais definir valores de metadados blob que tenham espaço em branco ou líder. #760
* O botão "Iniciar In" estava sempre ativado nas mesmas páginas do diálogo 'Ligar'. Está agora desativado quando apropriado. #761
* O Acesso Rápido deixará de gerar um erro na consola quando não foram adicionados itens de Acesso Rápido.

### <a name="known-issues"></a>Problemas Conhecidos

* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolhas, pode causar um erro que impeça que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Para mais informações, consulte #537.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não o desbloquear, comente sobre esta questão.
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão. Para contornar este problema ao fazer o upload ou o download de um recipiente de bolhas, pode utilizar a funcionalidade AzCopy experimental.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* A Azure Stack não suporta as seguintes funcionalidades. Tentar utilizar estas funcionalidades enquanto trabalha com os recursos do Azure Stack pode resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação suave
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* A versão Azure Resource Management API foi revertida para desbloquear os utilizadores do Governo dos EUA. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Os spinners de carregamento estão agora a usar animações CSS para reduzir a quantidade de GPU usada pelo Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Novo
* Os anexos de recursos externos, tais como para ligações SAS e emuladores, foram significativamente melhorados. Agora pode:
   * Personalize o nome de exibição do recurso que está a anexar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Ligue a vários emuladores locais utilizando diferentes portas. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicione recursos anexados ao Acesso Rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* O Storage Explorer suporta agora o Soft Delete. Pode:
   * Configure uma política de eliminação suave clicando no nó blob containers para a sua conta de Armazenamento.
   * Ver bolhas apagadas suaves no Blob Editor selecionando "Blobs ativos e apagados" no dropdown ao lado da barra de navegação.
   * Bolhas macias e macias.

### <a name="fixes"></a>Correções
* A ação "Configurar as Definições CORS" já não está disponível nas contas de Armazenamento Premium porque as contas de Armazenamento Premium não suportam CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Existe agora uma propriedade de Assinatura de Acesso Partilhado para Serviços Anexos SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação "Definir Nível de Acesso Predefinido" já está disponível para contas de Armazenamento Blob e GPV2 que foram fixadas ao Acesso Rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Storage Explorer não mostraria as contas de Armazenamento Clássico. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao utilizar emuladores, como o Azure Storage Emulator ou o Azurite, terá de os fazer ouvir ligações nas portas predefinidas. Caso contrário, o Explorador de Armazenamento não será capaz de se ligar a eles.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se o fizer não o desbloqueie, por favor comente [sobre este assunto](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito [aqui.](https://github.com/Azure/azure-storage-node/issues/317)
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* A versão Azure Resource Management API foi revertida para desbloquear os utilizadores do Governo dos EUA. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Os spinners de carregamento estão agora a usar animações CSS para reduzir a quantidade de GPU usada pelo Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Novo
* Os anexos de recursos externos, tais como para ligações SAS e emuladores, foram significativamente melhorados. Agora pode:
   * Personalize o nome de exibição do recurso que está a anexar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Ligue a vários emuladores locais utilizando diferentes portas. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicione recursos anexados ao Acesso Rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* O Storage Explorer suporta agora o Soft Delete. Pode:
   * Configure uma política de eliminação suave clicando no nó blob containers para a sua conta de Armazenamento.
   * Ver bolhas apagadas suaves no Blob Editor selecionando "Blobs ativos e apagados" no dropdown ao lado da barra de navegação.
   * Bolhas macias e macias.

### <a name="fixes"></a>Correções
* A ação "Configurar as Definições CORS" já não está disponível nas contas de Armazenamento Premium porque as contas de Armazenamento Premium não suportam CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Existe agora uma propriedade de Assinatura de Acesso Partilhado para Serviços Anexos SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação "Definir Nível de Acesso Predefinido" já está disponível para contas de Armazenamento Blob e GPV2 que foram fixadas ao Acesso Rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Storage Explorer não mostraria as contas de Armazenamento Clássico. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao utilizar emuladores, como o Azure Storage Emulator ou o Azurite, terá de os fazer ouvir ligações nas portas predefinidas. Caso contrário, o Explorador de Armazenamento não será capaz de se ligar a eles.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se o fizer não o desbloqueie, por favor comente [sobre este assunto](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito [aqui.](https://github.com/Azure/azure-storage-node/issues/317)
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* Atualizar versão API de Gestão de Recursos Azure para 2018-07-01 para adicionar suporte para novos tipos de Conta de Armazenamento Azure. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Novo
* Os anexos de recursos externos, tais como para ligações SAS e emuladores, foram significativamente melhorados. Agora pode:
   * Personalize o nome de exibição do recurso que está a anexar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Ligue a vários emuladores locais utilizando diferentes portas. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicione recursos anexados ao Acesso Rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* O Storage Explorer suporta agora o Soft Delete. Pode:
   * Configure uma política de eliminação suave clicando no nó blob containers para a sua conta de Armazenamento.
   * Ver bolhas apagadas suaves no Blob Editor selecionando "Blobs ativos e apagados" no dropdown ao lado da barra de navegação.
   * Bolhas macias e macias.

### <a name="fixes"></a>Correções
* A ação "Configurar as Definições CORS" já não está disponível nas contas de Armazenamento Premium porque as contas de Armazenamento Premium não suportam CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Existe agora uma propriedade de Assinatura de Acesso Partilhado para Serviços Anexos SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação "Definir Nível de Acesso Predefinido" já está disponível para contas de Armazenamento Blob e GPV2 que foram fixadas ao Acesso Rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Storage Explorer não mostraria as contas de Armazenamento Clássico. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao utilizar emuladores, como o Azure Storage Emulator ou o Azurite, terá de os fazer ouvir ligações nas portas predefinidas. Caso contrário, o Explorador de Armazenamento não será capaz de se ligar a eles.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se o fizer não o desbloqueie, por favor comente [sobre este assunto](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito [aqui.](https://github.com/Azure/azure-storage-node/issues/317)
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* No primeiro lançamento, o Storage Explorer não conseguiu gerar a chave utilizada para encriptar dados sensíveis. Isto causaria problemas ao utilizar o Acesso Rápido e anexar recursos. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Se a sua conta não exigisse MFA para o seu inquilino de casa, mas o fez para outros inquilinos, o Storage Explorer seria incapaz de listar subscrições. Agora, depois de iniciar sessão com tal conta, o Storage Explorer pedir-lhe-á para reentrar nas suas credenciais e realizar MFA. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* O Storage Explorer não conseguiu anexar recursos da Azure Germany e do Governo dos EUA. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Se você se inscrevesse em duas contas que tinham o mesmo endereço de e-mail, o Storage Explorer às vezes deixaria de mostrar os seus recursos na vista da árvore. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Em máquinas Windows mais lentas, o ecrã de respingo às vezes demoraria muito tempo a aparecer. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* O diálogo de ligação apareceria mesmo que houvesse contas ou serviços anexados. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Novo
* Os anexos de recursos externos, tais como para ligações SAS e emuladores, foram significativamente melhorados. Agora pode:
   * Personalize o nome de exibição do recurso que está a anexar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Ligue a vários emuladores locais utilizando diferentes portas. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicione recursos anexados ao Acesso Rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* O Storage Explorer suporta agora o Soft Delete. Pode:
   * Configure uma política de eliminação suave clicando no nó blob containers para a sua conta de Armazenamento.
   * Ver bolhas apagadas suaves no Blob Editor selecionando "Blobs ativos e apagados" no dropdown ao lado da barra de navegação.
   * Bolhas macias e macias.

### <a name="fixes"></a>Correções
* A ação "Configurar as Definições CORS" já não está disponível nas contas de Armazenamento Premium porque as contas de Armazenamento Premium não suportam CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Existe agora uma propriedade de Assinatura de Acesso Partilhado para Serviços Anexos SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação "Definir Nível de Acesso Predefinido" já está disponível para contas de Armazenamento Blob e GPV2 que foram fixadas ao Acesso Rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Storage Explorer não mostraria as contas de Armazenamento Clássico. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao utilizar emuladores, como o Azure Storage Emulator ou o Azurite, terá de os fazer ouvir ligações nas portas predefinidas. Caso contrário, o Explorador de Armazenamento não será capaz de se ligar a eles.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se o fizer não o desbloqueie, por favor comente [sobre este assunto](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito [aqui.](https://github.com/Azure/azure-storage-node/issues/317)
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* O acesso aos recipientes $web utilizados por Websites Estáticos é agora suportado. Isto permite-lhe carregar e gerir facilmente ficheiros e pastas utilizados pelo seu website. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* A barra de aplicações no macOS foi reorganizada. As alterações incluem um menu de ficheiros, algumas alterações de chave de atalho e vários novos comandos no menu da aplicação. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* O ponto final da autoridade para a assinatura do Governo Azure EUA foi alterado para https://login.microsoftonline.us/
* Acessibilidade: Quando um leitor de ecrã está ativo, a navegação do teclado funciona agora com as tabelas utilizadas para exibir itens no lado direito. Pode utilizar as teclas de seta para navegar em linhas e colunas, insira para invocar ações padrão, a chave do menu de contexto para abrir o menu de contexto para um item, e Shift ou Control para multiselecção. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Correções
*  Em algumas máquinas, os processos infantis demoravam muito tempo a começar. Quando isso acontecesse, apareceria um erro de "processo infantil em tempo útil". O tempo atribuído para iniciar um processo infantil foi agora aumentado de 20 para 90 segundos. Se ainda for afetado por este problema, por favor comente a questão do GitHub ligado. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Ao utilizar um SAS que não tivesse permissões lidas, não era possível carregar uma bolha grande. A lógica do upload foi modificada para funcionar neste cenário. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Definir o nível de acesso público a um contentor removeria todas as políticas de acesso, e vice-versa. Agora, o nível de acesso público e as políticas de acesso são preservados ao definir qualquer um dos dois. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" foi truncado no diálogo Properties. Este problema foi corrigido. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* Faltava o prefixo "Microsoft Azure Storage Explorer" do diálogo Create New Directory. Este problema foi corrigido. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Acessibilidade: O diálogo da Entidade Add foi difícil de navegar quando se utiliza o VoiceOver. Foram introduzidas melhorias. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Acessibilidade: A cor de fundo do botão de colapso/expansão para o painel de Ações e Propriedades foi inconsistente com controlos de UI semelhantes no tema High Contrast Black. A cor foi alterada. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Acessibilidade: No tema High Contrast Black, o estilo de focagem para o botão 'X' no diálogo Propriedades não era visível. Este problema foi corrigido. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Acessibilidade: Os separadores de Ações e Propriedades faltavam vários valores de ária que resultaram numa experiência de leitor de ecrã subpar. Os valores da ária em falta foram agora adicionados. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Acessibilidade: Os nós de árvores colapsados no lado esquerdo não estavam a receber um valor expandido de falso. Este problema foi corrigido. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Problemas Conhecidos
* A desvinculação de um recurso ligado através do SAS URI, como um recipiente de bolhas, pode causar um erro que impeça que outros acessórios apareçam corretamente. Para contornar esta questão, basta refrescar o nó de grupo. Consulte [esta edição](https://github.com/Microsoft/AzureStorageExplorer/issues/537) para mais informações.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se o fizer não o desbloqueie, por favor comente [sobre este assunto](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito [aqui.](https://github.com/Azure/azure-storage-node/issues/317)
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* O Azure Stack não suporta as seguintes funcionalidades, e tentar usá-las enquanto trabalha com a Azure Stack pode resultar em erros inesperados:
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação suave
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* Se o Storage Explorer não carregar as subscrições apenas de um subconjunto dos seus inquilinos, então quaisquer subscrições carregadas com sucesso serão mostradas juntamente com uma mensagem de erro especificamente para os inquilinos que falharam. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* No Windows, quando uma atualização estiver disponível, pode agora optar por "Atualizar em Fecho". Quando esta opção for escolhida, o instalador para a atualização será executado depois de fechar o Storage Explorer. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* O Restore Snapshot foi adicionado ao menu de contexto do editor de partilha de ficheiros ao visualizar uma imagem de partilha de ficheiros. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* O botão Clear Queue está agora sempre ativado. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* O suporte para a inscrição no ADFS Azure Stack foi re-activado. É necessária a versão 1804 ou superior do Azure Stack. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Correções
* Se visualizasse instantâneos para uma partilha de ficheiros cujo nome era um prefixo de outra parte de ficheiro na mesma conta de armazenamento, então as imagens para a outra parte do ficheiro também seriam listadas. Esta questão foi corrigida. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Quando anexado via SAS, restaurar um ficheiro a partir de uma imagem de partilha de ficheiro resultaria num erro. Esta questão foi corrigida. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Ao visualizar instantâneos para uma bolha, a ação Promote Snapshot foi ativada quando a bolha de base e uma única imagem foram selecionadas. A ação só está ativada se for selecionada uma única imagem instantânea. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Se um único trabalho (como o download de uma bolha) fosse iniciado e mais tarde falhasse, não voltaria automaticamente a tentar até iniciar outro trabalho do mesmo tipo. Todos os postos de trabalho devem agora voltar a tentar, independentemente do número de postos de trabalho que tenha feito.
* Os editores abriram para os recipientes blob recém-criados em contas GPV2 e Blob Storage não tinham uma coluna Access Tier. Esta questão foi corrigida. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Uma coluna Access Tier às vezes não aparece quando uma conta de armazenamento ou recipiente de bolhas foi anexada via SAS. A coluna será sempre mostrada, mas com um valor vazio se não houver um conjunto de Nível de Acesso. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* A definição do nível de acesso de uma bolha de bloco recentemente carregada foi desativada. Esta questão foi corrigida. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Se o botão "Keep Tab Open" fosse invocado com teclado, perder-se-ia o foco do teclado. Agora, o foco passará para a teia que foi mantida aberta. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Para uma consulta no Construtor de Consultas, o VoiceOver não estava a dar uma descrição utilizável do operador atual. Agora é mais descritivo. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* As ligações de paginação para os vários editores não eram descritivas. Foram alterados para serem mais descritivos. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* No diálogo da Entidade Add, o VoiceOver não anunciava de que coluna fazia parte um elemento de entrada. O nome da coluna atual está agora incluído na descrição do elemento. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Os botões de rádio e as caixas de verificação não tinham uma borda visível quando focados. Esta questão foi corrigida. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao utilizar emuladores, como o Azure Storage Emulator ou o Azurite, terá de os fazer ouvir ligações nas portas predefinidas. Caso contrário, o Explorador de Armazenamento não será capaz de se ligar a eles.
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se o fizer não o desbloqueie, por favor comente [sobre este assunto](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito [aqui.](https://github.com/Azure/azure-storage-node/issues/317)
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* O Storage Explorer suporta agora a utilização do Azurite. Nota: a ligação a Azurite está codificada nos pontos finais de desenvolvimento predefinidos.
* O Storage Explorer suporta agora os Níveis de Acesso apenas para blob e contas de armazenamento GPV2. Saiba mais sobre os Níveis de Acesso [aqui.](./storage/blobs/storage-blob-storage-tiers.md)
* Já não é necessário um tempo de início para gerar um SAS.

### <a name="fixes"></a>Correções
* A recuperação de assinaturas para contas do Governo dos EUA foi quebrada. Esta questão foi corrigida. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* O prazo de validade das políticas de acesso não estava a ser corretamente guardado. Esta questão foi corrigida. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Ao gerar um URL SAS para um item num recipiente, o nome do item não estava a ser anexado ao URL. Esta questão foi corrigida. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Ao criar um SAS, os tempos de validade que estão no passado seriam, por vezes, o valor padrão. Isto deveu-se ao Storage Explorer utilizando o último tempo de início e validade usado como valores predefinidos. Agora, sempre que abre o diálogo SAS, é gerado um novo conjunto de valores predefinidos. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Ao copiar entre contas de armazenamento, é gerado um SAS de 24 horas. Se a cópia durasse mais de 24 horas, a cópia falharia. Aumentámos as SAS para durar 1 semana para reduzir a hipótese de uma cópia falhar devido a um SAS expirado. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Para algumas atividades clicar em "Cancelar" nem sempre funcionaria. Esta questão foi corrigida. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Para algumas atividades, a velocidade de transferência estava errada. Esta questão foi corrigida. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* A ortografia de "Anterior" no menu Ver estava errada. Está agora devidamente escrito. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* A página final do instalador do Windows tinha um botão "Seguinte". Foi alterado para um botão "Acabamento". [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* O foco do separador não era visível para botões em diálogos quando se utilizava o tema HC Black. Agora é visível. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* O invólucro de "Auto-Resolve" para ações no registo de atividades estava errado. Está agora correto. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Ao eliminar uma entidade de uma tabela, o diálogo que lhe pede confirmação apresentou um ícone de erro. O diálogo agora usa um ícone de aviso. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Problemas Conhecidos
* Se utilizar o VS para Mac e alguma vez tiver criado uma configuração AAD personalizada, poderá não conseguir iniciar sôs-in. Para contornar a questão, elimine o conteúdo de ~/. IdentityService/AadConfigurations. Se o fizer não o desbloqueie, por favor comente [sobre este assunto](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamentos inesperados ao utilizar a Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* O upload da pasta OneDrive não funciona por causa de um bug no NodeJS. O bug foi reparado, mas ainda não está integrado em Eletrão.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito [aqui.](https://github.com/Azure/azure-storage-node/issues/317)
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* Autenticação melhorada que permite ao Storage Explorer utilizar a mesma loja de conta que o Visual Studio 2017. Para utilizar esta funcionalidade, terá de voltar a iniciar sessão nas suas contas e reensuitar as suas subscrições filtradas.
* Para as contas Azure Stack apoiadas pela AAD, o Storage Explorer irá agora recuperar as subscrições do Azure Stack quando 'Target Azure Stack' estiver ativado. Já não precisa de criar um ambiente de login personalizado.
* Vários atalhos foram adicionados para permitir uma navegação mais rápida. Estes incluem toggling vários painéis e mover-se entre editores. Consulte o menu Ver para mais detalhes.
* O feedback do Storage Explorer vive agora no GitHub. Pode chegar à nossa página de problemas clicando no botão de Feedback na parte inferior esquerda ou indo para [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues) . Sinta-se livre para fazer sugestões, relatar questões, fazer perguntas ou deixar qualquer outra forma de feedback.
* Se encontrar problemas com o Certificado TLS/SSL e não conseguir encontrar o certificado de ofensa, pode agora lançar o Storage Explorer a partir da linha de comando com a `--ignore-certificate-errors` bandeira. Quando lançado com esta bandeira, o Storage Explorer ignorará os erros de certificado TLS/SSL.
* Existe agora uma opção 'Download' no menu de contexto para itens de blob e ficheiros.
* Melhor acessibilidade e suporte ao leitor de ecrã. Se confia nas funcionalidades de acessibilidade, consulte a nossa [documentação de acessibilidade](./vs-azure-tools-storage-explorer-accessibility.md) para mais informações.
* O Explorador de Armazenamento agora utiliza o Eletrão 1.8.3

### <a name="breaking-changes"></a>Alterações Interruptivas
* O Storage Explorer mudou para uma nova biblioteca de autenticação. Como parte da mudança para a biblioteca, terá de voltar a iniciar sessão nas suas contas e re-definir as suas subscrições filtradas
* O método usado para encriptar dados sensíveis mudou. Isto pode resultar na necessidade de recolocar alguns dos seus itens de Acesso Rápido e/ou alguns de vós terem de ser recolocados.

### <a name="fixes"></a>Correções
* Alguns utilizadores por trás de proxies teriam uploads ou downloads de blob de grupo interrompidos por uma mensagem de erro "Incapaz de resolver". Esta questão foi corrigida.
* Se o pedido de sing-in fosse necessário durante a utilização de um link direto, clicar na pedido de 'Iniciar-In' apareceria um diálogo em branco. Esta questão foi corrigida.
* No Linux, se o Storage Explorer não for capaz de ser lançado devido a uma falha no processo GPU, será agora informado da falha, é-lhe dito para utilizar o interruptor "--desativar-gpu" e o Storage Explorer reiniciará automaticamente com o interruptor ativado.
* As políticas de acesso inválidas eram difíceis de identificar no diálogo das Políticas de Acesso. Os IDs de política de acesso inválido estão agora delineados a vermelho para maior visibilidade.
* O log de atividade teria, por vezes, grandes áreas de espaço branco entre as diferentes partes de uma atividade. Esta questão foi corrigida.
* No editor de consulta de tabela, se deixasse uma cláusula de tempo num estado inválido e tentasse modificar outra cláusula, o editor congelaria. O editor irá agora restaurar a cláusula de datatamp ao seu último estado válido quando for detetada uma alteração noutra cláusula.
* Se fez uma pausa enquanto digitava a sua consulta de pesquisa na vista da árvore, a procura começaria e o foco seria roubado da caixa de texto. Agora, deve começar a procurar explicitamente premindo a tecla 'Enter' ou clicando no botão de pesquisa inicial.
* O comando 'Obter Assinatura de Acesso Partilhado' seria por vezes desativado quando clicava num ficheiro numa Partilha de Ficheiros. Esta questão foi corrigida.
* Se o nó da árvore de recurso com foco foi filtrado durante a pesquisa, não foi possível abaixar a árvore de recursos e utilizar as teclas de seta para navegar na árvore de recursos. Agora, se o nó de árvore de recurso focado estiver escondido, o primeiro nó na árvore de recursos será automaticamente focado.
* Um separador extra às vezes seria visível na barra de ferramentas do editor. Esta questão foi corrigida.
* A caixa de texto de migalhas de pão às vezes transbordava. Esta questão foi corrigida.
* Os editores blob e file share às vezes atualizavam constantemente quando enviavam muitos ficheiros ao mesmo tempo. Esta questão foi corrigida.
* A função 'Estatísticas de Pasta' não tinha qualquer propósito na visão de Gestão de Snapshots de Partilha de Ficheiros. Foi agora desativado.
* No Linux, o menu de ficheiros não apareceu. Esta questão foi corrigida.
* Ao carregar uma pasta para uma Partilha de Ficheiros, por padrão, apenas o conteúdo da pasta foi carregado. Agora, o comportamento predefinido é enviar o conteúdo da pasta para uma pasta correspondente na Partilha de Ficheiros.
* A encomenda de botões em vários diálogos tinha sido invertida. Esta questão foi corrigida.
* Várias correções relacionadas com a segurança.

### <a name="known-issues"></a>Problemas Conhecidos
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para desafocar o foco, pode refrescar tudo.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* Um problema provocou a troca entre as vistas instantâneas para exibir os itens incorretamente. Esta questão foi corrigida.

### <a name="known-issues"></a>Problemas Conhecidos
* O Storage Explorer não suporta contas ADFS.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito [aqui.](https://github.com/Azure/azure-storage-node/issues/317)
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* O painel de definições de conta pode mostrar que é necessário reentrar em credenciais para filtrar subscrições.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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

* Suporte para fotos de partilha de ficheiros:
    * Crie e gere fotos para as suas Partilhas de Ficheiros.
    * Altere facilmente as vistas entre as imagens das suas Partilhas de Ficheiros à medida que explora.
    * Restaurar versões anteriores dos seus ficheiros.
* Suporte de pré-visualização para Azure Data Lake Store:
    * Conecte-se aos seus recursos ADLS em várias contas.
    * Conecte-se e partilhe recursos ADLS usando URIs ADL.
    * Efetue operações básicas de ficheiro/pasta de forma recursiva.
    * Pin as pastas individuais para acesso rápido.
    * Apresentar estatísticas de pastas.

### <a name="fixes"></a>Correções
* Melhorias no desempenho da startup.
* Várias correções de bugs.

### <a name="known-issues"></a>Problemas Conhecidos
* O Storage Explorer não suporta contas ADFS.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* O painel de definições de conta pode mostrar que é necessário reentrar em credenciais para filtrar subscrições.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* A janela do Explorador de Armazenamento existente será reutilizada quando:
    * Abertura de links diretos gerados no Storage Explorer.
    * Abertura do Explorador de Armazenamento do portal.
    * Abertura do Explorador de Armazenamento a partir da extensão do Código VS de armazenamento Azure (em breve).
* Capacidade adicional de abrir uma nova janela do Explorador de Armazenamento a partir de dentro do Storage Explorer.
    * Para o Windows, existe uma opção "Nova Janela" no Menu de Ficheiros e no menu de contexto da barra de tarefas.
    * Para o Mac, existe uma opção 'New Window' no Menu de Aplicações.

### <a name="fixes"></a>Correções
* Corrigi um problema de segurança. Por favor, atualize para 0.9.4 o mais rápido possível.
* As atividades antigas não estavam a ser devidamente limpas. Isto afetou o desempenho de trabalhos de longa data. Estão agora a ser limpos corretamente.
* Ações envolvendo um grande número de ficheiros e diretórios ocasionalmente fariam o Storage Explorer congelar. Os pedidos ao Azure para ações de ficheiros são agora acelerados para limitar a utilização de recursos do sistema.

### <a name="known-issues"></a>Problemas Conhecidos
* O Storage Explorer não suporta contas ADFS.
* As teclas de atalho para "View Explorer" e "View Account Management" devem ser Ctrl/Cmd+Shift+E e Ctrl/Cmd+Shift+A, respectivamente.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* O painel de definições de conta pode mostrar que é necessário reentrar em credenciais para filtrar subscrições.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* Foram possíveis alterações inesperadas de dados na edição dos valores Edm.DateTime para entidades de tabela, dependendo do fuso horário local. O editor usa agora uma caixa de texto simples, dando um controlo preciso e consistente sobre os valores Edm.DateTime.
* O upload/download de um grupo de bolhas quando ligados com o nome e a tecla não começaria. Esta questão foi corrigida.
* Anteriormente, o Storage Explorer só lhe pediria para reautorizá-lo uma conta velha se uma ou mais subscrições da conta fossem selecionadas. Agora o Storage Explorer irá pedir-lhe mesmo que a conta esteja totalmente filtrada.
* O domínio dos pontos finais para o Governo dos EUA estava errado. Foi arranjado.
* O botão de aplicação no painel 'Contas' gestão foi por vezes difícil de clicar. Isto não devia acontecer mais.

### <a name="new"></a>Novo
* Suporte de pré-visualização para Azure Cosmos DB:
    * [Documentação Online](./cosmos-db/storage-explorer.md)
    * Criar bases de dados e coleções
    * Manipular dados
    * Consultar, criar ou apagar documentos
    * Atualizar procedimentos armazenados, funções definidas pelo utilizador ou gatilhos
    * Utilize cadeias de ligação para ligar e gerir as suas bases de dados
* Melhorou o desempenho do upload/download de muitas pequenas bolhas.
* Adicionou uma ação "Retry All" se houver falhas num grupo de upload de blob ou no grupo de descarregamento blob.
* O Storage Explorer irá agora interromper a iteração durante o upload/download do blob se detetar que a sua ligação de rede foi perdida. Em seguida, pode retomar a iteração uma vez que a ligação à rede tenha sido restabelecida.
* Adicionou a capacidade de "Fechar Todos", "Fechar outros" e "Fechar" os separadores através do menu de contexto.
* O Storage Explorer utiliza agora diálogos nativos e menus de contexto nativo.
* O Storage Explorer está agora mais acessível. As melhorias incluem:
    * Suporte melhorado do leitor de ecrã, para NVDA no Windows e para VoiceOver no Mac
    * Tema de alto contraste melhorado
    * Correções de teclado e foco de teclado

### <a name="fixes"></a>Correções
* Se tentasse abrir ou descarregar uma bolha com um nome de ficheiro Inválido do Windows, a operação falharia. O Storage Explorer irá agora detetar se um nome de bolha é inválido e perguntar se pretende codificá-lo ou saltar a bolha. O Storage Explorer também detetará se um nome de ficheiro parece estar codificado e perguntar-lhe-á se pretende descodificá-lo antes de carregar.
* Durante o upload do blob, o editor do recipiente de bolhas alvo às vezes não se refresca corretamente. Esta questão foi corrigida.
* O suporte para várias formas de cordas de conexão e URIs SAS regrediu. Abordámos todas as questões conhecidas, mas por favor envie feedback se encontrar mais problemas.
* A notificação da atualização foi interrompida para alguns utilizadores em 0.9.0. Este problema foi corrigido, e para os afetados pelo bug, você pode descarregar manualmente a versão mais recente do Storage Explorer [aqui](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Problemas Conhecidos
* O Storage Explorer não suporta contas ADFS.
* As teclas de atalho para "View Explorer" e "View Account Management" devem ser Ctrl/Cmd+Shift+E e Ctrl/Cmd+Shift+A, respectivamente.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* O painel de definições de conta pode mostrar que é necessário reentrar em credenciais para filtrar subscrições.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
    * Consultar, criar ou apagar documentos
    * Atualizar procedimentos armazenados, funções definidas pelo utilizador ou gatilhos
    * Utilize cadeias de ligação para ligar e gerir as suas bases de dados
* Melhorou o desempenho do upload/download de muitas pequenas bolhas.
* Adicionou uma ação "Retry All" se houver falhas num grupo de upload de blob ou no grupo de descarregamento blob.
* O Storage Explorer irá agora interromper a iteração durante o upload/download do blob se detetar que a sua ligação de rede foi perdida. Em seguida, pode retomar a iteração uma vez que a ligação à rede tenha sido restabelecida.
* Adicionou a capacidade de "Fechar Todos", "Fechar outros" e "Fechar" os separadores através do menu de contexto.
* O Storage Explorer utiliza agora diálogos nativos e menus de contexto nativo.
* O Storage Explorer está agora mais acessível. As melhorias incluem:
    * Suporte melhorado do leitor de ecrã, para NVDA no Windows e para VoiceOver no Mac
    * Tema de alto contraste melhorado
    * Correções de teclado e foco de teclado

### <a name="fixes"></a>Correções
* Se tentasse abrir ou descarregar uma bolha com um nome de ficheiro Inválido do Windows, a operação falharia. O Storage Explorer irá agora detetar se um nome de bolha é inválido e perguntar se pretende codificá-lo ou saltar a bolha. O Storage Explorer também detetará se um nome de ficheiro parece estar codificado e perguntar-lhe-á se pretende descodificá-lo antes de carregar.
* Durante o upload do blob, o editor do recipiente de bolhas alvo às vezes não se refresca corretamente. Esta questão foi corrigida.
* O suporte para várias formas de cordas de conexão e URIs SAS regrediu. Abordámos todas as questões conhecidas, mas por favor envie feedback se encontrar mais problemas.
* A notificação da atualização foi interrompida para alguns utilizadores em 0.9.0. Este problema foi corrigido, e para aqueles afetados pelo bug, você pode manualmente baixar a versão mais recente do Storage Explorer [aqui](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Problemas Conhecidos
* O Storage Explorer não suporta contas ADFS.
* As teclas de atalho para "View Explorer" e "View Account Management" devem ser Ctrl/Cmd+Shift+E e Ctrl/Cmd+Shift+A, respectivamente.
* Ao direcionar o Azure Stack, o upload de certos ficheiros como bolhas de apêndice pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Isto porque estamos a usar o trabalho de filtro de cancelamento em torno descrito aqui.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* O painel de definições de conta pode mostrar que é necessário reentrar em credenciais para filtrar subscrições.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* A concha electron utilizada pelo Storage Explorer tem problemas com alguma aceleração de hardware gpu (unidade de processamento de gráficos). Se o Storage Explorer apresentar uma janela principal em branco (vazia), pode tentar lançar o Storage Explorer a partir da linha de comando e desativar a aceleração da GPU adicionando o `--disable-gpu` interruptor:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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
* Experiência de inscrição de Azure Stack melhorada
* Melhorou o desempenho do upload/download de muitos pequenos ficheiros ao mesmo tempo


### <a name="fixes"></a>Correções
* Para alguns tipos de bolhas, optar por "substituir" durante um conflito de upload por vezes resultaria no reinício do upload.
* Na versão 0.8.15, os uploads às vezes paravam a 99%.
* Ao enviar ficheiros para uma partilha de ficheiros, se optar por fazer o upload para um diretório que ainda não existia, o seu upload falharia.
* O Storage Explorer estava a gerar incorretamente selos de tempo para assinaturas de acesso partilhado e consultas de mesa.


### <a name="known-issues"></a>Problemas Conhecidos
* A utilização de um nome e de uma ligação chave não funciona atualmente. Será corrigido no próximo lançamento. Até lá, pode utilizar o anexo com o nome e a chave.
* Se tentar abrir um ficheiro com um nome de ficheiro inválido do Windows, o download resultará num erro não encontrado num ficheiro.
* Depois de clicar em "Cancelar" numa tarefa, pode demorar algum tempo para que essa tarefa seja cancelada. Esta é uma limitação da biblioteca do nó de armazenamento Azure.
* Depois de completar um upload de bolhas, o separador que iniciou o upload é atualizado. Esta é uma mudança de comportamento anterior, e também fará com que você seja levado de volta para a raiz do recipiente em que você está.
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça essa decisão.
* O painel de definições de conta pode mostrar que é necessário reentrar em credenciais para filtrar subscrições.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* Para os utilizadores em Ubuntu 14.04, terá de garantir que o GCC está atualizado - isto pode ser feito executando os seguintes comandos e, em seguida, reiniciando a sua máquina:

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

* Versão de Eletrões atualizada para 1.7.2 para aproveitar várias atualizações críticas de segurança
* Agora pode aceder rapidamente ao guia de resolução de problemas on-line a partir do menu de ajuda
* [Guia][2] de resolução de problemas do explorador de armazenamento
* [Instruções][3] de ligação a uma subscrição do Azure Stack

### <a name="known-issues"></a>Problemas Conhecidos

* Os botões do diálogo de confirmação da pasta de eliminação não se registam com os cliques do rato no Linux. trabalhar ao redor é usar a chave Enter
* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça a decisão.
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo pode causar erros
* O painel de definições de conta pode mostrar que é necessário reentrar em credenciais para filtrar subscrições
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* Ubuntu 14.04 instalar necessidades de versão gCC atualizada ou atualizada – os passos para a atualização estão abaixo:

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
* [Instruções][3] de ligação a uma subscrição do Azure Stack

#### <a name="fixes"></a>Correções

* Fixo: O upload de ficheiros tinha uma alta probabilidade de causar um erro de memória
* Fixo: Já pode iniciar sôs-in com PIN/Smartcard
* Fixed: Open in Portal agora trabalha com Azure China 21Vianet, Azure Germany, Azure US Government, e Azure Stack
* Fixo: Ao carregar uma pasta para um recipiente de bolhas, um erro de "operação ilegal" ocorreria por vezes
* Fixo: Selecione tudo foi desativado enquanto geria instantâneos
* Fixo: Os metadados da bolha de base podem ser substituídos após visualização das propriedades dos seus instantâneos

#### <a name="known-issues"></a>Problemas Conhecidos

* Se escolher o certificado PIN/Smartcard errado, terá de reiniciar para que o Explorador de Armazenamento esqueça a decisão.
* Enquanto zoomed dentro ou fora, o nível de zoom pode momentaneamente redefinir para o nível padrão
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo pode causar erros
* O painel de definições de conta pode mostrar que é necessário reentrar em credenciais para filtrar subscrições
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* Ubuntu 14.04 instalar necessidades de versão gCC atualizada ou atualizada – os passos para a atualização estão abaixo:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Versão 0.8.12 e 0.8.11 e 0.8.10
04/07/2017

#### <a name="new"></a>Novo

* O Storage Explorer fechará automaticamente quando instalar uma atualização a partir da notificação de atualização
* O acesso rápido no local proporciona uma experiência melhorada para trabalhar com os seus recursos frequentemente acedidos
* No editor do Blob Container, você pode agora ver a que máquina virtual uma bolha alugada pertence a
* Agora pode colapsar o painel do lado esquerdo
* Discovery agora corre ao mesmo tempo que o download
* Utilize estatísticas nos editores blob container, File Share e Table para ver o tamanho do seu recurso ou seleção
* Agora pode iniciar sedução nas contas Azure Ative Directory (AAD) baseadas em Azure Stack.
* Agora pode carregar ficheiros de arquivos sobre 32MB para contas de armazenamento Premium
* Melhor suporte à acessibilidade
* Agora pode adicionar certificados X.509 TSL/SSL codificados de confiança, indo para Editar - &gt; Certificados SSL - &gt; Certificados de Importação

#### <a name="fixes"></a>Correções

* Fixo: depois de refrescar as credenciais de uma conta, a vista da árvore às vezes não iria atualizar automaticamente
* Fixo: gerar um SAS para filas e tabelas de emuladores resultaria num URL inválido
* Fixo: as contas de armazenamento premium podem agora ser expandidas enquanto um proxy está habilitado
* Fixo: o botão de aplicação na página de gestão de contas não funcionaria se tivesse 1 ou 0 contas selecionadas
* Fixo: as bolhas de carregamento que requerem resoluções de conflitos podem falhar - fixadas em 0.8.11
* Fixo: o feedback de envio foi quebrado em 0.8.11 - fixado em 0.8.12

#### <a name="known-issues"></a>Problemas Conhecidos

* Depois de atualizar para 0.8.10, você precisará atualizar todas as suas credenciais.
* Enquanto ampliado para dentro ou para fora, o nível de zoom pode reiniciar momentaneamente para o nível padrão.
* Ter mais de 3 grupos de bolhas ou ficheiros a carregar ao mesmo tempo pode causar erros.
* O painel de definições de conta pode mostrar que é necessário reentrar em credenciais para filtrar subscrições.
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome.
* Embora a Azure Stack não suporte atualmente ações de ficheiros, um nó de Partilhas de Ficheiros ainda aparece numa conta de armazenamento Azure Stack anexada.
* Ubuntu 14.04 instalar necessidades de versão gCC atualizada ou atualizada – os passos para a atualização estão abaixo:

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
* Hotfix: utilizar um portal gerado SAS URI para anexar uma conta de armazenamento resultaria num erro.
* Agora pode criar, gerir e promover instantâneos blob.
* Pode agora inscrever-se nas contas do Azure China 21Vianet, Azure Germany e Azure US Government.
* Agora pode alterar o nível de zoom. Utilize as opções no menu 'Ver' para fazer zoom, zoom out e reset Zoom.
* Os caracteres Unicode são agora suportados em metadados do utilizador para bolhas e ficheiros.
* Melhorias de acessibilidade.
* As notas de lançamento da próxima versão podem ser visualizadas a partir da notificação da atualização. Também pode ver as atuais notas de lançamento do menu Ajuda.

#### <a name="fixes"></a>Correções

* Fixo: o número da versão é agora apresentado corretamente no Painel de Controlo no Windows
* Fixo: a procura já não se limita a 50.000 nosdes
* Fixo: faça o upload para uma partilha de ficheiros girado para sempre se o diretório de destino já não existisse
* Fixo: melhor estabilidade para carregamentos e transferências longos

#### <a name="known-issues"></a>Problemas Conhecidos

* Enquanto ampliado para dentro ou para fora, o nível de zoom pode reiniciar momentaneamente para o nível padrão.
* O Acesso Rápido funciona apenas com itens baseados em subscrição. Os recursos locais ou recursos anexados através de chave ou sinal SAS não são suportados nesta versão.
* Pode levar alguns segundos de Acesso Rápido para navegar para o recurso alvo, dependendo de quantos recursos tem.
* Ter mais de 3 grupos de bolhas ou ficheiros a carregar ao mesmo tempo pode causar erros.

12/16/2016
### <a name="version-087"></a>Versão 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Novo

* Pode escolher como resolver conflitos no início de uma sessão de atualização, descarregamento ou cópia na janela Atividades
* Pairar sobre um separador para ver o caminho completo do recurso de armazenamento
* Ao clicar num separador, sincroniza-se com a sua localização no painel de navegação do lado esquerdo

#### <a name="fixes"></a>Correções

* Fixed: Storage Explorer é agora uma aplicação fidedigna no Mac
* Fixo: Ubuntu 14.04 volta a ser apoiado
* Fixo: Às vezes a conta de adição UI pisca ao carregar subscrições
* Fixo: Às vezes nem todos os recursos de armazenamento foram listados no painel de navegação do lado esquerdo
* Fixo: O painel de ação às vezes apresentava ações vazias
* Fixo: O tamanho da janela da última sessão fechada é agora mantido
* Fixo: Pode abrir vários separadores para o mesmo recurso utilizando o menu de contexto

#### <a name="known-issues"></a>Problemas Conhecidos

* O Acesso Rápido funciona apenas com itens baseados em subscrição. Os recursos locais ou recursos anexados através de chave ou sinal SAS não são suportados nesta versão
* Pode levar alguns segundos de Acesso Rápido para navegar para o recurso alvo, dependendo de quantos recursos você tem
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo pode causar erros
* As pegas de pesquisa que procuram em cerca de 50.000 nós - depois disso, o desempenho pode ser impactado ou pode causar exceção não manipulada
* Pela primeira vez, utilizando o Storage Explorer no macOS, poderá ver várias solicitações a pedir permissão do utilizador para aceder ao chaveiro. Sugerimos que selecione Sempre Permitir para que o pedido não apareça novamente

11/18/2016
### <a name="version-086"></a>Versão 0.8.6

#### <a name="new"></a>Novo

* Agora pode fixar os serviços mais utilizados no Acesso Rápido para facilitar a navegação
* Agora pode abrir vários editores em separadores diferentes. Clique único para abrir um separador temporário; clique duplo para abrir um separador permanente. Também pode clicar no separador temporário para torná-lo um separador permanente
* Fizemos melhorias notáveis de desempenho e estabilidade para uploads e downloads, especialmente para grandes ficheiros em máquinas rápidas
* Pastas "virtuais" vazias podem agora ser criadas em recipientes blob
* Reinstauramos a procura com a nossa nova pesquisa de substring melhorada, pelo que tem agora duas opções para pesquisar:
    * Pesquisa global - basta inserir um termo de pesquisa na caixa de texto de pesquisa
    * Pesquisa scoped - clique no ícone de lupa ao lado de um nó, em seguida, adicione um termo de pesquisa ao fim do caminho, ou clique à direita e selecione "Search from Here"
* Adicionámos vários temas: Luz (padrão), Preto escuro, alto contraste e branco de alto contraste. Ir para Editar - &gt; Temas para alterar a sua preferência temática
* Pode modificar propriedades blob e ficheiros
* Agora apoiamos mensagens de fila codificadas (base64) e descodificadas
* Em Linux, um sistema operativo de 64 bits é agora necessário. Para esta versão apoiamos apenas Ubuntu 16.04.1 LTS de 64 bits
* Atualizámos o nosso logótipo!

#### <a name="fixes"></a>Correções

* Fixo: Problemas de congelamento do ecrã
* Fixo: Segurança reforçada
* Fixo: Por vezes, podem aparecer contas em anexo duplicadas
* Fixo: Uma bolha com um tipo de conteúdo indefinido pode gerar uma exceção
* Fixo: A abertura do Painel de Consulta numa mesa vazia não foi possível
* Fixo: Varia bugs em Pesquisa
* Fixo: Aumentou o número de recursos carregados de 50 para 100 ao clicar em "Carregar Mais"
* Fixo: Na primeira execução, se uma conta for assinada, agora selecionamos todas as subscrições para essa conta por padrão

#### <a name="known-issues"></a>Problemas Conhecidos

* Esta versão do Explorador de Armazenamento não funciona em Ubuntu 14.04
* Para abrir vários separadores para o mesmo recurso, não clique continuamente no mesmo recurso. Clique em outro recurso e, em seguida, volte e clique no recurso original para abri-lo novamente em outro separador
* O Acesso Rápido funciona apenas com itens baseados em subscrição. Os recursos locais ou recursos anexados através de chave ou sinal SAS não são suportados nesta versão
* Pode levar alguns segundos de Acesso Rápido para navegar para o recurso alvo, dependendo de quantos recursos você tem
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo pode causar erros
* As pegas de pesquisa que procuram em cerca de 50.000 nós - depois disso, o desempenho pode ser impactado ou pode causar exceção não manipulada

10/03/2016
### <a name="version-085"></a>Versão 0.8.5

#### <a name="new"></a>Novo

* Pode agora usar as chaves SAS geradas pelo Portal para anexar contas e recursos de armazenamento

#### <a name="fixes"></a>Correções

* Fixo: condição de corrida durante a busca, por vezes fez com que os nóns se tornassem não expansíveis
* Fixo: "Use HTTP" não funciona ao ligar-se às Contas de Armazenamento com nome de conta e chave
* Fixo: As teclas SAS (especialmente geradas pelo Portal) devolvem um erro de "corte de fuga"
* Fixo: questões de importação de quadros
    * Às vezes a chave de partição e a chave da linha foram invertidas
    * Incapaz de ler chaves de partição "nulas"

#### <a name="known-issues"></a>Problemas Conhecidos

* As pegas de pesquisa que procuram em cerca de 50.000 nóns - depois disso, o desempenho pode ser impactado
* A azure Stack não suporta ficheiros atualmente, por isso tentar expandir ficheiros mostrará um erro

09/12/2016
### <a name="version-084"></a>Versão 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Novo

* Gere links diretos para contas de armazenamento, contentores, filas, tabelas ou partilhas de ficheiros para partilha e fácil acesso aos seus recursos - suporte ao Windows e Mac OS
* Procure os seus recipientes blob, tabelas, filas, partilhas de ficheiros ou contas de armazenamento da caixa de pesquisa
* Agora pode agrupar cláusulas no construtor de consultas de mesa
* Rebatize e copie/pasta recipientes blob, partilhas de ficheiros, tabelas, bolhas, pastas blob, ficheiros e diretórios dentro de contas e contentores anexos ao SAS
* Renomear e copiar contentores blob e ações de ficheiros agora preserva propriedades e metadados

#### <a name="fixes"></a>Correções

* Fixo: não pode editar entidades de mesa se contiverem propriedades booleanas ou binárias

#### <a name="known-issues"></a>Problemas Conhecidos

* As pegas de pesquisa que procuram em cerca de 50.000 nóns - depois disso, o desempenho pode ser impactado

08/03/2016
### <a name="version-083"></a>Versão 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Novo

* Rebatize contentores, tabelas, ações de ficheiros
* Experiência de construtor de consultas melhorada
* Capacidade de poupar e carregar consultas
* Links diretos para contas de armazenamento ou contentores, filas, tabelas ou partilhas de ficheiros para partilha e fácil acesso aos seus recursos (suporte apenas para windows - macOS em breve!)
* Capacidade de gerir e configurar as regras do CORS

#### <a name="fixes"></a>Correções

* Fixo: Contas microsoft requerem reautensão a cada 8-12 horas

#### <a name="known-issues"></a>Problemas Conhecidos

* Às vezes a UI pode parecer congelada - maximizar a janela ajuda a resolver este problema
* instalação do macOS pode exigir permissões elevadas
* Painel de definições de conta pode mostrar que precisa de reentrar em credenciais para filtrar subscrições
* Renomear ações de ficheiros, recipientes blob e tabelas não preserva metadados ou outras propriedades no contentor, tais como quota de partilha de ficheiros, nível de acesso público ou políticas de acesso
* A renomeação de bolhas (individualmente ou dentro de um recipiente de bolhas renomeado) não preserva instantâneos. Todas as outras propriedades e metadados para bolhas, ficheiros e entidades são preservados durante um renome
* Copiar ou renomear recursos não funciona dentro de contas anexas à SAS

07/07/2016
### <a name="version-082"></a>Versão 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Novo

* As Contas de Armazenamento são agrupadas por subscrições; armazenamento de desenvolvimento e recursos anexados através de chave ou SAS são mostrados sob o nó (Local e Anexo)
* Inscreva-se nas contas no painel "Definições de Conta Azure"
* Configurar configurações de procuração para ativar e gerir o s-in
* Criar e quebrar arrendamentos blob
* Abra recipientes blob, filas, mesas e ficheiros com um único clique

#### <a name="fixes"></a>Correções

* Fixo: as mensagens de fila inseridas com bibliotecas .NET ou Java não estão devidamente descodificados a partir da base64
* Fixo: $metrics tabelas não são apresentadas para contas blob armazenamento
* Fixo: nó de mesas não funciona para armazenamento local (Desenvolvimento)

#### <a name="known-issues"></a>Problemas Conhecidos

* instalação do macOS pode exigir permissões elevadas

06/15/2016
### <a name="version-080"></a>Versão 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Novo

* Suporte de partilha de ficheiros: visualização, upload, download, cópia de ficheiros e diretórios, URIs SAS (criar e ligar)
* Melhor experiência do utilizador para ligar ao Armazenamento com URIs SAS ou chaves de conta
* Resultados da consulta da tabela de exportação
* Reordenação e personalização da coluna de tabelas
* Visualização de recipientes $logs blob e mesas de $metrics para contas de armazenamento com métricas habilitadas
* Melhor comportamento de exportação e importação, agora inclui tipo de valor imobiliário

#### <a name="fixes"></a>Correções

* Fixo: carregar ou descarregar grandes bolhas pode resultar em uploads/downloads incompletos
* Fixo: editar, adicionar ou importar uma entidade com um valor de corda numérica ("1") irá convertê-lo em dobro
* Fixo: Incapaz de expandir o nó de mesa no ambiente de desenvolvimento local

#### <a name="known-issues"></a>Problemas Conhecidos

* $metrics tabelas não são visíveis para contas blob storage
* As mensagens de fila adicionadas programáticamente não podem ser exibidas corretamente se as mensagens estiverem codificadas utilizando a codificação base64

05/17/2016
### <a name="version-07201605090"></a>Versão 0.7.20160509.0

#### <a name="new"></a>Novo

* Melhor manipulação de erros para falhas de aplicações

#### <a name="fixes"></a>Correções

* Bug fixo onde as mensagens InfoBar às vezes não aparecem quando as credenciais de inscrição eram necessárias

#### <a name="known-issues"></a>Problemas Conhecidos

* Tabelas: Adicionar, editar ou importar uma entidade que tenha um imóvel com um valor ambíguo numérico, como "1" ou "1.0", e o utilizador tenta enviá-lo como um `Edm.String` , o valor voltará através da API cliente como um Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Versão 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Novo

* Suporte de tabela: visualização, consulta, exportação, importação e operações CRUD para entidades
* Suporte à fila: visualização, adição, desadição de mensagens
* Gerar URIs SAS para contas de armazenamento
* Ligação às Contas de Armazenamento com URIs SAS
* Atualizar notificações para futuras atualizações para o Explorador de Armazenamento
* Olhar e sensação atualizados

#### <a name="fixes"></a>Correções

* Melhorias de desempenho e fiabilidade

### <a name="known-issues-amp-mitigations"></a>Mitigações de Questões &amp; Conhecidas

* O download de grandes ficheiros blob não funciona corretamente - recomendamos a utilização do AzCopy enquanto abordamos este problema
* As credenciais de conta não serão recuperadas nem em cache se a pasta da casa não puder ser encontrada ou não puder ser escrita para
* Se estivermos a adicionar, editar ou importar uma entidade que tenha um imóvel com um valor ambíguo numérico, como "1" ou "1.0", e o utilizador tentar enviá-lo como um `Edm.String` , o valor voltará através da API cliente como um Edm.Double
* Ao importar ficheiros CSV com registos multiline, os dados podem ser cortados ou mexidos

02/03/2016

### <a name="version-07201601291"></a>Versão 0.7.20160129.1

#### <a name="fixes"></a>Correções

* Melhor desempenho geral ao carregar, descarregar e copiar bolhas

01/14/2016

### <a name="version-07201601050"></a>Versão 0.7.20160105.0

#### <a name="new"></a>Novo

* Suporte Linux (funcionalidades de paridade para OSX)
* Adicione recipientes blob com a chave Assinaturas de Acesso Partilhada (SAS)
* Adicionar contas de armazenamento para Azure China 21Vianet
* Adicionar Contas de Armazenamento com pontos finais personalizados
* Abra e veja o texto de conteúdo e as bolhas de imagem
* Ver e editar propriedades blob e metadados

#### <a name="fixes"></a>Correções

* Fixo: carregar ou descarregar um grande número de blobs (500+) pode, por vezes, fazer com que a aplicação tenha um ecrã branco
* Fixo: ao definir o nível de acesso público do recipiente blob, o novo valor não é atualizado até que reescreva o foco no recipiente. Além disso, o diálogo sempre falha em "Sem acesso público", e não o valor atual real.
* Melhor teclado/acessibilidade geral e suporte à UI
* O texto da história das migalhas de pão envolve quando é longo com espaço branco
* O diálogo SAS suporta a validação de entrada
* O armazenamento local continua disponível mesmo que as credenciais dos utilizadores tenham expirado
* Quando um recipiente de bolha aberto é apagado, o explorador de bolhas do lado direito é fechado

#### <a name="known-issues"></a>Problemas Conhecidos

* A instalação do Linux necessita de uma versão gcc atualizada ou atualizada – os passos para atualizar estão abaixo:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Versão 0.7.20151116.0

#### <a name="new"></a>Novo

* versões macOS e Windows
* Iniciar sposição para ver as suas Contas de Armazenamento – utilize a sua Conta Org, Conta Microsoft, 2FA, etc.
* Armazenamento de desenvolvimento local (use emulador de armazenamento, apenas windows)
* Gestor de Recursos Azure e suporte clássico de recursos
* Criar e apagar bolhas, filas ou mesas
* Procure bolhas, filas ou mesas específicas
* Explore o conteúdo dos recipientes blob
* Ver e navegar através de diretórios
* Carregar, descarregar e apagar bolhas e pastas
* Ver e editar propriedades blob e metadados
* Gerar teclas SAS
* Gerir e criar Políticas de Acesso Armazenadas (SAP)
* Procurar bolhas por prefixo
* Arraste os ficheiros 'n drop para carregar ou transferir

#### <a name="known-issues"></a>Problemas Conhecidos

* Ao definir o nível de acesso público do recipiente blob, o novo valor não é atualizado até que re-ajuste o foco no recipiente
* Quando abre o diálogo para definir o nível de acesso público, mostra sempre "Não há acesso público" como padrão, e não o valor atual real
* Não é possível renomear bolhas descarregadas
* As entradas de registo de atividade por vezes ficam "presas" num estado em curso quando ocorre um erro, e o erro não é exibido
* Às vezes bate ou torna-se completamente branco ao tentar carregar ou descarregar um grande número de bolhas
* Às vezes, cancelar uma operação de cópia não funciona
* Durante a criação de um recipiente (blob/fila/tabela), se inserir um nome inválido e proceder à criação de outro sob um tipo de recipiente diferente, não pode concentrar-se no novo tipo
* Não é possível criar uma nova pasta ou mudar de nomeação




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md