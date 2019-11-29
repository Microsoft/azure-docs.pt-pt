---
title: Notas de versão do Gerenciador de Armazenamento do Microsoft Azure
description: Notas de versão para Gerenciador de Armazenamento do Microsoft Azure
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
ms.openlocfilehash: 1adfb59843150ffaa6ed76411d07d8ec6cf6a44b
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555274"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Notas de versão do Gerenciador de Armazenamento do Microsoft Azure

Este artigo contém as notas de versão mais recentes para Gerenciador de Armazenamento do Azure, bem como notas de versão para versões anteriores. 

[Gerenciador de armazenamento do Microsoft Azure](./vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que permite que você trabalhe facilmente com os dados do armazenamento do Azure no Windows, no MacOS e no Linux.

Para baixar versões anteriores do Gerenciador de Armazenamento, você pode visitar a [página de lançamentos](https://github.com/microsoft/AzureStorageExplorer/releases) do repositório github.

## <a name="version-1110"></a>1\.11.0 da versão
11/4/2019

### <a name="new"></a>Novo
* Operações para BLOBs, ADLS Gen2 e Managed Disks usam o AzCopy integrado. Mais especificamente, as seguintes operações são feitas usando AzCopy:
   * Blobs
      * Abrir para edição + carregamento
      * Carregar, incluindo arrastar & soltar
      * Transferir
      * Copiar & colar #1249
      * Eliminar
   * Blobs de ADLS Gen2
      * Carregar, incluindo arrastar & soltar
      * Transferir
      * Copiar & colar
      * Excluir, incluindo exclusão de pasta
   * Managed Disks
      * Carregar
      * Transferir
      * Copiar & colar

   Além disso, vários recursos solicitados com frequência foram adicionados à experiência de AzCopy integrada:
   * Resoluções de conflitos-você será solicitado durante as transferências para resolver conflitos. #1455
   * Carregar como BLOBs de páginas – você pode escolher se o AzCopy carrega ou não arquivos. vhd e. vhdx como BLOBs de páginas. #1164 e #1601
   * Parâmetros AzCopy configuráveis – várias configurações foram adicionadas para ajustar o desempenho e o uso de recursos do AzCopy. Veja mais detalhes abaixo.

* Para habilitar o acesso de vários protocolos de ADLS Gen2 e BLOBs e aprimorar ainda mais as experiências de ADLS Gen2, adicionamos os seguintes recursos para as contas de ADLS Gen2:
   * Pesquisar usando nomes amigáveis para definir permissões de ACL
   * Exibir contêineres ocultos, como $logs e $web
   * Adquirir e interromper a concessão de contêiner
   * Adquirir e interromper a concessão de blob #848
   * Gerenciar políticas de acesso de contêiner
   * Configurar camadas de acesso de BLOB
   * Copiar & colar BLOBs

* Nesta versão, estamos visualizando 17 idiomas adicionais. Você pode alternar para um idioma de sua escolha na página configurações em "aplicativo" → "configurações regionais" → "idioma (visualização)". Ainda estamos trabalhando duro para traduzir cadeias de caracteres adicionais e melhorar a qualidade da tradução. Se você tiver algum comentário sobre uma tradução ou se observar uma cadeia de caracteres que ainda não está traduzida, [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* Em todas as versões, tentamos integrar algumas configurações para habilitar a desativação correta Gerenciador de Armazenamento. Nesta versão, adicionamos as configurações para configurar ainda mais o AzCopy, bem como para ocultar os nós de serviço:
   * Limite de largura de banda AzCopy-ajuda a controlar a quantidade de AzCopy de rede usada. Você pode encontrar essa configuração em "Transfers" → "AzCopy" → "taxa de transferência máxima". #1099
   * Verificação de MD5 do AzCopy – permite que você configure se e como a estritamente AzCopy verifica se há hashes MD5 no download. Você pode encontrar essa configuração em "Transfers" → "AzCopy" → "check MD5".
   * Tamanho do buffer de memória e simultaneidade de AzCopy – por padrão, o AzCopy analisará seu computador para determinar valores padrão razoáveis para essas configurações. Mas se você tiver problemas de desempenho, essas configurações avançadas poderão ser usadas para adaptar ainda mais o modo como o AzCopy é executado no computador. Você pode encontrar essas configurações em "transferes" → "AzCopy". #994
   * Exibir e ocultar nós de serviço-essas configurações fornecem as opções para exibir ou ocultar qualquer um dos serviços do Azure aos quais Gerenciador de Armazenamento dá suporte. Você pode encontrar essas configurações na seção "serviços". #1877

* Ao criar um instantâneo de um disco gerenciado, um nome padrão é fornecido agora. #1847
* Ao anexar com o Azure AD, se você anexar um contêiner de blob ADLS Gen2, "(ADLS Gen2)" será mostrado ao lado do nó. #1861

### <a name="fixes"></a>Contida
* Ao copiar, carregar ou baixar discos grandes, Gerenciador de Armazenamento às vezes falharia em revogar o acesso aos discos envolvidos na operação. Isso foi corrigido. #2048
* As estatísticas de tabela falharam ao exibir uma consulta de chave de partição. Isso foi corrigido. #1886

### <a name="known-issues"></a>Problemas Conhecidos
* Gerenciador de Armazenamento 1.11.0 agora requer um ponto de extremidade DFS (como "myaccount.dfs.core.windows.net") para anexar a contêineres de ADLS Gen2. As versões anteriores do Gerenciador de Armazenamento permitiam que você usa um ponto de extremidade de BLOB. Esses anexos podem não funcionar mais após a atualização para o 1.11.0. Se você encontrar esse problema, anexe novamente usando o ponto de extremidade do DFS.
* As configurações numéricas não são verificadas se se encontram em um intervalo válido. #2140
* A cópia de contêineres de blob de uma conta de armazenamento para outra no modo de exibição de árvore pode falhar. Estamos investigando o problema. #2124
* A configuração de atualização automática ainda não afeta todas as operações no Gerenciador de BLOBs.
* Os recursos de disco gerenciado não têm suporte no Azure Stack.
* Se um upload ou uma colagem do disco falhar e um novo disco tiver sido criado antes da falha, Gerenciador de Armazenamento não excluirá o disco para você.
* Dependendo de quando você cancela um upload de disco ou cola, é possível deixar o novo disco em um estado corrompido. Se isso acontecer, você precisará excluir o novo disco ou chamar manualmente as APIs de disco para substituir o conteúdo do disco de modo que ele não seja mais corrompido.
* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* A desanexação de um recurso anexado via URI de SAS, como um contêiner de BLOB, pode causar um erro que impede que outros anexos sejam exibidos corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Consulte #537 para obter mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre esse problema.
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio. Para contornar esse problema ao carregar ou baixar de um contêiner de BLOB, você pode usar o recurso AzCopy experimental.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Azure Stack não oferece suporte aos recursos a seguir. A tentativa de usar esses recursos ao trabalhar com recursos de Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Exclusão reversível
   * ADLS Gen2
   * Managed Disks
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A execução do Gerenciador de Armazenamento no Linux exige que determinadas dependências sejam instaladas primeiro. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Gerenciador de armazenamento para obter mais informações.

## <a name="previous-releases"></a>Versões anteriores

* [1.10.1 da versão](#version-1101)
* [1.10.0 da versão](#version-1100)
* [1.9.0 da versão](#version-190)
* [1.8.1 da versão](#version-181)
* [1.8.0 da versão](#version-180)
* [1.7.0 da versão](#version-170)
* [1.6.2 da versão](#version-162)
* [1.6.1 da versão](#version-161)
* [1.6.0 da versão](#version-160)
* [Versão 1.5.0](#version-150)
* [1.4.4 da versão](#version-144)
* [1.4.3 da versão](#version-143)
* [Versão 1.4.2](#version-142)
* [1.4.1 da versão](#version-141)
* [1.3.0 da versão](#version-130)
* [1.2.0 da versão](#version-120)
* [Versão 1.1.0](#version-110)
* [Versão 1.0.0](#version-100)
* [0.9.6 da versão](#version-096)
* [0.9.5 da versão](#version-095)
* [Versão 0.9.4 e 0.9.3](#version-094-and-093)
* [0.9.2 da versão](#version-092)
* [Versão 0.9.1 e 0.9.0](#version-091-and-090)
* [0.8.16 da versão](#version-0816)
* [0.8.14 da versão](#version-0814)
* [0.8.13 da versão](#version-0813)
* [Versão 0.8.12 e 0.8.11 e 0.8.10](#version-0812-and-0811-and-0810)
* [Versão 0.8.9 e 0.8.8](#version-089-and-088)
* [0.8.7 da versão](#version-087)
* [0.8.6 da versão](#version-086)
* [0.8.5 da versão](#version-085)
* [0.8.4 da versão](#version-084)
* [0.8.3 da versão](#version-083)
* [0.8.2 da versão](#version-082)
* [0.8.0 da versão](#version-080)
* [0.7.20160509.0 da versão](#version-07201605090)
* [0.7.20160325.0 da versão](#version-07201603250)
* [0.7.20160129.1 da versão](#version-07201601291)
* [0.7.20160105.0 da versão](#version-07201601050)
* [0.7.20151116.0 da versão](#version-07201511160)

## <a name="version-1101"></a>1\.10.1 da versão
9/19/2019

### <a name="hotfix"></a>Hotfix
* Alguns usuários encontraram um erro no 1.10.0 ao tentar exibir seus dados em suas contas do ADLS Gen 1. Esse erro impediu que o painel do Explorer fosse renderizado corretamente. Isso foi corrigido. #1853 #1865

### <a name="new"></a>Novo
* Agora Gerenciador de Armazenamento tem uma interface do usuário de configurações dedicada. Você pode acessá-lo em Editar configurações de → ou clicando no ícone de configurações (a engrenagem) na barra de ferramentas vertical à esquerda. Esse recurso é a primeira etapa que estamos levando em voltar a fornecer uma variedade de [configurações solicitadas pelo usuário](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). A partir desta versão, há suporte para as seguintes configurações:
  * Tema
  * Proxy
  * Fazer logoff na #6 de saída
  * Habilitar entrada de fluxo de código do dispositivo
  * #1526 de atualização automática
  * Habilitar AzCopy
  * Duração da SAS do AzCopy se houver outras configurações que você gostaria de ver adicionadas, [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) que descreva a configuração que você deseja ver.
* Gerenciador de Armazenamento agora dá suporte a Managed Disks. Pode:
  * Carregar um VHD local para um novo disco
  * Baixar um disco
  * Copiar e colar discos entre grupos de recursos e regiões
  * Excluir discos
  * Criar um instantâneo de um disco o carregamento, o download e a cópia entre regiões de discos são alimentados por AzCopy v10.
* Agora, o Gerenciador de Armazenamento pode ser instalado por meio do armazenamento de snap no Linux. Quando você instala o por meio do repositório de snap, todas as dependências são instaladas para você, incluindo o .NET Core! Atualmente, verificamos se Gerenciador de Armazenamento é executado bem no Ubuntu e no CentOS. Se você encontrar problemas ao instalar do repositório de snap em outros distribuições do Linux, [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Para saber mais sobre a instalação do repositório de snap, consulte nosso [Guia de introdução](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux). #68
* Duas alterações importantes foram feitas para anexar com Azure Active Directory (Azure AD), que se destinam a tornar o recurso mais útil para ADLS Gen2 usuários:
  * Agora você seleciona o locatário no qual está anexando o recurso. Isso significa que você não precisa mais ter acesso de RBAC à assinatura do recurso.
  * Se você estiver anexando um contêiner de blob ADLS Gen2, agora você poderá anexar a um caminho específico no contêiner.
* Ao gerenciar ACLs para ADLS Gen2 arquivos e pastas, Gerenciador de Armazenamento agora mostrará os nomes amigáveis para entidades na ACL. #957
* Ao adicionar por meio de OID a um ADLS Gen2 ACL, o Gerenciador de Armazenamento agora validará que o OID pertence a uma entidade válida em seu locatário. #1603
* Os atalhos de teclado para navegar entre as guias agora usam mais combinações de teclas padrão. #1018
* O meio de clique em uma guia agora o fechará. #1348
* Se uma transferência AzCopy contiver ignorar e não houver falhas, Gerenciador de Armazenamento agora mostrará um ícone de aviso para realçar que as ignorações ocorreram. #1490
* O AzCopy integrado foi atualizado para a versão 10.2.1. Além disso, agora você pode exibir a versão do AzCopy instalada na caixa de diálogo sobre. #1343

### <a name="fixes"></a>Contida
* Muitos usuários se apresentaram a vários erros "não é possível ler a versão de indefinido" ou "não é possível ler a conexão de erro indefinido" ao trabalhar com contas de armazenamento anexadas. Embora ainda continuemos investigando a causa raiz desse problema, no 1.10.0, melhoramos a manipulação de erros em relação ao carregamento de contas de armazenamento anexadas. #1626, #985 e #1532
* Era possível que a árvore do Gerenciador (lado esquerdo) entrasse em um estado em que o foco saltaria para o nó superior repetidamente. Isso foi corrigido. #1596
* Ao gerenciar os instantâneos de um blob, o leitores não lerá o carimbo de data/hora associado ao instantâneo. Isso foi corrigido. #1202
* A configuração de proxy no macOS não estava sendo definida no tempo para o processo de autenticação usá-las. Isso foi corrigido. #1567
* Se uma conta de armazenamento em uma nuvem soberanas foi anexada usando o nome e a chave, AzCopy não funcionaria. Isso foi corrigido. #1544
* Ao anexar por meio de uma cadeia de conexão, Gerenciador de Armazenamento agora removerá espaços à direita. #1387

### <a name="known-issues"></a>Problemas Conhecidos
* A configuração de atualização automática ainda não afeta todas as operações no Gerenciador de BLOBs.
* Os recursos de disco gerenciado não têm suporte no Azure Stack.
* Se um upload ou uma colagem do disco falhar e um novo disco tiver sido criado antes da falha, Gerenciador de Armazenamento não excluirá o disco para você.
* Dependendo de quando você cancela um upload de disco ou cola, é possível deixar o novo disco em um estado corrompido. Se isso acontecer, você precisará excluir o novo disco ou chamar manualmente as APIs de disco para substituir o conteúdo do disco de modo que ele não seja mais corrompido.
* Dependendo de quando você cancela um upload de disco ou cola, é possível deixar o novo disco em um estado corrompido. Se isso acontecer, você precisará excluir o novo disco ou chamar manualmente as APIs de disco para substituir o conteúdo do disco de modo que ele não seja mais corrompido.
* Ao executar um download de BLOB não AzCopy, o MD5 para arquivos grandes não está sendo verificado. Isso ocorre devido a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* A desanexação de um recurso anexado via URI de SAS, como um contêiner de BLOB, pode causar um erro que impede que outros anexos sejam exibidos corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Consulte #537 para obter mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre esse problema.
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio. Para contornar esse problema ao carregar ou baixar de um contêiner de BLOB, você pode usar o recurso AzCopy experimental.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Azure Stack não oferece suporte aos recursos a seguir. A tentativa de usar esses recursos ao trabalhar com recursos de Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Exclusão reversível
   * ADLS Gen2
   * Managed Disks
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A execução do Gerenciador de Armazenamento no Linux exige que determinadas dependências sejam instaladas primeiro. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Gerenciador de armazenamento para obter mais informações.


## <a name="version-1100"></a>1\.10.0 da versão
9/12/2019

### <a name="new"></a>Novo

* Agora Gerenciador de Armazenamento tem uma interface do usuário de configurações dedicada. Você pode acessá-lo em Editar configurações de → ou clicando no ícone de configurações (a engrenagem) na barra de ferramentas vertical à esquerda. Esse recurso é a primeira etapa que estamos levando em voltar a fornecer uma variedade de [configurações solicitadas pelo usuário](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). A partir desta versão, há suporte para as seguintes configurações:
    * Tema
    * Proxy
    * Fazer logoff na [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6) de saída
    * Habilitar entrada de fluxo de código do dispositivo
    * [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526) de atualização automática
    * Habilitar AzCopy
    * Duração da SAS do AzCopy

    Se houver outras configurações que você gostaria de ver adicionadas, [abra um problema no GitHub que descreva a configuração que você deseja ver](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Gerenciador de Armazenamento agora dá suporte a Managed Disks. Pode:
    * Carregar um VHD local para um novo disco
    * Baixar um disco
    * Copiar e colar discos entre grupos de recursos e regiões
    * Excluir discos
    * Criar um instantâneo de um disco

    O carregamento, o download e a cópia entre regiões de discos são alimentados por AzCopy v10.
* Agora, o Gerenciador de Armazenamento pode ser instalado por meio do armazenamento de snap no Linux. Quando você instala o por meio do repositório de snap, todas as dependências são instaladas para você, incluindo o .NET Core! Atualmente, verificamos se Gerenciador de Armazenamento é executado bem no Ubuntu e no CentOS. Se você encontrar problemas ao instalar do repositório de snap em outros distribuições do Linux, [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Para saber mais sobre a instalação do repositório de snap, consulte nosso [Guia de introdução](https://aka.ms/storageexplorer/snapinformation). [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Duas alterações importantes foram feitas para anexar com Azure Active Directory (Azure AD), que são destinadas a tornar o recurso mais útil para ADLS Gen2 usuários: * agora você seleciona o locatário no qual está anexando o recurso. Isso significa que você não precisa mais ter acesso de RBAC à assinatura do recurso.
        * Se você estiver anexando um contêiner de blob ADLS Gen2, agora você pode anexar a um caminho específico no contêiner.
* Ao gerenciar ACLs para ADLS Gen2 arquivos e pastas, Gerenciador de Armazenamento agora mostrará os nomes amigáveis para entidades na ACL. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Ao adicionar por meio de OID a um ADLS Gen2 ACL, o Gerenciador de Armazenamento agora validará que o OID pertence a uma entidade válida em seu locatário. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* Os atalhos de teclado para navegar entre as guias agora usam mais combinações de teclas padrão. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* O meio de clique em uma guia agora o fechará. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Se uma transferência AzCopy contiver ignorar e não houver falhas, Gerenciador de Armazenamento agora mostrará um ícone de aviso para realçar que as ignorações ocorreram. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* O AzCopy integrado foi atualizado para a versão 10.2.1. Além disso, agora você pode exibir a versão do AzCopy instalada na caixa de diálogo sobre. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Contida

* Muitos usuários se apresentaram a vários erros "não é possível ler a versão de indefinido" ou "não é possível ler a conexão de erro indefinido" ao trabalhar com contas de armazenamento anexadas. Embora ainda continuemos investigando a causa raiz desse problema, no 1.10.0, melhoramos a manipulação de erros em relação ao carregamento de contas de armazenamento anexadas. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)e [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Era possível que a árvore do Gerenciador (lado esquerdo) entrasse em um estado em que o foco saltaria para o nó superior repetidamente. Isso foi corrigido. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Ao gerenciar os instantâneos de um blob, o leitores não lerá o carimbo de data/hora associado ao instantâneo. Isso foi corrigido. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* A configuração de proxy no macOS não estava sendo definida no tempo para o processo de autenticação usá-las. Isso foi corrigido. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Se uma conta de armazenamento em uma nuvem soberanas foi anexada usando o nome e a chave, AzCopy não funcionaria. Isso foi corrigido. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Ao anexar por meio de uma cadeia de conexão, Gerenciador de Armazenamento agora removerá espaços à direita. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Problemas Conhecidos

* A configuração de atualização automática ainda não afeta todas as operações no Gerenciador de BLOBs.
* Os recursos de disco gerenciado não têm suporte no Azure Stack.
* Se um upload ou uma colagem do disco falhar e um novo disco tiver sido criado antes da falha, Gerenciador de Armazenamento não excluirá o disco para você.
* Dependendo de quando você cancela um upload de disco ou cola, é possível deixar o novo disco em um estado corrompido. Se isso acontecer, você precisará excluir o novo disco ou chamar manualmente as APIs de disco para substituir o conteúdo do disco de modo que ele não seja mais corrompido.
* Ao executar um download de BLOB não AzCopy, o MD5 para arquivos grandes não está sendo verificado. Isso ocorre devido a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* A desanexação de um recurso anexado via URI de SAS, como um contêiner de BLOB, pode causar um erro que impede que outros anexos sejam exibidos corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Consulte #537 para obter mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre esse problema.
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio. Para contornar esse problema ao carregar ou baixar de um contêiner de BLOB, você pode usar o recurso AzCopy experimental.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Azure Stack não oferece suporte aos recursos a seguir. A tentativa de usar esses recursos ao trabalhar com recursos de Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Exclusão reversível
   * ADLS Gen2
   * Managed Disks
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A execução do Gerenciador de Armazenamento no Linux exige que determinadas dependências sejam instaladas primeiro. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Gerenciador de armazenamento para obter mais informações.

## <a name="version-190"></a>1\.9.0 da versão
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Baixar Gerenciador de Armazenamento do Azure 1.9.0
- [Gerenciador de Armazenamento do Azure 1.9.0 para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Gerenciador de Armazenamento do Azure 1.9.0 para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Gerenciador de Armazenamento do Azure 1.9.0 para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Novo

* Agora você pode anexar contêineres de blob por meio do Azure AD (permissões RBAC ou ACL). Esse recurso destina-se a ajudar os usuários que têm acesso aos contêineres, mas não às contas de armazenamento nas quais os contêineres estão. Consulte nosso guia de Introdução para obter mais informações sobre esse recurso.
* Adquira e interrompa a concessão agora trabalhe com o RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* O gerenciamento de políticas de acesso e a definição do nível de acesso público agora funcionam com o RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* A exclusão de pastas de BLOBs agora funciona com o RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* A alteração da camada de acesso ao blob agora funciona com o RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Agora você pode redefinir rapidamente o acesso rápido por meio de "Help" → "Reset". [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Funcionalidades de Pré-visualização

* A entrada do fluxo de código do dispositivo agora está disponível para visualização. Para habilitá-lo, vá para "visualização" → "usar entrada de fluxo de código de dispositivo". Incentivamos qualquer usuário que tenha problemas com janelas de entrada em branco para experimentar esse recurso, pois pode ser uma forma mais confiável de entrar.
* O Gerenciador de Armazenamento integrado ao AzCopy está disponível para visualização no momento. Para habilitá-lo, acesse "visualização" → "usar AzCopy para upload e download de blob aprimorados". As transferências de blob concluídas com AzCopy devem ser mais rápidas e com melhor desempenho.

### <a name="fixes"></a>Contida

* Corrigido não é possível carregar mais de 50 assinaturas para uma conta. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Corrigido o botão "entrar" não está funcionando na barra de referências que aparece quando um link direto falha. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Correção de não carregar arquivos. app no macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* "Repetir tudo" corrigido não está funcionando para uma renomeação de blob com falha. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* "Cancelar" fixo não está funcionando ao abrir um blob. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Correção de vários problemas de ortografia e dica de ferramenta em todo o produto. Muitos agradecimentos a todos os que relataram esses problemas! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336), [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368), [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Problemas Conhecidos

* Ao executar um download de BLOB não AzCopy, o MD5 para arquivos grandes não está sendo verificado. Isso ocorre devido a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* A tentativa de acessar BLOBs ADLS Gen2 quando por trás de um proxy pode falhar.
* A desanexação de um recurso anexado via URI de SAS, como um contêiner de BLOB, pode causar um erro que impede que outros anexos sejam exibidos corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Consulte #537 para obter mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre esse problema.
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio. Para contornar esse problema ao carregar ou baixar de um contêiner de BLOB, você pode usar o recurso AzCopy experimental.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Azure Stack não oferece suporte aos recursos a seguir. A tentativa de usar esses recursos ao trabalhar com recursos de Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Exclusão reversível
   * ADLS Gen2
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A execução do Gerenciador de Armazenamento no Linux exige que determinadas dependências sejam instaladas primeiro. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Gerenciador de armazenamento para obter mais informações.

## <a name="version-181"></a>1\.8.1 da versão
5/13/2019

### <a name="hotfixes"></a>Hotfixes
* Em alguns casos, clicar em "carregar mais" no nível de recurso não retornará a próxima página de recursos. Isso foi corrigido. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* No Windows, os downloads do AzCopy falharão se um único arquivo ou pasta fosse baixado e o nome do arquivo ou da pasta tivesse um caractere que era inválido para um caminho do Windows. Isso foi corrigido. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* Em casos extremamente raros, ao executar uma renomeação de um compartilhamento de arquivos ou uma renomeação em um compartilhamento de arquivos, se as cópias da renomeação falharem ou se a exploração do armazenamento não puder confirmar o sucesso das cópias com o Azure, houve o potencial para Gerenciador de Armazenamento excluir o o riginal arquivos antes que a cópia fosse concluída. Isso foi corrigido.

### <a name="new"></a>Novo

* A versão integrada do AzCopy foi atualizada para a versão 10.1.0.
* Ctrl/Cmd + R agora pode ser usado para atualizar o editor voltado para o momento. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* A versão da API de armazenamento Azure Stack foi alterada para 2017-04-17.
* A caixa de diálogo Gerenciar acesso para ADLS Gen2 agora manterá a máscara sincronizada de forma semelhante a outras ferramentas de permissões POSIX. A interface do usuário também avisará se for feita uma alteração que faz com que as permissões de um usuário ou grupo excedam os limites da máscara. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Para carregamentos do AzCopy, o sinalizador para calcular e definir o hash MD5 agora está habilitado. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funcionalidades de Pré-visualização

* A entrada do fluxo de código do dispositivo agora está disponível para visualização. Para habilitá-lo, vá para "visualização" → "usar entrada de fluxo de código de dispositivo". Incentivamos qualquer usuário que tenha problemas com janelas de entrada em branco para experimentar esse recurso, pois pode ser uma forma mais confiável de entrar.
* O Gerenciador de Armazenamento integrado ao AzCopy está disponível para visualização no momento. Para habilitá-lo, acesse "visualização" → "usar AzCopy para upload e download de blob aprimorados". As transferências de blob concluídas com AzCopy devem ser mais rápidas e com melhor desempenho.

### <a name="fixes"></a>Contida

* A caixa de diálogo políticas de acesso não definirá mais uma data de expiração nas políticas de acesso de armazenamento que não têm expiração. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Algumas alterações foram feitas na caixa de diálogo gerar SAS para garantir que as políticas de acesso armazenadas sejam usadas corretamente ao gerar uma SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Ao tentar carregar um arquivo com alinhamento de não 512 bytes em um blob de páginas, Gerenciador de Armazenamento agora vai expor um erro mais relevante. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* A cópia de um contêiner de BLOB que utiliza um nome de exibição falharia. Agora, o nome real do contêiner de BLOBs é usado. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* A tentativa de executar determinadas ações em uma pasta ADLS Gen2 que tinha caracteres Unicode em seu nome falharia. Todas as ações agora devem funcionar. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Problemas Conhecidos

* Ao executar um download de BLOB não AzCopy, o MD5 para arquivos grandes não está sendo verificado. Isso ocorre devido a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* A tentativa de acessar BLOBs ADLS Gen2 quando por trás de um proxy pode falhar.
* A desanexação de um recurso anexado via URI de SAS, como um contêiner de BLOB, pode causar um erro que impede que outros anexos sejam exibidos corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Consulte #537 para obter mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre esse problema.
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio. Para contornar esse problema ao carregar ou baixar de um contêiner de BLOB, você pode usar o recurso AzCopy experimental.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Azure Stack não oferece suporte aos recursos a seguir. A tentativa de usar esses recursos ao trabalhar com recursos de Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Exclusão reversível
   * ADLS Gen2
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A execução do Gerenciador de Armazenamento no Linux exige que determinadas dependências sejam instaladas primeiro. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Gerenciador de armazenamento para obter mais informações.

## <a name="version-180"></a>1\.8.0 da versão
5/1/2019

### <a name="new"></a>Novo

* A versão integrada do AzCopy foi atualizada para a versão 10.1.0.
* Ctrl/Cmd + R agora pode ser usado para atualizar o editor voltado para o momento. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* A versão da API de armazenamento Azure Stack foi alterada para 2017-04-17.
* A caixa de diálogo Gerenciar acesso para ADLS Gen2 agora manterá a máscara sincronizada de forma semelhante a outras ferramentas de permissões POSIX. A interface do usuário também avisará se for feita uma alteração que faz com que as permissões de um usuário ou grupo excedam os limites da máscara. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Para carregamentos do AzCopy, o sinalizador para calcular e definir o hash MD5 agora está habilitado. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funcionalidades de Pré-visualização

* A entrada do fluxo de código do dispositivo agora está disponível para visualização. Para habilitá-lo, vá para "visualização" → "usar entrada de fluxo de código de dispositivo". Incentivamos qualquer usuário que tenha problemas com janelas de entrada em branco para experimentar esse recurso, pois pode ser uma forma mais confiável de entrar.
* O Gerenciador de Armazenamento integrado ao AzCopy está disponível para visualização no momento. Para habilitá-lo, acesse "visualização" → "usar AzCopy para upload e download de blob aprimorados". As transferências de blob concluídas com AzCopy devem ser mais rápidas e com melhor desempenho.

### <a name="fixes"></a>Contida

* A caixa de diálogo políticas de acesso não definirá mais uma data de expiração nas políticas de acesso de armazenamento que não têm expiração. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Algumas alterações foram feitas na caixa de diálogo gerar SAS para garantir que as políticas de acesso armazenadas sejam usadas corretamente ao gerar uma SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Ao tentar carregar um arquivo com alinhamento de não 512 bytes em um blob de páginas, Gerenciador de Armazenamento agora vai expor um erro mais relevante. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* A cópia de um contêiner de BLOB que utiliza um nome de exibição falharia. Agora, o nome real do contêiner de BLOBs é usado. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* A tentativa de executar determinadas ações em uma pasta ADLS Gen2 que tinha caracteres Unicode em seu nome falharia. Todas as ações agora devem funcionar. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Problemas Conhecidos

* Ao executar um download de BLOB não AzCopy, o MD5 para arquivos grandes não está sendo verificado. Isso ocorre devido a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* A tentativa de acessar BLOBs ADLS Gen2 quando por trás de um proxy pode falhar.
* A desanexação de um recurso anexado via URI de SAS, como um contêiner de BLOB, pode causar um erro que impede que outros anexos sejam exibidos corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Consulte #537 para obter mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre esse problema.
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio. Para contornar esse problema ao carregar ou baixar de um contêiner de BLOB, você pode usar o recurso AzCopy experimental.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Azure Stack não oferece suporte aos recursos a seguir. A tentativa de usar esses recursos ao trabalhar com recursos de Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Exclusão reversível
   * ADLS Gen2
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A execução do Gerenciador de Armazenamento no Linux exige que determinadas dependências sejam instaladas primeiro. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Gerenciador de armazenamento para obter mais informações.

## <a name="version-170"></a>Versão 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Baixar Gerenciador de Armazenamento do Azure 1.7.0
- [Gerenciador de Armazenamento do Azure 1.7.0 para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Gerenciador de Armazenamento do Azure 1.7.0 para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Gerenciador de Armazenamento do Azure 1.7.0 para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Novo

* Agora você pode alterar o proprietário e o grupo proprietário ao gerenciar o acesso para um ADLS Gen2 contêiner, arquivo ou pasta.
* No Windows, a atualização de Gerenciador de Armazenamento de dentro do produto agora é uma instalação incremental. Isso deve resultar em uma experiência de atualização mais rápida. Se você preferir uma instalação limpa, poderá baixar o [instalador](https://azure.microsoft.com/features/storage-explorer/) por conta própria e, em seguida, instalá-lo manualmente. #1089

### <a name="preview-features"></a>Funcionalidades de Pré-visualização

* A entrada do fluxo de código do dispositivo agora está disponível para visualização. Para habilitá-lo, vá para "visualização" → "usar entrada de fluxo de código de dispositivo". Incentivamos qualquer usuário que tenha problemas com janelas de entrada em branco para experimentar esse recurso, pois pode ser uma forma mais confiável de entrar. #938
* O Gerenciador de Armazenamento integrado ao AzCopy está disponível para visualização no momento. Para habilitá-lo, acesse "visualização" → "usar AzCopy para upload e download de blob aprimorados". As transferências de blob concluídas com AzCopy devem ser mais rápidas e com melhor desempenho.

### <a name="fixes"></a>Contida

* Agora você pode escolher o tipo de BLOB que deseja carregar como quando AzCopy está habilitado. #1111
* Anteriormente, se você tivesse habilitado sites estáticos para uma conta de armazenamento ADLS Gen2 e, em seguida, anexou-o com o nome e a chave, Gerenciador de Armazenamento não detectou que o namespace hierárquico estava habilitado. Isso foi corrigido. #1081
* No editor de BLOB, a classificação por dias de retenção restantes ou o status foi rompido. Isso foi corrigido. #1106
* Depois da 1.5.0, Gerenciador de Armazenamento não espere mais que as cópias do lado do servidor sejam concluídas antes de relatar o sucesso durante uma renomeação ou cópia & colar. Isso foi corrigido. #976
* Ao usar o recurso AzCopy experimental, o comando copiado depois de clicar em "copiar comando para a área de transferência" nem sempre era executável por si só. Agora, todos os comandos necessários para executar a transferência manualmente serão copiados. #1079
* Anteriormente, ADLS Gen2 BLOBs não eram acessíveis se você estivesse atrás de um proxy. Isso ocorreu devido a um bug em uma nova biblioteca de rede usada pelo SDK de armazenamento. No 1.7.0, uma tentativa de mitigar esse problema foi feita, mas algumas pessoas podem continuar a ver os problemas. Uma correção completa será lançada em uma atualização futura. #1090
* No 1.7.0, a caixa de diálogo Salvar arquivo agora memoriza corretamente o último local em que você salvou um arquivo. #16
* No painel Propriedades, a camada de SKU de uma conta de armazenamento estava sendo mostrada como a espécie da conta. Isso foi corrigido. #654
* Às vezes, era impossível interromper a concessão de um blob, mesmo se você inseriu o nome do blob corretamente. Isso foi corrigido. #1070

### <a name="known-issues"></a>Problemas Conhecidos

* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* A tentativa de acessar BLOBs ADLS Gen2 quando por trás de um proxy pode falhar.
* A desanexação de um recurso anexado via URI de SAS, como um contêiner de BLOB, pode causar um erro que impede que outros anexos sejam exibidos corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Consulte #537 para obter mais informações.
* A desanexação de um recurso anexado via URI de SAS, como um contêiner de BLOB, pode causar um erro que impede que outros anexos sejam exibidos corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Para obter mais informações, consulte #537.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre esse problema.
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio. Para contornar esse problema ao carregar ou baixar de um contêiner de BLOB, você pode usar o recurso AzCopy experimental.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Azure Stack não oferece suporte aos recursos a seguir. A tentativa de usar esses recursos ao trabalhar com recursos de Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Exclusão reversível
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, será necessário instalar o [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>1\.6.2 da versão
1/9/2019

### <a name="hotfixes"></a>Hotfixes
* No 1.6.1, as entidades adicionadas a ADLS Gen2 ACLs por ObjectId que não eram usuários foram sempre adicionadas como grupos. Agora, somente grupos são adicionados como grupos, e entidades como aplicativos empresariais andService entidades de segurança são adicionadas como usuários. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Se uma conta de armazenamento ADLS Gen2 não tiver contêineres e tiver sido anexada ao nome e à chave, Gerenciador de Armazenamento não detectaria que a conta de armazenamento estava ADLS Gen2. Isso foi corrigido. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* Em 1.6.0, os conflitos durante copiar e colar não solicitarão uma resolução. Em vez disso, a cópia conflitante simplesmente falharia. Agora, no primeiro conflito, você será perguntado como deseja que ele seja resolvido. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Devido a limitações de API, todas as validações de ObjectIds na caixa de diálogo Gerenciar acesso foram desabilitadas. A validação ocorrerá agora apenas para UPNs do usuário. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* Na caixa de diálogo ADLS Gen2 gerenciar acesso, as permissões para um grupo não puderam ser modificadas. Isso foi corrigido. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Adicionado suporte de upload de arrastar e soltar ao editor de ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Às vezes, a propriedade URL na caixa de diálogo Propriedades de ADLS Gen2 arquivos e pastas não tem um '/'. Isso foi corrigido. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Se a obtenção das permissões atuais para um ADLS Gen2 contêiner, arquivo ou pasta falhar, o erro agora será exibido de uma vez no log de atividades. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* O caminho temporário criado para abrir arquivos foi reduzido para reduzir a chance de criar um caminho que seja maior que MAX_PATH no Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* A caixa de diálogo Conectar agora aparece corretamente quando não há usuários conectados e nenhum recurso foi anexado. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* No 1.6.0, salvar propriedades para BLOBs e arquivos não do HNS codificaria o valor de cada propriedade. Isso resultou na codificação desnecessária de valores que continham apenas caracteres ASCII. Agora, os valores só serão codificados se contiverem caracteres não ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* O carregamento de uma pasta para um contêiner de BLOBs que não seja do HNS falhará se uma SAS tiver sido usada e a SAS não tiver permissões de leitura. Isso foi corrigido. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Cancelar uma transferência de AzCopy não funcionou. Isso foi corrigido. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy falharia ao tentar baixar uma pasta de um contêiner de blob ADLS Gen2 se a pasta tivesse espaços em seu nome. Isso foi corrigido. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* O editor CosmosDB foi quebrado em 1.6.0. Agora ele está corrigido. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Novo

* Agora você pode usar Gerenciador de Armazenamento para acessar seus dados de blob via [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se você estiver conectado e Gerenciador de Armazenamento não puder recuperar as chaves para sua conta de armazenamento, um token OAuth será usado para autenticar ao interagir com seus dados.
* Gerenciador de Armazenamento agora dá suporte a ADLS Gen2 contas de armazenamento. Quando Gerenciador de Armazenamento detectar que o namespace hierárquico está habilitado para uma conta de armazenamento, você verá "(ADLS Gen2 visualização)" ao lado do nome da sua conta de armazenamento. Gerenciador de Armazenamento é capaz de detectar se o namespace hierárquico está habilitado ou não quando você está conectado ou se você anexou sua conta de armazenamento com o nome e a chave. Para ADLS Gen2 contas de armazenamento, você pode usar Gerenciador de Armazenamento para:
  * Criar e excluir contêineres
  * Gerenciar Propriedades e permissões de contêiner (lado esquerdo)
  * Exibir e navegar dados dentro de contêineres
  * Criar novas pastas
  * Carregar, baixar, renomear e excluir arquivos e pastas
  * Gerenciar Propriedades e permissões de arquivo e pasta (lado direito).
    
    Outros recursos de blob típicos, como exclusão reversível e instantâneos, não estão disponíveis no momento. O gerenciamento de permissões também está disponível somente quando conectado. Além disso, ao trabalhar em uma conta de armazenamento ADLS Gen2, Gerenciador de Armazenamento usará o AzCopy para todos os uploads e downloads e usará as credenciais de nome e chave para todas as operações, se disponíveis.
* Após os comentários fortes dos usuários, a concessão de quebra pode ser usada novamente para interromper concessões em vários BLOBs de uma vez.

### <a name="known-issues"></a>Problemas Conhecidos

* Ao baixar de uma conta de armazenamento ADLS Gen2, se um dos arquivos que estão sendo transferidos já existir, o AzCopy às vezes falhará. Isso será corrigido em um hotfix futuro.
* A desanexação de um recurso anexado via URI de SAS, como um contêiner de BLOB, pode causar um erro que impede que outros anexos sejam exibidos corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Para obter mais informações, consulte #537.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre esse problema.
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio. Para contornar esse problema ao carregar ou baixar de um contêiner de BLOB, você pode usar o recurso AzCopy experimental.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Azure Stack não oferece suporte aos recursos a seguir. A tentativa de usar esses recursos ao trabalhar com recursos de Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Exclusão reversível
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, será necessário instalar o [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>1\.6.1 da versão
12/18/2018

### <a name="hotfixes"></a>Hotfixes
* Devido a limitações de API, todas as validações de ObjectIds na caixa de diálogo Gerenciar acesso foram desabilitadas. A validação ocorrerá agora apenas para UPNs do usuário. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* Na caixa de diálogo ADLS Gen2 gerenciar acesso, as permissões para um grupo não puderam ser modificadas. Isso foi corrigido. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Adicionado suporte de upload de arrastar e soltar ao editor de ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Às vezes, a propriedade URL na caixa de diálogo Propriedades de ADLS Gen2 arquivos e pastas não tem um '/'. Isso foi corrigido. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Se a obtenção das permissões atuais para um ADLS Gen2 contêiner, arquivo ou pasta falhar, o erro agora será exibido de uma vez no log de atividades. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* O caminho temporário criado para abrir arquivos foi reduzido para reduzir a chance de criar um caminho que seja maior que MAX_PATH no Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* A caixa de diálogo Conectar agora aparece corretamente quando não há usuários conectados e nenhum recurso foi anexado. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* No 1.6.0, salvar propriedades para BLOBs e arquivos não do HNS codificaria o valor de cada propriedade. Isso resultou na codificação desnecessária de valores que continham apenas caracteres ASCII. Agora, os valores só serão codificados se contiverem caracteres não ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* O carregamento de uma pasta para um contêiner de BLOBs que não seja do HNS falhará se uma SAS tiver sido usada e a SAS não tiver permissões de leitura. Isso foi corrigido. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Cancelar uma transferência de AzCopy não funcionou. Isso foi corrigido. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy falharia ao tentar baixar uma pasta de um contêiner de blob ADLS Gen2 se a pasta tivesse espaços em seu nome. Isso foi corrigido. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* O editor CosmosDB foi quebrado em 1.6.0. Agora ele está corrigido. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Novo

* Agora você pode usar Gerenciador de Armazenamento para acessar seus dados de blob via [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se você estiver conectado e Gerenciador de Armazenamento não puder recuperar as chaves para sua conta de armazenamento, um token OAuth será usado para autenticar ao interagir com seus dados.
* Gerenciador de Armazenamento agora dá suporte a ADLS Gen2 contas de armazenamento. Quando Gerenciador de Armazenamento detectar que o namespace hierárquico está habilitado para uma conta de armazenamento, você verá "(ADLS Gen2 visualização)" ao lado do nome da sua conta de armazenamento. Gerenciador de Armazenamento é capaz de detectar se o namespace hierárquico está habilitado ou não quando você está conectado ou se você anexou sua conta de armazenamento com o nome e a chave. Para ADLS Gen2 contas de armazenamento, você pode usar Gerenciador de Armazenamento para:
  * Criar e excluir contêineres
  * Gerenciar Propriedades e permissões de contêiner (lado esquerdo)
  * Exibir e navegar dados dentro de contêineres
  * Criar novas pastas
  * Carregar, baixar, renomear e excluir arquivos e pastas
  * Gerenciar Propriedades e permissões de arquivo e pasta (lado direito).
    
    Outros recursos de blob típicos, como exclusão reversível e instantâneos, não estão disponíveis no momento. O gerenciamento de permissões também está disponível somente quando conectado. Além disso, ao trabalhar em uma conta de armazenamento ADLS Gen2, Gerenciador de Armazenamento usará o AzCopy para todos os uploads e downloads e usará as credenciais de nome e chave para todas as operações, se disponíveis.
* Após os comentários fortes dos usuários, a concessão de quebra pode ser usada novamente para interromper concessões em vários BLOBs de uma vez.

### <a name="known-issues"></a>Problemas Conhecidos

* Ao baixar de uma conta de armazenamento ADLS Gen2, se um dos arquivos que estão sendo transferidos já existir, o AzCopy às vezes falhará. Isso será corrigido em um hotfix futuro.
* A desanexação de um recurso anexado via URI de SAS, como um contêiner de BLOB, pode causar um erro que impede que outros anexos sejam exibidos corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Para obter mais informações, consulte #537.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre esse problema.
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio. Para contornar esse problema ao carregar ou baixar de um contêiner de BLOB, você pode usar o recurso AzCopy experimental.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Azure Stack não oferece suporte aos recursos a seguir. A tentativa de usar esses recursos ao trabalhar com recursos de Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Exclusão reversível
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, será necessário instalar o [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>1\.6.0 da versão
05/12/2018

### <a name="new"></a>Novo

* Agora você pode usar Gerenciador de Armazenamento para acessar seus dados de blob via [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se você estiver conectado e Gerenciador de Armazenamento não puder recuperar as chaves para sua conta de armazenamento, um token OAuth será usado para autenticar ao interagir com seus dados.
* Gerenciador de Armazenamento agora dá suporte a ADLS Gen2 contas de armazenamento. Quando Gerenciador de Armazenamento detectar que o namespace hierárquico está habilitado para uma conta de armazenamento, você verá "(ADLS Gen2 visualização)" ao lado do nome da sua conta de armazenamento. Gerenciador de Armazenamento é capaz de detectar se o namespace hierárquico está habilitado ou não quando você está conectado ou se você anexou sua conta de armazenamento com o nome e a chave. Para ADLS Gen2 contas de armazenamento, você pode usar Gerenciador de Armazenamento para:
  * Criar e excluir contêineres
  * Gerenciar Propriedades e permissões de contêiner (lado esquerdo)
  * Exibir e navegar dados dentro de contêineres
  * Criar novas pastas
  * Carregar, baixar, renomear e excluir arquivos e pastas
  * Gerenciar Propriedades e permissões de arquivo e pasta (lado direito).
    
    Outros recursos de blob típicos, como exclusão reversível e instantâneos, não estão disponíveis no momento. O gerenciamento de permissões também está disponível somente quando conectado. Além disso, ao trabalhar em uma conta de armazenamento ADLS Gen2, Gerenciador de Armazenamento usará o AzCopy para todos os uploads e downloads e usará as credenciais de nome e chave para todas as operações, se disponíveis.
* Após os comentários fortes dos usuários, a concessão de quebra pode ser usada novamente para interromper concessões em vários BLOBs de uma vez.

### <a name="known-issues"></a>Problemas Conhecidos

* Ao baixar de uma conta de armazenamento ADLS Gen2, se um dos arquivos que estão sendo transferidos já existir, o AzCopy às vezes falhará. Isso será corrigido em um hotfix futuro.
* A desanexação de um recurso anexado via URI de SAS, como um contêiner de BLOB, pode causar um erro que impede que outros anexos sejam exibidos corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Para obter mais informações, consulte #537.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre esse problema.
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio. Para contornar esse problema ao carregar ou baixar de um contêiner de BLOB, você pode usar o recurso AzCopy experimental.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Azure Stack não oferece suporte aos recursos a seguir. A tentativa de usar esses recursos ao trabalhar com recursos de Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Exclusão reversível
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, será necessário instalar o [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Versão 1.5.0
10/29/2018

### <a name="new"></a>Novo

* Agora você pode usar [AzCopy v10 (visualização)](https://github.com/Azure/azure-storage-azcopy) para carregar e baixar BLOBs. Para habilitar esse recurso, vá para o menu "experimental" e, em seguida, clique em "usar AzCopy para upload e download de blob aprimorados". Quando habilitado, AzCopy será usado nos seguintes cenários:
   * Carregamento de pastas e arquivos para contêineres de BLOB, seja por meio da barra de ferramentas ou arrastando e soltando.
   * Download de pastas e arquivos, seja por meio da barra de ferramentas ou do menu de contexto.

* Além disso, ao usar AzCopy:
   * Você pode copiar o comando AzCopy usado para executar a transferência para a área de transferência. Basta clicar em "copiar o comando AzCopy para a área de transferência" no log de atividades.
   * Você precisará atualizar o editor de blob manualmente após o carregamento.
   * Não há suporte para o carregamento de arquivos em blobs de acréscimo, e os arquivos VHD serão carregados como BLOBs de páginas e todos os outros arquivos serão carregados como BLOBs de blocos.
   * Erros e conflitos que ocorrem durante o carregamento ou download não serão exibidos até que um carregamento ou download seja concluído.

Por fim, o suporte ao uso de AzCopy com compartilhamentos de arquivos será lançado no futuro.
* Gerenciador de Armazenamento agora está usando a versão 2.0.11 do
* A quebra de leasing agora pode ser executada apenas em um blob por vez. Além disso, você precisa inserir o nome do BLOB cuja concessão está sendo interrompida. Essa alteração foi feita para reduzir a probabilidade de interromper acidentalmente uma concessão, especialmente para VMs. #394
* Se você já encontrar problemas de entrada, agora você pode tentar redefinir a autenticação. Vá para o menu "ajuda" e clique em "redefinir" para acessar esse recurso. #419

### <a name="fix"></a>Soluciona

* Após os comentários de usuários fortes, o nó de emulador padrão foi reabilitado. Você ainda pode adicionar conexões de emulador adicionais por meio da caixa de diálogo conectar, mas se o emulador estiver configurado para usar as portas padrão, você também poderá usar o nó "portas padrão do emulador *" em "local &/contas de armazenamento". #669
* Gerenciador de Armazenamento não permitirá que você defina valores de metadados de BLOB que tenham espaço em branco à esquerda ou à direita. #760
* O botão "entrar" sempre foi habilitado nas mesmas páginas da caixa de diálogo Conectar. Agora, ele é desabilitado quando apropriado. #761
* O acesso rápido não gerará mais um erro no console quando nenhum item de acesso rápido tiver sido adicionado.

### <a name="known-issues"></a>Problemas Conhecidos

* A desanexação de um recurso anexado via URI de SAS, como um contêiner de BLOB, pode causar um erro que impede que outros anexos sejam exibidos corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Para obter mais informações, consulte #537.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre esse problema.
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio. Para contornar esse problema ao carregar ou baixar de um contêiner de BLOB, você pode usar o recurso AzCopy experimental.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Azure Stack não oferece suporte aos recursos a seguir. A tentativa de usar esses recursos ao trabalhar com recursos de Azure Stack pode resultar em erros inesperados.
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Exclusão reversível
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, será necessário instalar o [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>1\.4.4 da versão
10/15/2018

### <a name="hotfixes"></a>Hotfixes
* A versão da API de gerenciamento de recursos do Azure foi revertida para desbloquear os usuários do governo dos EUA do Azure. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* O carregamento de getinteriors agora está usando animações CSS para reduzir a quantidade de GPU usada pelo Gerenciador de Armazenamento. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Novo
* Os anexos de recursos externos, como para conexões SAS e emuladores, foram significativamente aprimorados. Agora você pode:
   * Personalize o nome de exibição do recurso que você está anexando. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexe a vários emuladores locais usando portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicionar recursos anexados ao acesso rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Gerenciador de Armazenamento agora dá suporte à exclusão reversível. Pode:
   * Configure uma política de exclusão reversível clicando com o botão direito do mouse no nó contêineres de BLOB para sua conta de armazenamento.
   * Exiba BLOBs com exclusão reversível no editor de blob selecionando "BLOBs ativos e excluídos" na lista suspensa ao lado da barra de navegação.
   * Restaurar BLOBs com exclusão reversível.

### <a name="fixes"></a>Contida
* A ação "definir configurações de CORS" não está mais disponível em contas de armazenamento Premium porque as contas de armazenamento Premium não dão suporte a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Agora há uma propriedade de assinatura de acesso compartilhado para serviços conectados à SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação "definir camada de acesso padrão" agora está disponível para contas de armazenamento de BLOBs e GPV2 que foram fixadas para acesso rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, Gerenciador de Armazenamento falha ao mostrar as contas de armazenamento clássicas. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao usar emuladores, como o emulador de armazenamento do Azure ou o azurite, você precisará fazer com que eles escutem as conexões em suas portas padrão. Caso contrário, Gerenciador de Armazenamento não será capaz de se conectar a eles.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, será necessário instalar o [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>1\.4.3 da versão
10/11/2018

### <a name="hotfixes"></a>Hotfixes
* A versão da API de gerenciamento de recursos do Azure foi revertida para desbloquear os usuários do governo dos EUA do Azure. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* O carregamento de getinteriors agora está usando animações CSS para reduzir a quantidade de GPU usada pelo Gerenciador de Armazenamento. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Novo
* Os anexos de recursos externos, como para conexões SAS e emuladores, foram significativamente aprimorados. Agora você pode:
   * Personalize o nome de exibição do recurso que você está anexando. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexe a vários emuladores locais usando portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicionar recursos anexados ao acesso rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Gerenciador de Armazenamento agora dá suporte à exclusão reversível. Pode:
   * Configure uma política de exclusão reversível clicando com o botão direito do mouse no nó contêineres de BLOB para sua conta de armazenamento.
   * Exiba BLOBs com exclusão reversível no editor de blob selecionando "BLOBs ativos e excluídos" na lista suspensa ao lado da barra de navegação.
   * Restaurar BLOBs com exclusão reversível.

### <a name="fixes"></a>Contida
* A ação "definir configurações de CORS" não está mais disponível em contas de armazenamento Premium porque as contas de armazenamento Premium não dão suporte a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Agora há uma propriedade de assinatura de acesso compartilhado para serviços conectados à SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação "definir camada de acesso padrão" agora está disponível para contas de armazenamento de BLOBs e GPV2 que foram fixadas para acesso rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, Gerenciador de Armazenamento falha ao mostrar as contas de armazenamento clássicas. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao usar emuladores, como o emulador de armazenamento do Azure ou o azurite, você precisará fazer com que eles escutem as conexões em suas portas padrão. Caso contrário, Gerenciador de Armazenamento não será capaz de se conectar a eles.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, será necessário instalar o [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Versão 1.4.2
09/24/2018

### <a name="hotfixes"></a>Hotfixes
* Atualize a versão da API de gerenciamento de recursos do Azure para 2018-07-01 para adicionar suporte para novos tipos de conta de armazenamento do Azure. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Novo
* Os anexos de recursos externos, como para conexões SAS e emuladores, foram significativamente aprimorados. Agora você pode:
   * Personalize o nome de exibição do recurso que você está anexando. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexe a vários emuladores locais usando portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicionar recursos anexados ao acesso rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Gerenciador de Armazenamento agora dá suporte à exclusão reversível. Pode:
   * Configure uma política de exclusão reversível clicando com o botão direito do mouse no nó contêineres de BLOB para sua conta de armazenamento.
   * Exiba BLOBs com exclusão reversível no editor de blob selecionando "BLOBs ativos e excluídos" na lista suspensa ao lado da barra de navegação.
   * Restaurar BLOBs com exclusão reversível.

### <a name="fixes"></a>Contida
* A ação "definir configurações de CORS" não está mais disponível em contas de armazenamento Premium porque as contas de armazenamento Premium não dão suporte a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Agora há uma propriedade de assinatura de acesso compartilhado para serviços conectados à SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação "definir camada de acesso padrão" agora está disponível para contas de armazenamento de BLOBs e GPV2 que foram fixadas para acesso rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, Gerenciador de Armazenamento falha ao mostrar as contas de armazenamento clássicas. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao usar emuladores, como o emulador de armazenamento do Azure ou o azurite, você precisará fazer com que eles escutem as conexões em suas portas padrão. Caso contrário, Gerenciador de Armazenamento não será capaz de se conectar a eles.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, será necessário instalar o [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>1\.4.1 da versão
08/28/2018

### <a name="hotfixes"></a>Hotfixes
* Na primeira inicialização, Gerenciador de Armazenamento não pôde gerar a chave usada para criptografar dados confidenciais. Isso causaria problemas ao usar o acesso rápido e a anexar recursos. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Se sua conta não requer MFA para seu locatário inicial, mas fez para alguns outros locatários, Gerenciador de Armazenamento não seria possível listar assinaturas. Agora, depois de entrar com essa conta, Gerenciador de Armazenamento pedirá que você insira novamente suas credenciais e execute MFA. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* O Gerenciador de Armazenamento não pôde anexar recursos do Azure Alemanha e do governo dos EUA do Azure. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Se você tiver entrado em duas contas que tinham o mesmo endereço de email, Gerenciador de Armazenamento às vezes falha ao mostrar seus recursos no modo de exibição de árvore. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Em computadores com Windows mais lentos, a tela inicial às vezes leva uma quantidade significativa de tempo para aparecer. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* A caixa de diálogo Conectar seria exibida mesmo se houver contas ou serviços anexados. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Novo
* Os anexos de recursos externos, como para conexões SAS e emuladores, foram significativamente aprimorados. Agora você pode:
   * Personalize o nome de exibição do recurso que você está anexando. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexe a vários emuladores locais usando portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicionar recursos anexados ao acesso rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Gerenciador de Armazenamento agora dá suporte à exclusão reversível. Pode:
   * Configure uma política de exclusão reversível clicando com o botão direito do mouse no nó contêineres de BLOB para sua conta de armazenamento.
   * Exiba BLOBs com exclusão reversível no editor de blob selecionando "BLOBs ativos e excluídos" na lista suspensa ao lado da barra de navegação.
   * Restaurar BLOBs com exclusão reversível.

### <a name="fixes"></a>Contida
* A ação "definir configurações de CORS" não está mais disponível em contas de armazenamento Premium porque as contas de armazenamento Premium não dão suporte a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Agora há uma propriedade de assinatura de acesso compartilhado para serviços conectados à SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação "definir camada de acesso padrão" agora está disponível para contas de armazenamento de BLOBs e GPV2 que foram fixadas para acesso rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, Gerenciador de Armazenamento falha ao mostrar as contas de armazenamento clássicas. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao usar emuladores, como o emulador de armazenamento do Azure ou o azurite, você precisará fazer com que eles escutem as conexões em suas portas padrão. Caso contrário, Gerenciador de Armazenamento não será capaz de se conectar a eles.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, será necessário instalar o [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>1\.3.0 da versão
07/09/2018

### <a name="new"></a>Novo
* Agora há suporte para o acesso aos contêineres de $web usados por sites estáticos. Isso permite que você carregue e gerencie facilmente arquivos e pastas usados pelo seu site. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* A barra de aplicativos no macOS foi reorganizada. As alterações incluem um menu de arquivo, algumas alterações de tecla de atalho e vários novos comandos no menu do aplicativo. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* O ponto de extremidade de autoridade para entrar no Azure governo dos EUA foi alterado para https://login.microsoftonline.us/
* Acessibilidade: quando um leitor de tela está ativo, a navegação por teclado agora funciona com as tabelas usadas para exibir itens no lado direito. Você pode usar as teclas de direção para navegar em linhas e colunas, Enter para invocar as ações padrão, a tecla de menu de contexto para abrir o menu de contexto de um item e deslocar ou controlar para seleção asselect. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Contida
*  Em alguns computadores, os processos filho estavam demorando muito para serem iniciados. Quando isso acontecer, um erro "processo filho falhou ao iniciar oportunamente" seria exibido. O tempo alocado para um processo filho iniciar agora foi aumentado de 20 para 90 segundos. Se você ainda estiver afetado por esse problema, comente sobre o problema vinculado do GitHub. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Ao usar uma SAS que não tenha permissões de leitura, não foi possível carregar um blob grande. A lógica para upload foi modificada para funcionar neste cenário. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Definir o nível de acesso público para um contêiner removeria todas as políticas de acesso, e vice-versa. Agora, o nível de acesso público e as políticas de acesso são preservados ao definir um dos dois. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" foi truncado na caixa de diálogo Propriedades. Isso foi corrigido. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* O prefixo "Gerenciador de Armazenamento do Microsoft Azure-" estava ausente na caixa de diálogo Criar novo diretório. Isso foi corrigido. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Acessibilidade: a caixa de diálogo Adicionar entidade era difícil de navegar ao usar o VoiceOver. Foram feitas melhorias. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Acessibilidade: a cor da tela de fundo do botão Recolher/Expandir do painel Ações e propriedades era inconsistente com controles de interface do usuário semelhantes em Alto Contraste tema preto. A cor foi alterada. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Acessibilidade: em Alto Contraste tema preto, o estilo de foco para o botão ' X ' na caixa de diálogo Propriedades não estava visível. Isso foi corrigido. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Acessibilidade: as guias ações e propriedades estavam faltando vários valores do Aria que resultaram em uma experiência do leitor de tela média. Os valores ausentes do Aria agora foram adicionados. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Acessibilidade: nós de árvore recolhidos no lado esquerdo não estavam recebendo um valor de false expandido pelo Aria. Isso foi corrigido. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Problemas Conhecidos
* A desanexação de um recurso anexado via URI de SAS, como um contêiner de BLOB, pode causar um erro que impede que outros anexos sejam exibidos corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Consulte [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/537) para obter mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Azure Stack não dá suporte aos recursos a seguir e tentar usá-los enquanto trabalha com Azure Stack pode resultar em erros inesperados:
   * Partilhas de Ficheiros
   * Camadas de acesso
   * Exclusão reversível
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, será necessário instalar o [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>1\.2.0 da versão
06/12/2018

### <a name="new"></a>Novo
* Se Gerenciador de Armazenamento falha ao carregar assinaturas de apenas um subconjunto de seus locatários, todas as assinaturas carregadas com êxito serão mostradas junto com uma mensagem de erro específica para os locatários que falharam. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* No Windows, quando uma atualização está disponível, agora você pode optar por "atualizar ao fechar". Quando essa opção for escolhida, o instalador da atualização será executado depois que você fechar Gerenciador de Armazenamento. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* O instantâneo de restauração foi adicionado ao menu de contexto do editor de compartilhamento de arquivos ao exibir um instantâneo de compartilhamento de arquivos. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* O botão limpar fila agora está sempre habilitado. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* O suporte para entrar no ADFS Azure Stack foi reabilitado. Azure Stack versão 1804 ou superior é necessária. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Contida
* Se você exibir instantâneos para um compartilhamento de arquivos cujo nome era um prefixo de outro compartilhamento de arquivos na mesma conta de armazenamento, os instantâneos para o outro compartilhamento de arquivos também serão listados. Esse problema foi corrigido. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Quando anexado via SAS, a restauração de um arquivo de um instantâneo de compartilhamento de arquivos resultaria em um erro. Esse problema foi corrigido. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Ao exibir instantâneos para um blob, a ação promover instantâneo foi habilitada quando o blob de base e um único instantâneo foram selecionados. A ação agora estará habilitada apenas se um único instantâneo for selecionado. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Se um único trabalho (como baixar um blob) tiver sido iniciado e posteriormente falhar, ele não será repetido automaticamente até que você inicie outro trabalho do mesmo tipo. Todos os trabalhos agora devem ser repetidos automaticamente, independentemente de quantos trabalhos você enfileiraram.
* Editores abertos para contêineres de blob recém-criados em GPV2 e contas de armazenamento de BLOBs não tinham uma coluna de camada de acesso. Esse problema foi corrigido. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Algumas vezes, uma coluna de camada de acesso não apareceria quando uma conta de armazenamento ou contêiner de blob foi anexado via SAS. A coluna agora será sempre mostrada, mas com um valor vazio se não houver nenhuma camada de acesso definida. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* A definição da camada de acesso de um blob de blocos recém carregado foi desabilitada. Esse problema foi corrigido. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Se o botão "manter guia aberta" for chamado usando o teclado, o foco do teclado será perdido. Agora, o foco será movido para a guia que foi mantida aberta. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Para uma consulta na Construtor de Consultas, o VoiceOver não estava fornecendo uma descrição utilizável do operador atual. Agora é mais descritivo. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Os links de paginação para os vários editores não eram descritivos. Elas foram alteradas para serem mais descritivas. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* Na caixa de diálogo Adicionar entidade, a VoiceOver não anunciava a qual coluna um elemento de entrada faz parte. O nome da coluna atual agora está incluído na descrição do elemento. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Botões de opção e caixas de seleção não tinham uma borda visível quando concentrados. Esse problema foi corrigido. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Problemas Conhecidos
* Ao usar emuladores, como o emulador de armazenamento do Azure ou o azurite, você precisará fazer com que eles escutem as conexões em suas portas padrão. Caso contrário, Gerenciador de Armazenamento não será capaz de se conectar a eles.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, será necessário instalar o [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Versão 1.1.0
05/09/2018

### <a name="new"></a>Novo
* Gerenciador de Armazenamento agora dá suporte ao uso de azurite. Observação: a conexão com o azurite é codificada para os pontos de extremidade de desenvolvimento padrão.
* O Gerenciador de Armazenamento agora dá suporte a camadas de acesso somente para BLOB e contas de armazenamento GPV2. Saiba mais sobre as camadas de acesso [aqui](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
* Uma hora de início não é mais necessária ao gerar uma SAS.

### <a name="fixes"></a>Contida
* A recuperação de assinaturas para contas do governo dos EUA foi interrompida. Esse problema foi corrigido. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* O tempo de expiração das políticas de acesso não foi salvo corretamente. Esse problema foi corrigido. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Ao gerar uma URL SAS para um item em um contêiner, o nome do item não estava sendo anexado à URL. Esse problema foi corrigido. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Ao criar uma SAS, os tempos de expiração que estão no passado às vezes seriam o valor padrão. Isso ocorreu devido a Gerenciador de Armazenamento usando a hora de início e expiração da última utilização como valores padrão. Agora, toda vez que você abrir a caixa de diálogo SAS, um novo conjunto de valores padrão será gerado. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Ao copiar entre contas de armazenamento, uma SAS de 24 horas é gerada. Se a cópia duração mais de 24 horas, a cópia falhará. Aumentamos as SAS para a última semana para reduzir a chance de uma falha de cópia devido a uma SAS expirada. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Para algumas atividades, clicar em "Cancelar" nem sempre funcionaria. Esse problema foi corrigido. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Para algumas atividades, a velocidade de transferência estava errada. Esse problema foi corrigido. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* A ortografia de "anterior" no menu Exibir estava errada. Agora, ele está escrito corretamente. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* A página final do Windows Installer teve um botão "Avançar". Ele foi alterado para um botão "Concluir". [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* O foco da guia não estava visível para botões em caixas de diálogo ao usar o tema preto HC. Agora ele está visível. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* A capitalização de "resolução automática" para ações no log de atividades estava errada. Agora ele está correto. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Ao excluir uma entidade de uma tabela, a caixa de diálogo solicitando a confirmação exibiu um ícone de erro. A caixa de diálogo agora usa um ícone de aviso. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Problemas Conhecidos
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/. IdentityService/AadConfigurations. Se isso não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O azurite ainda não implementou totalmente todas as APIs de armazenamento. Por isso, pode haver erros ou comportamento inesperados ao usar o azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* O carregamento de sua pasta do OneDrive não funciona devido a um bug no NodeJS. O bug foi corrigido, mas ainda não está integrado ao seu próprio.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, será necessário instalar o [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Versão 1.0.0
04/16/2018

### <a name="new"></a>Novo
* Autenticação avançada que permite que Gerenciador de Armazenamento use o mesmo armazenamento de conta que o Visual Studio 2017. Para usar esse recurso, você precisará fazer logon novamente em suas contas e redefinir suas assinaturas filtradas.
* Para Azure Stack contas apoiadas pelo AAD, Gerenciador de Armazenamento recuperará Azure Stack assinaturas quando ' target Azure Stack ' estiver habilitado. Você não precisa mais criar um ambiente de logon personalizado.
* Vários atalhos foram adicionados para permitir uma navegação mais rápida. Isso inclui a alternância de vários painéis e a movimentação entre editores. Consulte o menu Exibir para obter mais detalhes.
* Gerenciador de Armazenamento comentários agora reside no GitHub. Você pode acessar nossa página de problemas clicando no botão de comentários na parte inferior esquerda ou acessando [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues). Sinta-se à vontade para fazer sugestões, relatar problemas, fazer perguntas ou deixar qualquer outra forma de comentários.
* Se você estiver executando problemas de certificado SSL e não conseguir localizar o certificado transgressor, agora poderá iniciar Gerenciador de Armazenamento na linha de comando com o sinalizador `--ignore-certificate-errors`. Quando iniciado com esse sinalizador, Gerenciador de Armazenamento ignorará os erros do certificado SSL.
* Agora há uma opção de ' download ' no menu de contexto para itens de BLOB e arquivo.
* Melhor suporte a acessibilidade e leitor de tela. Se você depender de recursos de acessibilidade, consulte nossa [documentação de acessibilidade](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) para obter mais informações.
* Gerenciador de Armazenamento agora usa o 1.8.3 de uso de digital

### <a name="breaking-changes"></a>Alterações Interruptivas
* Gerenciador de Armazenamento mudou para uma nova biblioteca de autenticação. Como parte da mudança para a biblioteca, você precisará fazer logon novamente em suas contas e redefinir suas assinaturas filtradas
* O método usado para criptografar dados confidenciais foi alterado. Isso pode resultar em alguns de seus itens de acesso rápido que precisam ser adicionados novamente e/ou alguns recursos anexados que precisam ser reanexados.

### <a name="fixes"></a>Contida
* Alguns usuários atrás de proxies teriam carregamentos de blob de grupo ou downloads interrompidos por uma mensagem de erro ' incapaz de resolver '. Esse problema foi corrigido.
* Se a entrada fosse necessária ao usar um link direto, clicar no prompt ' entrar ' exibiria uma caixa de diálogo em branco. Esse problema foi corrigido.
* No Linux, se Gerenciador de Armazenamento não puder iniciar devido a uma falha de processo de GPU, você será informado sobre a falha, instruído a usar a opção '--Disable-GPU ' e Gerenciador de Armazenamento será reiniciado automaticamente com a opção habilitada.
* Políticas de acesso inválidas eram difíceis de identificar na caixa de diálogo políticas de acesso. As IDs de política de acesso inválidas agora são descritas em vermelho para obter mais visibilidade.
* Às vezes, o log de atividades teria grandes áreas de espaço em branco entre as diferentes partes de uma atividade. Esse problema foi corrigido.
* No editor de consultas de tabela, se você deixou uma cláusula timestamp em um estado inválido e depois tentou Modificar outra cláusula, o editor congelaria. O editor agora irá restaurar a cláusula timestamp para seu último estado válido quando uma alteração em outra cláusula for detectada.
* Se você pausou enquanto digita em sua consulta de pesquisa no modo de exibição de árvore, a pesquisa começaria e o foco seria roubado da caixa de texto. Agora, você deve iniciar a pesquisa explicitamente pressionando a tecla ' Enter ' ou clicando no botão Iniciar pesquisa.
* O comando "obter assinatura de acesso compartilhado" às vezes seria desabilitado ao clicar com o botão direito do mouse em um arquivo em um compartilhamento de arquivos. Esse problema foi corrigido.
* Se o nó da árvore de recursos com foco foi filtrado durante a pesquisa, você não poderá fazer uma tabulação na árvore de recursos e usar as teclas de direção para navegar na árvore de recursos. Agora, se o nó da árvore de recursos focalizado estiver oculto, o primeiro nó na árvore de recursos será focalizado automaticamente.
* Um separador extra às vezes seria visível na barra de ferramentas do editor. Esse problema foi corrigido.
* Às vezes, a caixa de texto de navegação estrutural seria um estouro. Esse problema foi corrigido.
* Os editores de BLOB e compartilhamento de arquivos às vezes seriam atualizados constantemente ao carregar vários arquivos ao mesmo tempo. Esse problema foi corrigido.
* O recurso ' estatísticas de pasta ' não tinha nenhuma finalidade na exibição de gerenciamento de instantâneos de compartilhamento de arquivos. Agora ele foi desabilitado.
* No Linux, o menu arquivo não apareceu. Esse problema foi corrigido.
* Ao carregar uma pasta em um compartilhamento de arquivos, por padrão, somente o conteúdo da pasta foi carregado. Agora, o comportamento padrão é carregar o conteúdo da pasta em uma pasta correspondente no compartilhamento de arquivos.
* A ordem dos botões em várias caixas de diálogo foi revertida. Esse problema foi corrigido.
* Várias correções relacionadas à segurança.

### <a name="known-issues"></a>Problemas Conhecidos
* Em casos raros, o foco da árvore pode ficar preso no acesso rápido. Para restringir o foco, você pode atualizar tudo.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, será necessário instalar o [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>0\.9.6 da versão
02/28/2018

### <a name="fixes"></a>Contida
* Um problema impediu que os BLOBs/arquivos esperados fossem listados no editor. Esse problema foi corrigido.
* Um problema fazia a alternância entre exibições de instantâneo para exibir itens incorretamente. Esse problema foi corrigido.

### <a name="known-issues"></a>Problemas Conhecidos
* Gerenciador de Armazenamento não oferece suporte a contas do ADFS.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar assinaturas.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>0\.9.5 da versão
02/06/2018

### <a name="new"></a>Novo

* Suporte para instantâneos de compartilhamentos de arquivos:
    * Crie e gerencie instantâneos para seus compartilhamentos de arquivos.
    * Alterne facilmente exibições entre instantâneos de seus compartilhamentos de arquivos conforme você explora.
    * Restaurar versões anteriores de seus arquivos.
* Suporte de visualização para Azure Data Lake Store:
    * Conecte-se aos recursos do ADLS em várias contas.
    * Conecte-se a e compartilhe recursos do ADLS usando URIs ADL.
    * Execute operações básicas de arquivo/pasta recursivamente.
    * Fixe pastas individuais para acesso rápido.
    * Exibir estatísticas de pasta.

### <a name="fixes"></a>Contida
* Melhorias de desempenho de inicialização.
* Várias correções de bugs.

### <a name="known-issues"></a>Problemas Conhecidos
* Gerenciador de Armazenamento não oferece suporte a contas do ADFS.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar assinaturas.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Versão 0.9.4 e 0.9.3
01/21/2018

### <a name="new"></a>Novo
* Sua janela de Gerenciador de Armazenamento existente será reutilizada quando:
    * Abrindo links diretos gerados em Gerenciador de Armazenamento.
    * Abrindo Gerenciador de Armazenamento do Portal.
    * Abrir Gerenciador de Armazenamento da extensão de VS Code de armazenamento do Azure (em breve).
* Foi adicionada a capacidade de abrir uma nova janela de Gerenciador de Armazenamento de dentro Gerenciador de Armazenamento.
    * Para o Windows, há uma opção ' nova janela ' no menu arquivo e no menu de contexto da barra de tarefas.
    * Para Mac, há uma opção ' nova janela ' no menu do aplicativo.

### <a name="fixes"></a>Contida
* Corrigido um problema de segurança. Atualize para o 0.9.4 da sua primeira conveniência.
* As atividades antigas não estavam sendo corretamente limpas. Isso afetou o desempenho de trabalhos de longa execução. Agora eles estão sendo limpos corretamente.
* As ações que envolvem um grande número de arquivos e diretórios eventualmente fazem com que Gerenciador de Armazenamento congele. As solicitações para o Azure para compartilhamentos de arquivos agora são limitadas para limitar o uso de recursos do sistema.

### <a name="known-issues"></a>Problemas Conhecidos
* Gerenciador de Armazenamento não oferece suporte a contas do ADFS.
* As teclas de atalho para "Exibir Gerenciador" e "Exibir gerenciamento de conta" devem ser Ctrl/Cmd + Shift + e e Ctrl/Cmd + Shift + A respectivamente.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar assinaturas.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>0\.9.2 da versão
11/01/2017

### <a name="hotfixes"></a>Hotfixes
* Alterações de dados inesperadas foram possíveis ao editar valores EDM. DateTime para entidades de tabela, dependendo do fuso horário local. O editor agora usa uma caixa de texto sem formatação, fornecendo controle preciso e consistente sobre os valores EDM. DateTime.
* O carregamento/download de um grupo de BLOBs quando anexados ao nome e à chave não seriam iniciados. Esse problema foi corrigido.
* Anteriormente Gerenciador de Armazenamento solicitaria que você autenticasse novamente uma conta obsoleta se uma ou mais das assinaturas da conta fosse selecionada. Agora Gerenciador de Armazenamento solicitará que você mesmo se a conta estiver completamente filtrada.
* O domínio dos pontos de extremidade para o governo dos EUA do Azure estava errado. Ele foi corrigido.
* Às vezes, o botão aplicar no painel gerenciar contas era difícil de clicar. Isso não deve acontecer mais.

### <a name="new"></a>Novo
* Suporte de visualização para Azure Cosmos DB:
    * [Documentação online](./cosmos-db/storage-explorer.md)
    * Criar bancos de dados e coleções
    * Manipular dados
    * Consultar, criar ou excluir documentos
    * Atualizar procedimentos armazenados, funções definidas pelo usuário ou gatilhos
    * Usar cadeias de conexão para conectar e gerenciar seus bancos de dados
* Melhorou o desempenho do carregamento/download de muitos BLOBs pequenos.
* Adicionada uma ação "repetir tudo" se houver falhas em um grupo de carregamento de BLOB ou grupo de download de BLOB.
* Gerenciador de Armazenamento agora irá pausar a iteração durante o upload/download do blob se detectar que a conexão de rede foi perdida. Você pode retomar a iteração depois que a conexão de rede for restabelecida.
* Adicionada a capacidade de "fechar tudo", "fechar outras" e "fechar" guias por meio do menu de contexto.
* Gerenciador de Armazenamento agora usa caixas de diálogo nativas e menus de contexto nativos.
* Gerenciador de Armazenamento agora é mais acessível. Os aprimoramentos incluem:
    * Melhor suporte ao leitor de tela, para NVDA no Windows e para o VoiceOver no Mac
    * Melhoria na ti de alto contraste
    * Correções do teclado e de foco do teclado

### <a name="fixes"></a>Contida
* Se você tentou abrir ou baixar um blob com um nome de arquivo do Windows inválido, a operação falharia. Gerenciador de Armazenamento agora irá detectar se um nome de blob é inválido e perguntar se você gostaria de codificá-lo ou ignorar o blob. Gerenciador de Armazenamento também detectará se um nome de arquivo parece estar codificado e perguntar se você deseja decodificá-lo antes de carregá-lo.
* Durante o upload do blob, o editor do contêiner de blob de destino às vezes não seria corretamente atualizado. Esse problema foi corrigido.
* O suporte para várias formas de cadeias de conexão e URIs SAS foram regressivos. Abordamos todos os problemas conhecidos, mas envie comentários se você encontrar mais problemas.
* A notificação de atualização foi interrompida para alguns usuários no 0.9.0. Esse problema foi corrigido e, para aqueles afetados pelo Bug, você pode baixar manualmente a versão mais recente do Gerenciador de Armazenamento [aqui](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Problemas Conhecidos
* Gerenciador de Armazenamento não oferece suporte a contas do ADFS.
* As teclas de atalho para "Exibir Gerenciador" e "Exibir gerenciamento de conta" devem ser Ctrl/Cmd + Shift + e e Ctrl/Cmd + Shift + A respectivamente.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar assinaturas.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Versão 0.9.1 e 0.9.0
10/20/2017
### <a name="new"></a>Novo
* Suporte de visualização para Azure Cosmos DB:
    * [Documentação online](./cosmos-db/storage-explorer.md)
    * Criar bancos de dados e coleções
    * Manipular dados
    * Consultar, criar ou excluir documentos
    * Atualizar procedimentos armazenados, funções definidas pelo usuário ou gatilhos
    * Usar cadeias de conexão para conectar e gerenciar seus bancos de dados
* Melhorou o desempenho do carregamento/download de muitos BLOBs pequenos.
* Adicionada uma ação "repetir tudo" se houver falhas em um grupo de carregamento de BLOB ou grupo de download de BLOB.
* Gerenciador de Armazenamento agora irá pausar a iteração durante o upload/download do blob se detectar que a conexão de rede foi perdida. Você pode retomar a iteração depois que a conexão de rede for restabelecida.
* Adicionada a capacidade de "fechar tudo", "fechar outras" e "fechar" guias por meio do menu de contexto.
* Gerenciador de Armazenamento agora usa caixas de diálogo nativas e menus de contexto nativos.
* Gerenciador de Armazenamento agora é mais acessível. Os aprimoramentos incluem:
    * Melhor suporte ao leitor de tela, para NVDA no Windows e para o VoiceOver no Mac
    * Melhoria na ti de alto contraste
    * Correções do teclado e de foco do teclado

### <a name="fixes"></a>Contida
* Se você tentou abrir ou baixar um blob com um nome de arquivo do Windows inválido, a operação falharia. Gerenciador de Armazenamento agora irá detectar se um nome de blob é inválido e perguntar se você gostaria de codificá-lo ou ignorar o blob. Gerenciador de Armazenamento também detectará se um nome de arquivo parece estar codificado e perguntar se você deseja decodificá-lo antes de carregá-lo.
* Durante o upload do blob, o editor do contêiner de blob de destino às vezes não seria corretamente atualizado. Esse problema foi corrigido.
* O suporte para várias formas de cadeias de conexão e URIs SAS foram regressivos. Abordamos todos os problemas conhecidos, mas envie comentários se você encontrar mais problemas.
* A notificação de atualização foi interrompida para alguns usuários no 0.9.0. Esse problema foi corrigido e, para aqueles afetados pelo Bug, você pode baixar manualmente a versão mais recente do Gerenciador de Armazenamento [aqui](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Problemas Conhecidos
* Gerenciador de Armazenamento não oferece suporte a contas do ADFS.
* As teclas de atalho para "Exibir Gerenciador" e "Exibir gerenciamento de conta" devem ser Ctrl/Cmd + Shift + e e Ctrl/Cmd + Shift + A respectivamente.
* Ao direcionar Azure Stack, o carregamento de determinados arquivos como BLOBs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Isso ocorre porque estamos usando a solução de filtragem de cancelamento que é descrita aqui.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar assinaturas.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* O Shell de vídeo de equipamentos de vídeo usado pelo Gerenciador de Armazenamento tem problemas com uma aceleração de hardware de GPU (unidade de processamento gráfico). Se Gerenciador de Armazenamento estiver exibindo uma janela principal em branco (vazia), você poderá tentar iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>0\.8.16 da versão
8/21/2017

### <a name="new"></a>Novo
* Quando você abre um blob, Gerenciador de Armazenamento solicitará que você carregue o arquivo baixado se uma alteração for detectada
* Experiência de entrada de Azure Stack aprimorada
* Melhorou o desempenho do carregamento/download de muitos arquivos pequenos ao mesmo tempo


### <a name="fixes"></a>Contida
* Para alguns tipos de BLOB, escolher "substituir" durante um conflito de upload às vezes resultaria na reinicialização do carregamento.
* Na versão 0.8.15, os carregamentos às vezes seriam interrompidos em 99%.
* Ao carregar arquivos em um compartilhamento de arquivos, se você optar por carregar em um diretório que ainda não existia, o upload falhará.
* O Gerenciador de Armazenamento estava gerando incorretamente carimbos de data/hora para assinaturas de acesso compartilhado e consultas de tabela.


### <a name="known-issues"></a>Problemas Conhecidos
* O uso de uma cadeia de conexão de nome e chave não funciona atualmente. Ele será corrigido na próxima versão. Até lá, você pode usar Attach com nome e chave.
* Se você tentar abrir um arquivo com um nome de arquivo do Windows inválido, o download resultará em um erro de arquivo não encontrado.
* Depois de clicar em "Cancelar" em uma tarefa, pode levar algum tempo para que a tarefa seja cancelada. Essa é uma limitação da biblioteca de nós de armazenamento do Azure.
* Depois de concluir um carregamento de BLOB, a guia que iniciou o carregamento é atualizada. Essa é uma alteração do comportamento anterior e também fará com que você seja levado de volta para a raiz do contêiner no qual você está.
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que Gerenciador de Armazenamento se esqueça dessa decisão.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar assinaturas.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* Para os usuários no Ubuntu 14, 4, você precisará garantir que o GCC esteja atualizado. isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17, 4, você precisará instalar o GConf-isso pode ser feito executando os seguintes comandos e, em seguida, reiniciando o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>0\.8.14 da versão
06/22/2017

### <a name="new"></a>Novo

* Atualizada a versão do atual para 1.7.2 a fim de aproveitar várias atualizações de segurança críticas
* Agora você pode acessar rapidamente o guia de solução de problemas online no menu ajuda
* [Guia][2] de solução de problemas Gerenciador de armazenamento
* [Instruções][3] sobre como se conectar a uma assinatura do Azure Stack

### <a name="known-issues"></a>Problemas Conhecidos

* Os botões na caixa de diálogo de confirmação Excluir pasta não se registram com os cliques do mouse no Linux. solução alternativa é usar a tecla Enter
* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento esqueça a decisão
* Ter mais de três grupos de BLOBs ou arquivos carregando ao mesmo tempo pode causar erros
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar as assinaturas
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* A instalação do Ubuntu 14, 4 precisa de uma versão gcc atualizada ou atualizada – as etapas para atualizar estão abaixo:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>0\.8.13 da versão
05/12/2017

#### <a name="new"></a>Novo

* [Guia][2] de solução de problemas Gerenciador de armazenamento
* [Instruções][3] sobre como se conectar a uma assinatura do Azure Stack

#### <a name="fixes"></a>Contida

* Corrigido: o carregamento do arquivo teve uma alta chance de causar um erro de memória insuficiente
* Corrigido: agora você pode entrar com PIN/cartão inteligente
* Corrigido: abrir no Portal agora funciona com o Azure China 21Vianet, o Azure Alemanha, o governo dos EUA do Azure e o Azure Stack
* Corrigido: ao carregar uma pasta em um contêiner de BLOBs, um erro de "operação ilegal" às vezes ocorreria
* Corrigido: selecionar tudo foi desabilitado ao gerenciar instantâneos
* Corrigido: os metadados do blob de base podem ser substituídos após a exibição das propriedades de seus instantâneos

#### <a name="known-issues"></a>Problemas Conhecidos

* Se você escolher o PIN/certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento esqueça a decisão
* Embora seja ampliado ou reduzido, o nível de zoom pode ser redefinido momentaneamente para o nível padrão
* Ter mais de três grupos de BLOBs ou arquivos carregando ao mesmo tempo pode causar erros
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar as assinaturas
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* A instalação do Ubuntu 14, 4 precisa de uma versão gcc atualizada ou atualizada – as etapas para atualizar estão abaixo:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Versão 0.8.12 e 0.8.11 e 0.8.10
04/07/2017

#### <a name="new"></a>Novo

* Gerenciador de Armazenamento agora será fechado automaticamente quando você instalar uma atualização da notificação de atualização
* O acesso rápido in-loco fornece uma experiência aprimorada para trabalhar com seus recursos acessados com frequência
* No editor de contêiner de BLOB, agora você pode ver a qual máquina virtual um blob concedido pertence
* Agora você pode recolher o painel do lado esquerdo
* A descoberta agora é executada ao mesmo tempo que o download
* Usar estatísticas no contêiner de BLOB, compartilhamento de arquivos e editores de tabela para ver o tamanho de seu recurso ou seleção
* Agora você pode entrar no Azure Active Directory (AAD) com base em contas de Azure Stack.
* Agora você pode carregar arquivos mortos por 32 MB para contas de armazenamento Premium
* Suporte de acessibilidade aprimorado
* Agora você pode adicionar certificados SSL X. 509 codificados de base 64 confiáveis acessando editar-&gt; certificados SSL-&gt; importar certificados

#### <a name="fixes"></a>Contida

* Corrigido: depois de atualizar as credenciais de uma conta, a exibição de árvore às vezes não seria atualizada automaticamente
* Corrigido: a geração de uma SAS para filas e tabelas do emulador resultaria em uma URL inválida
* Corrigido: as contas de armazenamento Premium agora podem ser expandidas enquanto um proxy está habilitado
* Corrigido: o botão Aplicar na página de gerenciamento de contas não funcionaria se você tivesse 1 ou 0 contas selecionadas
* Corrigido: o upload de BLOBs que exigem resoluções de conflitos pode falhar – corrigido em 0.8.11
* Corrigido: o envio de comentários foi quebrado em 0.8.11-corrigido em 0.8.12

#### <a name="known-issues"></a>Problemas Conhecidos

* Depois de atualizar para o 0.8.10, você precisará atualizar todas as suas credenciais.
* Embora seja ampliado ou reduzido, o nível de zoom pode ser redefinido momentaneamente para o nível padrão.
* Ter mais de três grupos de BLOBs ou arquivos carregando ao mesmo tempo pode causar erros.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar as assinaturas.
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação.
* Embora Azure Stack atualmente não ofereça suporte a compartilhamentos de arquivos, um nó de compartilhamentos de arquivos ainda aparece em uma conta de armazenamento Azure Stack anexada.
* A instalação do Ubuntu 14, 4 precisa de uma versão gcc atualizada ou atualizada – as etapas para atualizar estão abaixo:

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

* Gerenciador de Armazenamento 0.8.9 baixará automaticamente a versão mais recente para atualizações.
* Hotfix: usar um URI SAS gerado pelo portal para anexar uma conta de armazenamento resultaria em um erro.
* Agora você pode criar, gerenciar e promover instantâneos de BLOB.
* Agora você pode entrar no Azure China 21Vianet, Azure Alemanha e contas do governo dos EUA do Azure.
* Agora você pode alterar o nível de zoom. Use as opções no menu Exibir para ampliar, reduzir e redefinir o zoom.
* Os caracteres Unicode agora têm suporte nos metadados do usuário para BLOBs e arquivos.
* Aprimoramentos de acessibilidade.
* As notas de versão da próxima versão podem ser exibidas na notificação de atualização. Você também pode exibir as notas de versão atuais no menu ajuda.

#### <a name="fixes"></a>Contida

* Corrigido: o número de versão agora é exibido corretamente no painel de controle no Windows
* Corrigido: a pesquisa não está mais limitada a 50.000 nós
* Corrigido: carregar em um compartilhamento de arquivos girado para sempre se o diretório de destino ainda não existir
* Corrigido: estabilidade aprimorada para carregamentos e downloads longos

#### <a name="known-issues"></a>Problemas Conhecidos

* Embora seja ampliado ou reduzido, o nível de zoom pode ser redefinido momentaneamente para o nível padrão.
* O acesso rápido funciona apenas com itens baseados em assinatura. Recursos locais ou recursos anexados por meio de chave ou token SAS não têm suporte nesta versão.
* Pode levar um rápido acesso a alguns segundos para navegar até o recurso de destino, dependendo de quantos recursos você tem.
* Ter mais de três grupos de BLOBs ou arquivos carregando ao mesmo tempo pode causar erros.

12/16/2016
### <a name="version-087"></a>0\.8.7 da versão

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Novo

* Você pode escolher como resolver conflitos no início de uma atualização, baixar ou copiar sessão na janela atividades
* Passe o mouse sobre uma guia para ver o caminho completo do recurso de armazenamento
* Quando você clica em uma guia, ela é sincronizada com seu local no painel de navegação do lado esquerdo

#### <a name="fixes"></a>Contida

* Corrigido: Gerenciador de Armazenamento agora é um aplicativo confiável no Mac
* Corrigido: o Ubuntu 14, 4 tem suporte novamente
* Corrigido: às vezes, a interface do usuário da conta de adição pisca ao carregar assinaturas
* Corrigido: às vezes, nem todos os recursos de armazenamento foram listados no painel de navegação do lado esquerdo
* Corrigido: o painel de ações às vezes exibiu ações vazias
* Corrigido: o tamanho da janela da última sessão fechada agora é mantido
* Corrigido: você pode abrir várias guias para o mesmo recurso usando o menu de contexto

#### <a name="known-issues"></a>Problemas Conhecidos

* O acesso rápido funciona apenas com itens baseados em assinatura. Recursos locais ou recursos anexados por meio de chave ou token SAS não têm suporte nesta versão
* Pode levar um rápido acesso a alguns segundos para navegar até o recurso de destino, dependendo de quantos recursos você tem
* Ter mais de três grupos de BLOBs ou arquivos carregando ao mesmo tempo pode causar erros
* Identificadores de pesquisa pesquisando em aproximadamente 50.000 nós-depois disso, o desempenho pode ser afetado ou pode causar uma exceção sem tratamento
* Pela primeira vez usando o Gerenciador de Armazenamento no macOS, você poderá ver vários prompts solicitando permissão do usuário para acessar o conjunto de chaves. Sugerimos que você selecione sempre permitir para que o prompt não apareça novamente

11/18/2016
### <a name="version-086"></a>0\.8.6 da versão

#### <a name="new"></a>Novo

* Agora você pode fixar os serviços usados com mais frequência ao acesso rápido para facilitar a navegação
* Agora você pode abrir vários editores em guias diferentes. Clique único para abrir uma guia temporária; Clique duas vezes para abrir uma guia permanente. Você também pode clicar na guia temporária para torná-la uma guia permanente
* Fizemos melhorias de desempenho e estabilidade perceptíveis para uploads e downloads, especialmente para arquivos grandes em computadores rápidos
* Pastas "virtuais" vazias agora podem ser criadas em contêineres de BLOB
* Introduzimos novamente a pesquisa com escopo com nossa nova pesquisa avançada de subcadeias de caracteres, portanto, agora você tem duas opções para Pesquisar:
    * Pesquisa global – basta inserir um termo de pesquisa na caixa de texto de pesquisa
    * Pesquisa com escopo-clique no ícone de lupa ao lado de um nó e, em seguida, adicione um termo de pesquisa ao final do caminho, ou clique com o botão direito do mouse e selecione "Pesquisar aqui"
* Adicionamos vários temas: claro (padrão), escuro, Alto Contraste preto e Alto Contraste branco. Acesse os temas de editar-&gt; para alterar sua preferência de temas
* Você pode modificar propriedades de BLOB e arquivo
* Agora há suporte para mensagens de fila codificadas (Base64) e não codificadas
* No Linux, um sistema operacional de 64 bits agora é necessário. Para esta versão, damos suporte apenas a 64-bit Ubuntu 16.04.1 LTS
* Atualizamos nosso logotipo!

#### <a name="fixes"></a>Contida

* Corrigido: tela congelando problemas
* Corrigido: segurança aprimorada
* Corrigido: às vezes, podem aparecer contas anexadas duplicadas
* Corrigido: um blob com um tipo de conteúdo indefinido pode gerar uma exceção
* Corrigido: não foi possível abrir o painel de consulta em uma tabela vazia
* Corrigido: varia bugs na pesquisa
* Corrigido: aumentou o número de recursos carregados de 50 para 100 ao clicar em "carregar mais"
* Corrigido: na primeira execução, se uma conta estiver conectada, agora selecionaremos todas as assinaturas para essa conta por padrão

#### <a name="known-issues"></a>Problemas Conhecidos

* Esta versão do Gerenciador de Armazenamento não é executada no Ubuntu 14, 4
* Para abrir várias guias para o mesmo recurso, não clique continuamente no mesmo recurso. Clique em outro recurso e volte e clique no recurso original para abri-lo novamente em outra guia
* O acesso rápido funciona apenas com itens baseados em assinatura. Recursos locais ou recursos anexados por meio de chave ou token SAS não têm suporte nesta versão
* Pode levar um rápido acesso a alguns segundos para navegar até o recurso de destino, dependendo de quantos recursos você tem
* Ter mais de três grupos de BLOBs ou arquivos carregando ao mesmo tempo pode causar erros
* Identificadores de pesquisa pesquisando em aproximadamente 50.000 nós-depois disso, o desempenho pode ser afetado ou pode causar uma exceção sem tratamento

10/03/2016
### <a name="version-085"></a>0\.8.5 da versão

#### <a name="new"></a>Novo

* Agora pode usar chaves SAS geradas pelo portal para anexar a recursos e contas de armazenamento

#### <a name="fixes"></a>Contida

* Corrigido: a condição de corrida durante a pesquisa às vezes fazia com que os nós se tornassem não expansíveis
* Corrigido: "usar HTTP" não funciona ao se conectar a contas de armazenamento com o nome e a chave da conta
* Corrigido: chaves SAS (especialmente geradas pelo portal) retornam um erro de "barra à direita"
* Corrigido: problemas de importação de tabela
    * Algumas vezes a chave de partição e a chave de linha foram revertidas
    * Não é possível ler chaves de partição "nulas"

#### <a name="known-issues"></a>Problemas Conhecidos

* Pesquisar identificadores pesquisando em aproximadamente 50.000 nós-depois disso, o desempenho pode ser afetado
* O Azure Stack atualmente não dá suporte a arquivos, portanto, tentar expandir arquivos mostrará um erro

09/12/2016
### <a name="version-084"></a>0\.8.4 da versão

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Novo

* Gere links diretos para contas de armazenamento, contêineres, filas, tabelas ou compartilhamentos de arquivos para compartilhamento e acesso fácil a seus recursos-suporte a Windows e Mac OS
* Pesquise seus contêineres de BLOB, tabelas, filas, compartilhamentos de arquivos ou contas de armazenamento na caixa de pesquisa
* Agora você pode agrupar cláusulas no construtor de consultas de tabela
* Renomear e copiar/colar contêineres de BLOB, compartilhamentos de arquivos, tabelas, BLOBs, pastas de BLOBs, arquivos e diretórios de dentro de contêineres e contas anexadas por SAS
* Renomear e copiar contêineres de BLOB e compartilhamentos de arquivos agora preservam Propriedades e metadados

#### <a name="fixes"></a>Contida

* Corrigido: não é possível editar entidades de tabela se elas contiverem Propriedades boolianas ou binárias

#### <a name="known-issues"></a>Problemas Conhecidos

* Pesquisar identificadores pesquisando em aproximadamente 50.000 nós-depois disso, o desempenho pode ser afetado

08/03/2016
### <a name="version-083"></a>0\.8.3 da versão

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Novo

* Renomear contêineres, tabelas, compartilhamentos de arquivos
* Experiência do construtor de consultas aprimorada
* Capacidade de salvar e carregar consultas
* Links diretos para contas de armazenamento ou contêineres, filas, tabelas ou compartilhamentos de arquivos para compartilhamento e acesso fácil a seus recursos (somente Windows-suporte a macOS em breve!)
* Capacidade de gerenciar e configurar regras de CORS

#### <a name="fixes"></a>Contida

* Corrigido: as contas da Microsoft exigem nova autenticação a cada 8-12 horas

#### <a name="known-issues"></a>Problemas Conhecidos

* Às vezes, a interface do usuário pode parecer congelada-maximizar a janela ajuda a resolver esse problema
* a instalação do macOS pode exigir permissões elevadas
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar as assinaturas
* Renomear compartilhamentos de arquivos, contêineres de BLOB e tabelas não preserva metadados ou outras propriedades no contêiner, como cota de compartilhamento de arquivos, nível de acesso público ou políticas de acesso
* Renomear BLOBs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados para BLOBs, arquivos e entidades são preservados durante uma renomeação
* A cópia ou renomeação de recursos não funciona em contas anexadas por SAS

07/07/2016
### <a name="version-082"></a>0\.8.2 da versão

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Novo

* As contas de armazenamento são agrupadas por assinaturas; o armazenamento de desenvolvimento e os recursos anexados via chave ou SAS são mostrados no nó (local e anexado)
* Sair de contas no painel "configurações de conta do Azure"
* Definir configurações de proxy para habilitar e gerenciar a entrada
* Criar e interromper concessões de BLOB
* Abrir contêineres de BLOB, filas, tabelas e arquivos com um único clique

#### <a name="fixes"></a>Contida

* Corrigido: mensagens de fila inseridas com bibliotecas .NET ou Java não são decodificadas corretamente de Base64
* Corrigido: tabelas de $metrics não são mostradas para contas de armazenamento de BLOBs
* Corrigido: o nó tabelas não funciona para armazenamento local (desenvolvimento)

#### <a name="known-issues"></a>Problemas Conhecidos

* a instalação do macOS pode exigir permissões elevadas

06/15/2016
### <a name="version-080"></a>0\.8.0 da versão

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Novo

* Suporte a compartilhamento de arquivos: exibindo, carregando, baixando, copiando arquivos e diretórios, URIs SAS (criar e conectar)
* Experiência do usuário aprimorada para se conectar ao armazenamento com URIs SAS ou chaves de conta
* Exportar resultados da consulta de tabela
* Reordenação e personalização de coluna de tabela
* Exibindo $logs contêineres de BLOB e tabelas de $metrics para contas de armazenamento com métricas habilitadas
* Melhor comportamento de exportação e importação, agora inclui o tipo de valor de propriedade

#### <a name="fixes"></a>Contida

* Corrigido: carregar ou baixar BLOBs grandes pode resultar em uploads/downloads incompletos
* Corrigido: editar, adicionar ou importar uma entidade com um valor de cadeia de caracteres numérico ("1") irá convertê-lo em duplo
* Corrigido: não é possível expandir o nó de tabela no ambiente de desenvolvimento local

#### <a name="known-issues"></a>Problemas Conhecidos

* $metrics tabelas não são visíveis para contas de armazenamento de BLOBs
* Mensagens de fila adicionadas programaticamente podem não ser exibidas corretamente se as mensagens são codificadas usando a codificação Base64

05/17/2016
### <a name="version-07201605090"></a>0\.7.20160509.0 da versão

#### <a name="new"></a>Novo

* Melhor tratamento de erros para falhas do aplicativo

#### <a name="fixes"></a>Contida

* Corrigido o bug em que as mensagens da barra de erros às vezes não aparecem quando as credenciais de entrada eram necessárias

#### <a name="known-issues"></a>Problemas Conhecidos

* Tabelas: adicionando, editando ou importando uma entidade que tem uma propriedade com um valor numérico ambíguo, como "1" ou "1,0", e o usuário tenta enviá-lo como um `Edm.String`, o valor voltará pela API do cliente como um EDM. Double

03/31/2016

### <a name="version-07201603250"></a>0\.7.20160325.0 da versão

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Novo

* Suporte a tabelas: exibição, consulta, exportação, importação e operações CRUD para entidades
* Suporte à fila: exibindo, adicionando, recolocando mensagens na fila
* Gerando URIs SAS para contas de armazenamento
* Conectando-se a contas de armazenamento com URIs SAS
* Atualizar notificações para atualizações futuras para Gerenciador de Armazenamento
* Aparência atualizada

#### <a name="fixes"></a>Contida

* Melhorias de desempenho e confiabilidade

### <a name="known-issues-amp-mitigations"></a>Problemas conhecidos &amp; mitigações

* O download de arquivos de blob grandes não funciona corretamente-é recomendável usar AzCopy enquanto resolvemos esse problema
* As credenciais da conta não serão recuperadas nem armazenadas em cache se a pasta base não puder ser encontrada ou não puder ser gravada
* Se estivermos adicionando, editando ou importando uma entidade que tem uma propriedade com um valor numérico ambíguo, como "1" ou "1,0", e o usuário tentar enviá-la como um `Edm.String`, o valor voltará pela API do cliente como um EDM. Double
* Ao importar arquivos CSV com registros de várias linhas, os dados podem ser cortadosdos ou embaralhados

02/03/2016

### <a name="version-07201601291"></a>0\.7.20160129.1 da versão

#### <a name="fixes"></a>Contida

* Desempenho geral melhorado ao carregar, baixar e copiar BLOBs

01/14/2016

### <a name="version-07201601050"></a>0\.7.20160105.0 da versão

#### <a name="new"></a>Novo

* Suporte para Linux (recursos de paridade para OSX)
* Adicionar contêineres de blob com chave de SAS (assinaturas de acesso compartilhado)
* Adicionar contas de armazenamento para o Azure China 21Vianet
* Adicionar contas de armazenamento com pontos de extremidade personalizados
* Abrir e exibir os blobs de texto e imagem de conteúdo
* Exibir e editar propriedades de BLOB e metadados

#### <a name="fixes"></a>Contida

* Corrigido: carregar ou baixar um grande número de BLOBs (mais de 500) pode, às vezes, fazer com que o aplicativo tenha uma tela branca
* Corrigido: ao definir o nível de acesso público do contêiner de BLOB, o novo valor não é atualizado até que você redefina o foco no contêiner. Além disso, a caixa de diálogo sempre usa como padrão "sem acesso público", e não o valor atual real.
* Melhor suporte geral para teclado/acessibilidade e interface do usuário
* O texto do histórico de trilhas é quebrado quando é longo com espaço em branco
* Caixa de diálogo SAS dá suporte à validação de entrada
* O armazenamento local continua disponível mesmo que as credenciais do usuário tenham expirado
* Quando um contêiner de blob aberto é excluído, o Gerenciador de blob no lado direito é fechado

#### <a name="known-issues"></a>Problemas Conhecidos

* A instalação do Linux precisa de uma versão gcc atualizada ou atualizada – as etapas para atualizar estão abaixo:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>0\.7.20151116.0 da versão

#### <a name="new"></a>Novo

* versões do macOS e do Windows
* Entre para exibir suas contas de armazenamento – Use sua conta da org, conta da Microsoft, 2FA, etc.
* Armazenamento de desenvolvimento local (use o emulador de armazenamento, somente Windows)
* Azure Resource Manager e suporte a recursos clássicos
* Criar e excluir BLOBs, filas ou tabelas
* Pesquisar por BLOBs, filas ou tabelas específicas
* Explorar o conteúdo dos contêineres de BLOB
* Exibir e navegar pelos diretórios
* Carregar, baixar e excluir BLOBs e pastas
* Exibir e editar propriedades de BLOB e metadados
* Gerar chaves SAS
* Gerenciar e criar políticas de acesso armazenado (SAP)
* Pesquisar blobs por prefixo
* Arraste ' n soltar arquivos para carregar ou baixar

#### <a name="known-issues"></a>Problemas Conhecidos

* Ao definir o nível de acesso público do contêiner de BLOB, o novo valor não é atualizado até que você redefina o foco no contêiner
* Quando você abre a caixa de diálogo para definir o nível de acesso público, ela sempre mostra "sem acesso público" como o padrão e não o valor atual real
* Não é possível renomear BLOBs baixados
* Às vezes, as entradas do log de atividades ficam "presas" em um estado em andamento quando ocorre um erro e o erro não é exibido
* Às vezes falha ou fica completamente branco ao tentar carregar ou baixar um grande número de BLOBs
* Às vezes, cancelar uma operação de cópia não funciona
* Durante a criação de um contêiner (blob/fila/tabela), se você inserir um nome inválido e continuar a criar outro em um tipo de contêiner diferente, não será possível definir o foco no novo tipo
* Não é possível criar nova pasta ou renomear pasta




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
