---
title: Notas de versão do Explorador de armazenamento do Microsoft Azure
description: Notas de versão do Explorador de armazenamento do Microsoft Azure
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
ms.openlocfilehash: aa3f2bc23b731f1e2c02d84edd6079debfbae134
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57727268"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Notas de versão do Explorador de armazenamento do Microsoft Azure

Este artigo contém as notas de versão para versão de Explorador de armazenamento do Azure 1.4.3, bem como as notas de versão para versões anteriores.

[Explorador de armazenamento do Microsoft Azure](./vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados de armazenamento do Azure no Windows, macOS e Linux.

## <a name="version-170"></a>Versão 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Transfira o Explorador de armazenamento do Azure 1.7.0
- [Explorador de armazenamento do Azure 1.7.0 para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Explorador de armazenamento do Azure 1.7.0 para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Explorador de armazenamento do Azure 1.7.0 para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Novo

* Agora pode alterar o proprietário e o grupo proprietário ao gerir o acesso para um contentor de geração 2 do ADLS, ficheiro ou pasta.
* No Windows, a atualização Explorador de armazenamento a partir de dentro do produto é agora uma instalação incremental. Isso deve resultar numa experiência de atualização mais rápida. Se preferir uma instalação limpa, então pode baixar o [instalador](https://azure.microsoft.com/en-us/features/storage-explorer/) por conta própria e, em seguida, instale manualmente. #1089

### <a name="preview-features"></a>Funcionalidades de Pré-visualização

* Sessão de fluxo de código de dispositivo no está agora disponível para pré-visualização. Para ativá-lo, aceda a "Pré-visualização" → "Utilização dispositivo código fluxo de início de sessão". Nós o encorajamos qualquer usuário que tenha tido problemas com o windows de início de sessão em branco para experimentar esta funcionalidade, como ele pode se revelar um formulário mais confiável de início de sessão. #938
* Explorador de armazenamento integrado com o AzCopy está atualmente disponível para pré-visualização. Para ativá-lo, aceda a "Pré-visualização" → "Use AzCopy para melhorada Blob carregar e transferir". As transferências de blob concluídas com o AzCopy devem ser mais rápidas e um melhor desempenho.

### <a name="fixes"></a>Correções

* Agora, pode escolher o tipo de blob que pretende carregar como quando o AzCopy é ativado. #1111
* Anteriormente, se tinha ativado os Web sites estáticos para uma conta de armazenamento do ADLS geração 2 e, em seguida, anexado-lo com o nome e a chave, Explorador de armazenamento seria não ter detetado que esse espaço de nomes hierárquico foi ativado. Isto foi corrigido. #1081
* No editor de blob, ordenar por Estado restante de dias de retenção ou foi interrompida. Isto foi corrigido. #1106
* Após 1.5.0, o Explorador de armazenamento já não espera para cópias de lado do servidor concluir antes de o relatório de sucesso durante uma mudança de nome ou copiar e colar. Isto foi corrigido. #976
* Quando utilizar a funcionalidade experimental do AzCopy, o comando copiado depois de clicar em "Copiar o comando para área de transferência" nem sempre era executável por conta própria. Agora, serão copiados todos os comandos necessários para executar a transferência manualmente. #1079
* Anteriormente, os blobs de geração 2 do ADLS não estavam acessíveis a se estivesse atrás de um proxy. Isto foi devido a um erro numa nova biblioteca de rede utilizado pelo SDK de armazenamento. 1.7.0, foi efetuada uma tentativa para atenuar este problema, mas algumas pessoas podem continuar a ver problemas. Será lançada uma correção completa numa atualização futura. #1090
* No 1.7.0, salvar caixa de diálogo arquivo agora corretamente se lembra tiver guardado um ficheiro para a localização do último. #16
* No painel de propriedades, o escalão de SKU de uma conta de armazenamento foi a ser apresentado como um tipo da conta. Isto foi corrigido. #654
* Às vezes, foi impossível interromper a concessão de um blob, mesmo que introduziu corretamente o nome do blob. Isto foi corrigido. #1070

### <a name="known-issues"></a>Problemas Conhecidos

* Ao utilizar o RBAC, o Explorador de armazenamento requer algumas permissões de camada de gestão para poder aceder aos seus recursos de armazenamento. Consulte a [guia de resolução de problemas](https://docs.microsoft.com/en-us/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* A tentar aceder a Blobs de geração 2 do ADLS quando, por trás de um proxy poderá falhar.
* A desanexar a partir de um recurso ligado através do URI de SAS, por exemplo, um contentor de BLOBs, pode fazer com que um erro que impede outros anexos de aparecer corretamente. Para contornar este problema, apenas Atualize o nó do grupo. Ver #537 para obter mais informações.
* A desanexar a partir de um recurso ligado através do URI de SAS, por exemplo, um contentor de BLOBs, pode fazer com que um erro que impede outros anexos de aparecer corretamente. Para contornar este problema, apenas Atualize o nó do grupo. Para obter mais informações, consulte #537.
* Se utilizar o VS para Mac e alguma vez criou uma configuração AAD personalizada, pode não ser possível para início de sessão. Para contornar o problema, elimine o conteúdo de ~ /. IdentityService/AadConfigurations. Se isso não desbloquear, comentário sobre este problema.
* Azurite ainda não totalmente implementada todas as APIs de armazenamento. Por este motivo, talvez haja erros inesperados ou comportamento quando utilizar Azurite para o armazenamento de desenvolvimento.
* Em casos raros, o foco de árvore pode ficar bloqueado no acesso rápido. Para unstick o foco, pode atualizar tudo.
* Carregar a partir de pasta do OneDrive não funcionar devido a um bug em NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Bombardeador. Para contornar este problema ao carregar para ou transferir a partir de um contentor de BLOBs, pode utilizar a funcionalidade experimental do AzCopy.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito aqui.
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* O Azure Stack não suporta as seguintes funcionalidades. Recursos tentando usar esses recursos ao trabalhar com o Azure Stack podem resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação de forma recuperável
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="previous-releases"></a>Versões anteriores

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

## <a name="version-162"></a>Versão 1.6.2
1/9/2019

### <a name="hotfixes"></a>Correções
* No 1.6.1, entidades adicionadas para ACLs de geração 2 do ADLS por ObjectId que não eram os usuários sempre foram adicionadas como grupos. Agora, apenas os grupos são adicionados como grupos e entidades como andService de aplicações empresariais que principais são adicionados como os utilizadores. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Se uma conta de armazenamento do ADLS Gen2 tinha não existem contentores e foi ligada com o nome e a chave, em seguida, o Explorador de armazenamento seria não detetar que a conta de armazenamento foi ADLS Gen2. Isto foi corrigido. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* No 1.6.0, conflitos durante a copiar e colar não seriam pedir uma resolução. Em vez disso, a cópia em conflito simplesmente falhava. Agora, sobre o conflito primeiro, será solicitado como gostaria que ele seja resolvido. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Devido a limitações de API, validação de todos os de ObjectIds na caixa de diálogo Gerir o acesso foram desativadas. Agora, validação situação ocorrerá apenas para o utilizador UPNs. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* Na caixa de diálogo de geração 2 do ADLS gerir o acesso, não foi possível modificar as permissões para um grupo. Isto foi corrigido. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Foi adicionado arrastar e soltar carregar o suporte para o editor de geração 2 do ADLS. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* A propriedade de URL na caixa de diálogo Propriedades de geração 2 do ADLS ficheiros e pastas, às vezes, estava em falta um '/'. Isto foi corrigido. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Se a obter as permissões atuais para um contentor de geração 2 do ADLS, ficheiro ou pasta falha, então o erro agora é propertly apresentado no registo de atividades. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* O caminho temporário, criado para abrir ficheiros tem foram abreviado para reduzir a possibilidade de criar um caminho que é mais de MAX_PATH no Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* A caixa de diálogo Connect agora corretamente é apresentado quando não foram encontrados utilizadores com sessão iniciada e não existem recursos que tenham sido vinculados. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* 1.6.0, a guardar as propriedades de ficheiros e Blobs de HNS seria codificar o valor de cada propriedade. Isso resultou em codificação desnecessários de valores que continha apenas carateres ASCII. Agora, só irão ser codificados valores se contiverem carateres não ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Carregar uma pasta para um contentor de BLOBs não HNS falha se foi utilizada uma SAS e a SAS não tem as permissões de leitura. Isto foi corrigido. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* A cancelar uma transferência de AzCopy não funcionava. Isto foi corrigido. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy Falha ao tentar baixar uma pasta de um contentor de BLOBs de geração 2 do ADLS, se a pasta tivesse espaços no nome. Isto foi corrigido. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* O editor do cosmos DB foi dividido em 1.6.0. Agora é fixa. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Novo

* Agora pode utilizar o Explorador de armazenamento para aceder aos dados de BLOBs por meio [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se tem sessão iniciada e o Explorador de armazenamento é não é possível obter as chaves para a sua conta de armazenamento, em seguida, um token de OAuth será utilizado para autenticar ao interagir com os seus dados.
* Explorador de armazenamento agora suporta contas de armazenamento de geração 2 do ADLS. Quando o Explorador de armazenamento detecta esse espaço de nomes hierárquico está ativado para uma conta de armazenamento, irá ver "(pré-visualização de geração 2 ADLS)" junto ao nome da conta de armazenamento. Explorador de armazenamento é capaz de detetar se é ou não espaço de nomes hierárquico é ativado quando tem sessão iniciada, ou se tiver ligado a conta de armazenamento com o nome e a chave. Para contas de armazenamento do ADLS geração 2, pode utilizar o Explorador de armazenamento:
    * Criar e eliminar contentores
    * Gerir as propriedades do contentor e permissões (lado esquerdo)
    * Exibir e navegar dados dentro de contentores
    * Criar novas pastas
    * Carregar, transferir, mudar o nome e eliminar ficheiros e pastas
    * Gerir as propriedades de ficheiros e pastas e permissões (lado direito).
    
    Outras funcionalidades de Blob típicas, como a eliminação de forma recuperável e instantâneos, não estão atualmente disponíveis. Gerenciamento de permissões também só está disponível quando conectado. Além disso, ao trabalhar numa conta de armazenamento de geração 2 do ADLS, Explorador de armazenamento irá utilizar o AzCopy para todos os carregamentos e transferências e predefinido para utilizar o nome e as credenciais da chave para todas as operações se estiver disponível.
* Depois de comentários do usuário forte, break lease mais uma vez pode ser utilizado para dividir as concessões em vários blobs ao mesmo tempo.

### <a name="known-issues"></a>Problemas Conhecidos

* Ao transferir a partir de uma conta de armazenamento do ADLS geração 2, se um dos ficheiros a serem transferidos já existir, o AzCopy, às vezes, irá falhar. Isso será corrigido numa futura correção.
* A desanexar a partir de um recurso ligado através do URI de SAS, por exemplo, um contentor de BLOBs, pode fazer com que um erro que impede outros anexos de aparecer corretamente. Para contornar este problema, apenas Atualize o nó do grupo. Para obter mais informações, consulte #537.
* Se utilizar o VS para Mac e alguma vez criou uma configuração AAD personalizada, pode não ser possível para início de sessão. Para contornar o problema, elimine o conteúdo de ~ /. IdentityService/AadConfigurations. Se isso não desbloquear, comentário sobre este problema.
* Azurite ainda não totalmente implementada todas as APIs de armazenamento. Por este motivo, talvez haja erros inesperados ou comportamento quando utilizar Azurite para o armazenamento de desenvolvimento.
* Em casos raros, o foco de árvore pode ficar bloqueado no acesso rápido. Para unstick o foco, pode atualizar tudo.
* Carregar a partir de pasta do OneDrive não funcionar devido a um bug em NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Bombardeador. Para contornar este problema ao carregar para ou transferir a partir de um contentor de BLOBs, pode utilizar a funcionalidade experimental do AzCopy.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito aqui.
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* O Azure Stack não suporta as seguintes funcionalidades. Recursos tentando usar esses recursos ao trabalhar com o Azure Stack podem resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação de forma recuperável
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>Versão 1.6.1
12/18/2018

### <a name="hotfixes"></a>Correções
* Devido a limitações de API, validação de todos os de ObjectIds na caixa de diálogo Gerir o acesso foram desativadas. Agora, validação situação ocorrerá apenas para o utilizador UPNs. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* Na caixa de diálogo de geração 2 do ADLS gerir o acesso, não foi possível modificar as permissões para um grupo. Isto foi corrigido. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Foi adicionado arrastar e soltar carregar o suporte para o editor de geração 2 do ADLS. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* A propriedade de URL na caixa de diálogo Propriedades de geração 2 do ADLS ficheiros e pastas, às vezes, estava em falta um '/'. Isto foi corrigido. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Se a obter as permissões atuais para um contentor de geração 2 do ADLS, ficheiro ou pasta falha, então o erro agora é propertly apresentado no registo de atividades. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* O caminho temporário, criado para abrir ficheiros tem foram abreviado para reduzir a possibilidade de criar um caminho que é mais de MAX_PATH no Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* A caixa de diálogo Connect agora corretamente é apresentado quando não foram encontrados utilizadores com sessão iniciada e não existem recursos que tenham sido vinculados. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* 1.6.0, a guardar as propriedades de ficheiros e Blobs de HNS seria codificar o valor de cada propriedade. Isso resultou em codificação desnecessários de valores que continha apenas carateres ASCII. Agora, só irão ser codificados valores se contiverem carateres não ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Carregar uma pasta para um contentor de BLOBs não HNS falha se foi utilizada uma SAS e a SAS não tem as permissões de leitura. Isto foi corrigido. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* A cancelar uma transferência de AzCopy não funcionava. Isto foi corrigido. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy Falha ao tentar baixar uma pasta de um contentor de BLOBs de geração 2 do ADLS, se a pasta tivesse espaços no nome. Isto foi corrigido. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* O editor do cosmos DB foi dividido em 1.6.0. Agora é fixa. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Novo

* Agora pode utilizar o Explorador de armazenamento para aceder aos dados de BLOBs por meio [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se tem sessão iniciada e o Explorador de armazenamento é não é possível obter as chaves para a sua conta de armazenamento, em seguida, um token de OAuth será utilizado para autenticar ao interagir com os seus dados.
* Explorador de armazenamento agora suporta contas de armazenamento de geração 2 do ADLS. Quando o Explorador de armazenamento detecta esse espaço de nomes hierárquico está ativado para uma conta de armazenamento, irá ver "(pré-visualização de geração 2 ADLS)" junto ao nome da conta de armazenamento. Explorador de armazenamento é capaz de detetar se é ou não espaço de nomes hierárquico é ativado quando tem sessão iniciada, ou se tiver ligado a conta de armazenamento com o nome e a chave. Para contas de armazenamento do ADLS geração 2, pode utilizar o Explorador de armazenamento:
    * Criar e eliminar contentores
    * Gerir as propriedades do contentor e permissões (lado esquerdo)
    * Exibir e navegar dados dentro de contentores
    * Criar novas pastas
    * Carregar, transferir, mudar o nome e eliminar ficheiros e pastas
    * Gerir as propriedades de ficheiros e pastas e permissões (lado direito).
    
    Outras funcionalidades de Blob típicas, como a eliminação de forma recuperável e instantâneos, não estão atualmente disponíveis. Gerenciamento de permissões também só está disponível quando conectado. Além disso, ao trabalhar numa conta de armazenamento de geração 2 do ADLS, Explorador de armazenamento irá utilizar o AzCopy para todos os carregamentos e transferências e predefinido para utilizar o nome e as credenciais da chave para todas as operações se estiver disponível.
* Depois de comentários do usuário forte, break lease mais uma vez pode ser utilizado para dividir as concessões em vários blobs ao mesmo tempo.

### <a name="known-issues"></a>Problemas Conhecidos

* Ao transferir a partir de uma conta de armazenamento do ADLS geração 2, se um dos ficheiros a serem transferidos já existir, o AzCopy, às vezes, irá falhar. Isso será corrigido numa futura correção.
* A desanexar a partir de um recurso ligado através do URI de SAS, por exemplo, um contentor de BLOBs, pode fazer com que um erro que impede outros anexos de aparecer corretamente. Para contornar este problema, apenas Atualize o nó do grupo. Para obter mais informações, consulte #537.
* Se utilizar o VS para Mac e alguma vez criou uma configuração AAD personalizada, pode não ser possível para início de sessão. Para contornar o problema, elimine o conteúdo de ~ /. IdentityService/AadConfigurations. Se isso não desbloquear, comentário sobre este problema.
* Azurite ainda não totalmente implementada todas as APIs de armazenamento. Por este motivo, talvez haja erros inesperados ou comportamento quando utilizar Azurite para o armazenamento de desenvolvimento.
* Em casos raros, o foco de árvore pode ficar bloqueado no acesso rápido. Para unstick o foco, pode atualizar tudo.
* Carregar a partir de pasta do OneDrive não funcionar devido a um bug em NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Bombardeador. Para contornar este problema ao carregar para ou transferir a partir de um contentor de BLOBs, pode utilizar a funcionalidade experimental do AzCopy.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito aqui.
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* O Azure Stack não suporta as seguintes funcionalidades. Recursos tentando usar esses recursos ao trabalhar com o Azure Stack podem resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação de forma recuperável
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>Versão 1.6.0
12/5/2018

### <a name="new"></a>Novo

* Agora pode utilizar o Explorador de armazenamento para aceder aos dados de BLOBs por meio [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se tem sessão iniciada e o Explorador de armazenamento é não é possível obter as chaves para a sua conta de armazenamento, em seguida, um token de OAuth será utilizado para autenticar ao interagir com os seus dados.
* Explorador de armazenamento agora suporta contas de armazenamento de geração 2 do ADLS. Quando o Explorador de armazenamento detecta esse espaço de nomes hierárquico está ativado para uma conta de armazenamento, irá ver "(pré-visualização de geração 2 ADLS)" junto ao nome da conta de armazenamento. Explorador de armazenamento é capaz de detetar se é ou não espaço de nomes hierárquico é ativado quando tem sessão iniciada, ou se tiver ligado a conta de armazenamento com o nome e a chave. Para contas de armazenamento do ADLS geração 2, pode utilizar o Explorador de armazenamento:
    * Criar e eliminar contentores
    * Gerir as propriedades do contentor e permissões (lado esquerdo)
    * Exibir e navegar dados dentro de contentores
    * Criar novas pastas
    * Carregar, transferir, mudar o nome e eliminar ficheiros e pastas
    * Gerir as propriedades de ficheiros e pastas e permissões (lado direito).
    
    Outras funcionalidades de Blob típicas, como a eliminação de forma recuperável e instantâneos, não estão atualmente disponíveis. Gerenciamento de permissões também só está disponível quando conectado. Além disso, ao trabalhar numa conta de armazenamento de geração 2 do ADLS, Explorador de armazenamento irá utilizar o AzCopy para todos os carregamentos e transferências e predefinido para utilizar o nome e as credenciais da chave para todas as operações se estiver disponível.
* Depois de comentários do usuário forte, break lease mais uma vez pode ser utilizado para dividir as concessões em vários blobs ao mesmo tempo.

### <a name="known-issues"></a>Problemas Conhecidos

* Ao transferir a partir de uma conta de armazenamento do ADLS geração 2, se um dos ficheiros a serem transferidos já existir, o AzCopy, às vezes, irá falhar. Isso será corrigido numa futura correção.
* A desanexar a partir de um recurso ligado através do URI de SAS, por exemplo, um contentor de BLOBs, pode fazer com que um erro que impede outros anexos de aparecer corretamente. Para contornar este problema, apenas Atualize o nó do grupo. Para obter mais informações, consulte #537.
* Se utilizar o VS para Mac e alguma vez criou uma configuração AAD personalizada, pode não ser possível para início de sessão. Para contornar o problema, elimine o conteúdo de ~ /. IdentityService/AadConfigurations. Se isso não desbloquear, comentário sobre este problema.
* Azurite ainda não totalmente implementada todas as APIs de armazenamento. Por este motivo, talvez haja erros inesperados ou comportamento quando utilizar Azurite para o armazenamento de desenvolvimento.
* Em casos raros, o foco de árvore pode ficar bloqueado no acesso rápido. Para unstick o foco, pode atualizar tudo.
* Carregar a partir de pasta do OneDrive não funcionar devido a um bug em NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Bombardeador. Para contornar este problema ao carregar para ou transferir a partir de um contentor de BLOBs, pode utilizar a funcionalidade experimental do AzCopy.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito aqui.
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* O Azure Stack não suporta as seguintes funcionalidades. Recursos tentando usar esses recursos ao trabalhar com o Azure Stack podem resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação de forma recuperável
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Versão 1.5.0
10/29/2018

### <a name="new"></a>Novo

* Agora, pode utilizar [AzCopy v10 (pré-visualização)](https://github.com/Azure/azure-storage-azcopy) para carregar e transferir Blobs. Para ativar esta funcionalidade vá para o menu "Experimental" e, em seguida, clique em "Utilizar AzCopy para melhorada Blob carregar e transferir". Quando ativada, o AzCopy será utilizado nos seguintes cenários:
   * Carregar pastas e arquivos para os contentores de BLOBs, através da barra de ferramentas ou arraste e largue.
   * Transferência de pastas e arquivos, através do menu de contexto ou da barra de ferramentas.

* Além disso, ao utilizar o AzCopy:
   * Pode copiar o comando do AzCopy utilizado para executar a transferência para a área de transferência. Basta clicar em "Copiar AzCopy comando para área de transferência" no registo de atividades.
   * Terá de atualizar o editor de BLOBs manualmente depois de carregar.
   * Não é suportada a carregar ficheiros para blobs de acréscimo e ficheiros vhd serão carregados como blobs de páginas e serão carregados todos os outros ficheiros como blobs de blocos.
   * Erros e conflitos que ocorrem durante o carregamento ou transferência não serão mostrados até que, após um carregamento ou download for concluído.

Por fim, suporte para utilizar o AzCopy com partilhas de ficheiros chegará no futuro.
* Explorador de armazenamento agora está a utilizar o Bombardeador versão 2.0.11.
* Quebra concessões agora pode apenas ser executada num blob de cada vez. Além disso, tem de introduzir o nome do blob cujo concessão são a última hora. Esta alteração foi feita para reduzir a probabilidade de quebrar acidentalmente uma concessão, especialmente para as VMs. #394
* Se alguma vez ocorrer problemas de início de sessão, pode agora tentar repor a autenticação. Vá para o menu de "Ajuda" e clique em "Repor" para aceder a esta capacidade. #419

### <a name="fix"></a>Corrigir

* Depois de comentários do usuário forte, o nó de emulador predefinido foi reativado. Pode ainda adicionar ligações de emulador adicionais por meio da caixa de diálogo conectar, mas se o emulador estiver configurado para utilizar as portas padrão também pode utilizar o "Emulador * portas predefinidas" no nó "Local e ligado/as contas de armazenamento". #669
* Explorador de armazenamento já não será permitem-lhe definir os valores de metadados de BLOBs que têm espaço em branco à esquerda nem à direita. #760
* O botão "Sessão" sempre foi ativado no mesmo páginas da caixa de diálogo Connect. Agora está desativada quando apropriado. #761
* Acesso rápido já não irá gerar um erro na consola quando não existem itens de acesso rápido foram adicionados.

### <a name="known-issues"></a>Problemas Conhecidos

* A desanexar a partir de um recurso ligado através do URI de SAS, por exemplo, um contentor de BLOBs, pode fazer com que um erro que impede outros anexos de aparecer corretamente. Para contornar este problema, apenas Atualize o nó do grupo. Para obter mais informações, consulte #537.
* Se utilizar o VS para Mac e alguma vez criou uma configuração AAD personalizada, pode não ser possível para início de sessão. Para contornar o problema, elimine o conteúdo de ~ /. IdentityService/AadConfigurations. Se isso não desbloquear, comentário sobre este problema.
* Azurite ainda não totalmente implementada todas as APIs de armazenamento. Por este motivo, talvez haja erros inesperados ou comportamento quando utilizar Azurite para o armazenamento de desenvolvimento.
* Em casos raros, o foco de árvore pode ficar bloqueado no acesso rápido. Para unstick o foco, pode atualizar tudo.
* Carregar a partir de pasta do OneDrive não funcionar devido a um bug em NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Bombardeador. Para contornar este problema ao carregar para ou transferir a partir de um contentor de BLOBs, pode utilizar a funcionalidade experimental do AzCopy.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito aqui.
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* O Azure Stack não suporta as seguintes funcionalidades. Recursos tentando usar esses recursos ao trabalhar com o Azure Stack podem resultar em erros inesperados.
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação de forma recuperável
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Versão 1.4.4
10/15/2018

### <a name="hotfixes"></a>Correções
* A versão de API de gestão de recursos do Azure foi revertida para o desbloqueio de utilizadores do Azure US Government. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* A carregar spinners estão agora a utilizar animações do CSS para reduzir a quantidade de GPU utilizado pelo Explorador de armazenamento. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Novo
* Anexos de recurso externo, como para ligações SAS e emuladores, foi melhorada significativamente. Agora, pode:
   * Personalize o nome a apresentar do recurso que associar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexe a vários emuladores locais utilizar portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicione recursos ligados para acesso rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Explorador de armazenamento agora suporta a eliminação de forma recuperável. Pode:
   * Configure uma política de eliminação de forma recuperável, clicando com o botão direito no nó de contentores de BLOBs para a sua conta de armazenamento.
   * Blobs de exibição de forma recuperável eliminada no Editor de Blob, selecionando "Active Directory e eliminar blobs" na lista pendente junto a barra de navegação.
   * Anular a eliminação de blobs eliminados de forma recuperável.

### <a name="fixes"></a>Correções
* A ação de "Configurar definições de CORS" já não está disponível nas contas de armazenamento Premium como contas de armazenamento Premium não suportam o CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Agora é uma propriedade de assinatura de acesso partilhado para os serviços ligados de SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação de "Definir predefinido o escalão de acesso" está agora disponíveis para BLOBs e GPV2 armazenamento as contas que foram afixadas para acesso rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Explorador de armazenamento falharia mostrar as contas de armazenamento clássicas. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Quando utilizar emuladores, como o emulador de armazenamento do Azure ou Azurite, terá de tê-los a escutar ligações nas suas portas de predefinição. Caso contrário, o Explorador de armazenamento não será capaz de se ligar aos mesmos.
* Se utilizar o VS para Mac e alguma vez criou uma configuração AAD personalizada, pode não ser possível para início de sessão. Para contornar o problema, elimine o conteúdo de ~ /. IdentityService/AadConfigurations. Se isso não desbloquear, comentar sobre [este problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ainda não totalmente implementada todas as APIs de armazenamento. Por este motivo, talvez haja erros inesperados ou comportamento quando utilizar Azurite para o armazenamento de desenvolvimento.
* Em casos raros, o foco de árvore pode ficar bloqueado no acesso rápido. Para unstick o foco, pode atualizar tudo.
* Carregar a partir de pasta do OneDrive não funcionar devido a um bug em NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Bombardeador.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Versão 1.4.3
10/11/2018

### <a name="hotfixes"></a>Correções
* A versão de API de gestão de recursos do Azure foi revertida para o desbloqueio de utilizadores do Azure US Government. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* A carregar spinners estão agora a utilizar animações do CSS para reduzir a quantidade de GPU utilizado pelo Explorador de armazenamento. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Novo
* Anexos de recurso externo, como para ligações SAS e emuladores, foi melhorada significativamente. Agora, pode:
   * Personalize o nome a apresentar do recurso que associar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexe a vários emuladores locais utilizar portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicione recursos ligados para acesso rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Explorador de armazenamento agora suporta a eliminação de forma recuperável. Pode:
   * Configure uma política de eliminação de forma recuperável, clicando com o botão direito no nó de contentores de BLOBs para a sua conta de armazenamento.
   * Blobs de exibição de forma recuperável eliminada no Editor de Blob, selecionando "Active Directory e eliminar blobs" na lista pendente junto a barra de navegação.
   * Anular a eliminação de blobs eliminados de forma recuperável.

### <a name="fixes"></a>Correções
* A ação de "Configurar definições de CORS" já não está disponível nas contas de armazenamento Premium como contas de armazenamento Premium não suportam o CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Agora é uma propriedade de assinatura de acesso partilhado para os serviços ligados de SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação de "Definir predefinido o escalão de acesso" está agora disponíveis para BLOBs e GPV2 armazenamento as contas que foram afixadas para acesso rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Explorador de armazenamento falharia mostrar as contas de armazenamento clássicas. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Quando utilizar emuladores, como o emulador de armazenamento do Azure ou Azurite, terá de tê-los a escutar ligações nas suas portas de predefinição. Caso contrário, o Explorador de armazenamento não será capaz de se ligar aos mesmos.
* Se utilizar o VS para Mac e alguma vez criou uma configuração AAD personalizada, pode não ser possível para início de sessão. Para contornar o problema, elimine o conteúdo de ~ /. IdentityService/AadConfigurations. Se isso não desbloquear, comentar sobre [este problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ainda não totalmente implementada todas as APIs de armazenamento. Por este motivo, talvez haja erros inesperados ou comportamento quando utilizar Azurite para o armazenamento de desenvolvimento.
* Em casos raros, o foco de árvore pode ficar bloqueado no acesso rápido. Para unstick o foco, pode atualizar tudo.
* Carregar a partir de pasta do OneDrive não funcionar devido a um bug em NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Bombardeador.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Versão 1.4.2
24/09/2018

### <a name="hotfixes"></a>Correções
* Atualize a versão de API de gestão de recursos Azure 2018-07-01, para adicionar suporte para novos tipos de conta de armazenamento do Azure. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Novo
* Anexos de recurso externo, como para ligações SAS e emuladores, foi melhorada significativamente. Agora, pode:
   * Personalize o nome a apresentar do recurso que associar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexe a vários emuladores locais utilizar portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicione recursos ligados para acesso rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Explorador de armazenamento agora suporta a eliminação de forma recuperável. Pode:
   * Configure uma política de eliminação de forma recuperável, clicando com o botão direito no nó de contentores de BLOBs para a sua conta de armazenamento.
   * Blobs de exibição de forma recuperável eliminada no Editor de Blob, selecionando "Active Directory e eliminar blobs" na lista pendente junto a barra de navegação.
   * Anular a eliminação de blobs eliminados de forma recuperável.

### <a name="fixes"></a>Correções
* A ação de "Configurar definições de CORS" já não está disponível nas contas de armazenamento Premium como contas de armazenamento Premium não suportam o CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Agora é uma propriedade de assinatura de acesso partilhado para os serviços ligados de SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação de "Definir predefinido o escalão de acesso" está agora disponíveis para BLOBs e GPV2 armazenamento as contas que foram afixadas para acesso rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Explorador de armazenamento falharia mostrar as contas de armazenamento clássicas. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Quando utilizar emuladores, como o emulador de armazenamento do Azure ou Azurite, terá de tê-los a escutar ligações nas suas portas de predefinição. Caso contrário, o Explorador de armazenamento não será capaz de se ligar aos mesmos.
* Se utilizar o VS para Mac e alguma vez criou uma configuração AAD personalizada, pode não ser possível para início de sessão. Para contornar o problema, elimine o conteúdo de ~ /. IdentityService/AadConfigurations. Se isso não desbloquear, comentar sobre [este problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ainda não totalmente implementada todas as APIs de armazenamento. Por este motivo, talvez haja erros inesperados ou comportamento quando utilizar Azurite para o armazenamento de desenvolvimento.
* Em casos raros, o foco de árvore pode ficar bloqueado no acesso rápido. Para unstick o foco, pode atualizar tudo.
* Carregar a partir de pasta do OneDrive não funcionar devido a um bug em NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Bombardeador.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Versão 1.4.1
08/28/2018

### <a name="hotfixes"></a>Correções
* Na primeira execução, o Explorador de armazenamento não foi possível gerar a chave utilizada para encriptar dados confidenciais. Isso causaria problemas ao utilizar o acesso rápido e anexar a recursos. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Se sua conta não obrigava a MFA para o seu inquilino principal, mas que fez com alguns outros inquilinos, o Explorador de armazenamento seria não é possível para subscrições de lista. Agora, depois de iniciar sessão com uma conta destas, Explorador de armazenamento irá pedir-lhe para reintroduzir as suas credenciais e executar a MFA. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Explorador de armazenamento não foi possível anexar os recursos do Azure US Government e do Azure Alemanha. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Se tiver iniciado sessão duas contas que tinha o mesmo endereço de e-mail, o Explorador de armazenamento, às vezes, falharia mostrar os seus recursos na vista de árvore. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Nas máquinas do Windows mais lentas, a tela inicial, às vezes, demoraria uma quantidade significativa de tempo a serem apresentados. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* A caixa de diálogo connect apareceria mesmo que haja contas anexadas ou serviços. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Novo
* Anexos de recurso externo, como para ligações SAS e emuladores, foi melhorada significativamente. Agora, pode:
   * Personalize o nome a apresentar do recurso que associar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexe a vários emuladores locais utilizar portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicione recursos ligados para acesso rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Explorador de armazenamento agora suporta a eliminação de forma recuperável. Pode:
   * Configure uma política de eliminação de forma recuperável, clicando com o botão direito no nó de contentores de BLOBs para a sua conta de armazenamento.
   * Blobs de exibição de forma recuperável eliminada no Editor de Blob, selecionando "Active Directory e eliminar blobs" na lista pendente junto a barra de navegação.
   * Anular a eliminação de blobs eliminados de forma recuperável.

### <a name="fixes"></a>Correções
* A ação de "Configurar definições de CORS" já não está disponível nas contas de armazenamento Premium como contas de armazenamento Premium não suportam o CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Agora é uma propriedade de assinatura de acesso partilhado para os serviços ligados de SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* A ação de "Definir predefinido o escalão de acesso" está agora disponíveis para BLOBs e GPV2 armazenamento as contas que foram afixadas para acesso rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Explorador de armazenamento falharia mostrar as contas de armazenamento clássicas. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas Conhecidos
* Quando utilizar emuladores, como o emulador de armazenamento do Azure ou Azurite, terá de tê-los a escutar ligações nas suas portas de predefinição. Caso contrário, o Explorador de armazenamento não será capaz de se ligar aos mesmos.
* Se utilizar o VS para Mac e alguma vez criou uma configuração AAD personalizada, pode não ser possível para início de sessão. Para contornar o problema, elimine o conteúdo de ~ /. IdentityService/AadConfigurations. Se isso não desbloquear, comentar sobre [este problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ainda não totalmente implementada todas as APIs de armazenamento. Por este motivo, talvez haja erros inesperados ou comportamento quando utilizar Azurite para o armazenamento de desenvolvimento.
* Em casos raros, o foco de árvore pode ficar bloqueado no acesso rápido. Para unstick o foco, pode atualizar tudo.
* Carregar a partir de pasta do OneDrive não funcionar devido a um bug em NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Bombardeador.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Versão 1.3.0
07/09/2018

### <a name="new"></a>Novo
* Acessar os contentores de $web utilizados por Web sites estáticos é agora suportado. Isto permite-lhe para facilmente carregar e gerir ficheiros e pastas utilizadas pelo seu Web site. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* Tem sido reorganizar a barra de aplicativo no macOS. As alterações incluem um menu de ficheiro, algumas alterações de chave de atalho e vários novos comandos no menu da aplicação. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* O ponto final da autoridade para iniciar sessão no Azure US Government foi alterado para https://login.microsoftonline.us/
* Acessibilidade: Quando um leitor de tela está ativo, navegação do teclado agora funciona com as tabelas usadas para exibir itens no lado direito. Pode utilizar as teclas de seta para navegar de linhas e colunas, Enter para invocar ações padrão, a chave de menu de contexto para abrir o menu de contexto para um item e Shift ou controlar a seleção múltipla. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Correções
*  Em algumas máquinas, os processos filho foram demorar muito tempo para iniciar. Quando isso acontece, aparecerá um erro de "processo filho falhou ao iniciar atempadamente". O tempo alocado para um processo filho começar agora aumentou de 20 para 90 segundos. Se ainda é afetados por este problema, comente sobre o problema do GitHub ligado. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Quando utilizar uma SAS que não ter permissões de leitura, não foi possível carregar um blob de grandes dimensões. A lógica para o carregamento foi modificada para funcionar neste cenário. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Definir o nível de acesso público para um contentor teria de remover todas as políticas de acesso e vice-versa. Agora, as políticas de acesso e de nível de acesso público são preservadas durante a configuração de um dos dois. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" foi truncado na caixa de diálogo de propriedades. Isto foi corrigido. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* O "Microsoft Azure Storage Explorer-" o prefixo estava em falta na caixa de diálogo Criar novo diretório. Isto foi corrigido. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Acessibilidade: A caixa de diálogo Adicionar entidade era difícil navegue até ao utilizar VoiceOver. Foram efetuadas melhorias. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Acessibilidade: A cor de fundo do botão Expandir/Fechar para o painel de ações e propriedades foi inconsistente com controles de interface do Usuário semelhante no tema de alto contraste preto. A cor foi alterada. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Acessibilidade: Tema de alto contraste preto, o foco de definição de estilo para o botão 'X', na caixa de diálogo de propriedades não era visível. Isto foi corrigido. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Acessibilidade: Os separadores de propriedades e ações foram em falta vários valores de aria que resultou numa experiência de leitor de ecrã abaixo da média comparados. Os valores em falta do aria agora foram adicionados. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Acessibilidade: Nós da árvore recolhido no lado esquerdo não foram a ser fornecidos um valor false aria-expandido. Isto foi corrigido. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Problemas Conhecidos
* A desanexar a partir de um recurso ligado através do URI de SAS, por exemplo, um contentor de BLOBs, pode fazer com que um erro que impede outros anexos de aparecer corretamente. Para contornar este problema, apenas Atualize o nó do grupo. Ver [este problema](https://github.com/Microsoft/AzureStorageExplorer/issues/537) para obter mais informações.
* Se utilizar o VS para Mac e alguma vez criou uma configuração AAD personalizada, pode não ser possível para início de sessão. Para contornar o problema, elimine o conteúdo de ~ /. IdentityService/AadConfigurations. Se isso não desbloquear, comentar sobre [este problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ainda não totalmente implementada todas as APIs de armazenamento. Por este motivo, talvez haja erros inesperados ou comportamento quando utilizar Azurite para o armazenamento de desenvolvimento.
* Em casos raros, o foco de árvore pode ficar bloqueado no acesso rápido. Para unstick o foco, pode atualizar tudo.
* Carregar a partir de pasta do OneDrive não funcionar devido a um bug em NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Bombardeador.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* O Azure Stack não suporta as seguintes funcionalidades e tentar usá-los ao trabalhar com o Azure Stack pode resultar em erros inesperados:
   * Partilhas de ficheiros
   * Camadas de acesso
   * Eliminação de forma recuperável
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Versão 1.2.0
12/06/2018

### <a name="new"></a>Novo
* Se Explorador de armazenamento falhar ao carregar subscrições de apenas um subconjunto dos seus inquilinos, em seguida, quaisquer subscrições carregadas com êxito serão apresentadas juntamente com uma mensagem de erro especificamente para os inquilinos que falharam. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* No Windows, quando uma atualização está disponível, agora pode optar por "Atualizar no Fechar". Quando esta opção é escolhida, o instalador para a atualização será executado depois de fechar o Explorador de armazenamento. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Restaurar o instantâneo foi adicionado ao menu de contexto do editor de partilha de ficheiros ao visualizar um instantâneo de partilha de ficheiros. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* O botão Limpar fila agora está sempre ativado. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Suporte para início de sessão no ADFS do Azure Stack foi reativado. O Azure Stack versão 1804 ou superior é necessário. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Correções
* Se visse instantâneos para uma partilha de ficheiros cujo nome foi um prefixo de outra partilha de ficheiros na mesma conta de armazenamento, em seguida, os instantâneos da partilha de ficheiros também seriam listados. Este problema foi corrigido. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Quando ligado através de SAS, restaurar um ficheiro a partir de um instantâneo de partilha de ficheiros iria resultar num erro. Este problema foi corrigido. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Ao visualizar os instantâneos de um blob, a ação de promover o instantâneo foi ativada quando o blob de base e um único instantâneo foram selecionados. A ação agora está ativada apenas se for selecionado um único instantâneo. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Se uma única tarefa (por exemplo, transferir um blob) foi iniciada e falha mais tarde, ele seria automaticamente repete até que iniciou a tarefa de outra do mesmo tipo. Todas as tarefas devem agora automaticamente repetição, independentemente de quantas tarefas têm em fila.
* Editores aberta para recentemente contentores de BLOBs criado no GPV2 e contas de armazenamento de BLOBs não tinha uma coluna de camada de acesso. Este problema foi corrigido. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Uma coluna de camada de acesso, por vezes, não apareceria quando uma conta de armazenamento ou contentor de BLOBs foi ligado através de SAS. A coluna sempre agora vai ser mostrada, mas com um valor vazio se não houver nenhum conjunto de camada de acesso. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Definir a camada de acesso de um blob de blocos recém-carregada foi desativada. Este problema foi corrigido. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Se o botão "Manter separador aberto" foi invocado com o teclado, o foco do teclado seriam perdido. Agora, o foco será movido para o separador que foi mantido aberto. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Para uma consulta no construtor de consultas, VoiceOver não era a dar uma descrição utilizável do operador atual. Agora é mais descritivo. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Os links de paginação para os vários editores não eram descritivos. Eles foram alterados para ser mais descritivo. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* Na caixa de diálogo Adicionar entidade, VoiceOver foi não Anunciamos que coluna um elemento de entrada fazia parte do. O nome da coluna atual agora está incluído na descrição do elemento. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Botões de opção e caixas de verificação não tinha uma borda visível quando em destaque. Este problema foi corrigido. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Problemas Conhecidos
* Quando utilizar emuladores, como o emulador de armazenamento do Azure ou Azurite, terá de tê-los a escutar ligações nas suas portas de predefinição. Caso contrário, o Explorador de armazenamento não será capaz de se ligar aos mesmos.
* Se utilizar o VS para Mac e alguma vez criou uma configuração AAD personalizada, pode não ser possível para início de sessão. Para contornar o problema, elimine o conteúdo de ~ /. IdentityService/AadConfigurations. Se isso não desbloquear, comentar sobre [este problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ainda não totalmente implementada todas as APIs de armazenamento. Por este motivo, talvez haja erros inesperados ou comportamento quando utilizar Azurite para o armazenamento de desenvolvimento.
* Em casos raros, o foco de árvore pode ficar bloqueado no acesso rápido. Para unstick o foco, pode atualizar tudo.
* Carregar a partir de pasta do OneDrive não funcionar devido a um bug em NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Bombardeador.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Versão 1.1.0
05/09/2018

### <a name="new"></a>Novo
* Explorador de armazenamento agora suporta a utilização de Azurite. Nota: a ligação ao Azurite é codificado para os pontos finais de desenvolvimento de predefinição.
* Agora, o Explorador de armazenamento suporta os escalões de acesso para apenas de BLOBs e contas de armazenamento GPV2. Saiba mais sobre escalões de acesso [aqui](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
* Uma hora de início já não é necessária ao gerar uma SAS.

### <a name="fixes"></a>Correções
* A obter as subscrições para contas de administração pública dos EUA estava quebrada. Este problema foi corrigido. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* A hora de expiração para políticas de acesso corretamente não estava a ser guardada. Este problema foi corrigido. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Ao gerar um URL de SAS para um item num contentor, o nome do item não estava a ser anexado ao URL. Este problema foi corrigido. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Ao criar uma SAS, tempos de expiração que estão no passado, às vezes, seria o valor predefinido. Isto foi devido à utilização do Explorador de armazenamento que a última utilizada a hora de início e de expiração como valores predefinidos. Agora, sempre que abrir a caixa de diálogo SAS, é gerado um novo conjunto de valores predefinidos. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Ao copiar entre contas de armazenamento, é gerada uma SAS de 24 horas. Se a cópia há mais de 24 horas, em seguida, a cópia de falha. Aumentámos a SAS última semana para reduzir a possibilidade de uma cópia falhar devido a uma SAS expirada. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Para algumas atividades clicando em "Cancelar" não sempre funciona. Este problema foi corrigido. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Para algumas atividades, a velocidade de transferência estava incorreta. Este problema foi corrigido. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* A ortografia de "Anterior" no menu exibir estava incorreta. Ele agora está escrito corretamente. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* A página final do programa de instalação Windows tinha um botão "Seguinte". Ele foi alterado para um botão "Concluir". [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Detalhe do separador não era estará visível para os botões nas caixas de diálogo quando utiliza o tema do HC preto. Agora está visível. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* As maiúsculas e minúsculas de "Resolver automaticamente" para ações no registo de atividades estava incorreta. Agora está correto. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Quando eliminar uma entidade a partir de uma tabela, a caixa de diálogo a pedir-lhe confirmação apresentado um ícone de erro. A caixa de diálogo agora usa um ícone de aviso. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Problemas Conhecidos
* Se utilizar o VS para Mac e alguma vez criou uma configuração AAD personalizada, pode não ser possível para início de sessão. Para contornar o problema, elimine o conteúdo de ~ /. IdentityService/AadConfigurations. Se isso não desbloquear, comentar sobre [este problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ainda não totalmente implementada todas as APIs de armazenamento. Por este motivo, talvez haja erros inesperados ou comportamento quando utilizar Azurite para o armazenamento de desenvolvimento.
* Em casos raros, o foco de árvore pode ficar bloqueado no acesso rápido. Para unstick o foco, pode atualizar tudo.
* Carregar a partir de pasta do OneDrive não funcionar devido a um bug em NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Bombardeador.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Versão 1.0.0
04/16/2018

### <a name="new"></a>Novo
* Autenticação avançada que permite que o Explorador de armazenamento utilizar o mesmo armazenamento de conta como o Visual Studio 2017. Para utilizar esta funcionalidade, terá de re-início de sessão para as suas contas e definir novamente as suas subscrições filtradas.
* Para contas do Azure Stack alicerçadas AAD, o Explorador de armazenamento recuperará agora subscrições do Azure Stack quando 'Azure Stack de destino' está ativado. Já não terá de criar um ambiente de início de sessão personalizada.
* Vários atalhos foram adicionados para permitir a navegação mais rápida. Estes incluem a alteração de vários painéis e mover entre editores. Ver o menu exibir para obter mais detalhes.
* Comentários do Explorador de armazenamento agora residem no GitHub. Pode entrar nossa página de problemas ao clicar no botão de comentários na parte inferior esquerda ou ao aceder a [ https://github.com/Microsoft/AzureStorageExplorer/issues ](https://github.com/Microsoft/AzureStorageExplorer/issues). Não hesite em fazer sugestões, reportar problemas, fazer perguntas ou deixe qualquer outra forma de comentários.
* Se se deparar com problemas de certificado SSL e não é possível localizar o certificado incorreto, agora, pode iniciar o Explorador de armazenamento da linha de comando com o `--ignore-certificate-errors` sinalizador. Quando iniciado com esse sinalizador, o Explorador de armazenamento irão ignorar erros de certificado SSL.
* Agora é uma opção de "Transferir" no menu de contexto para itens de BLOBs e ficheiros.
* Acessibilidade melhorada e suporte de leitor de ecrã. Se contar com funcionalidades de acessibilidade, consulte nosso [documentação de acessibilidade](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) para obter mais informações.
* Agora, o Explorador de armazenamento utiliza Bombardeador 1.8.3

### <a name="breaking-changes"></a>Alterações Interruptivas
* Explorador de armazenamento mudou para uma nova biblioteca de autenticação. Como parte do comutador para a biblioteca, será necessário re-início de sessão para as suas contas e definir novamente as suas subscrições filtradas
* O método usado para criptografar dados confidenciais foi alterado. Isso pode resultar em alguns dos seus itens de acesso rápido que precisam de ser adicionado novamente e/ou alguns de vocês anexado recursos que precisem de ser novamente ligado.

### <a name="fixes"></a>Correções
* Alguns usuários por trás de proxies teria carregamentos de BLOBs de grupo ou transferências interrompidas por uma "não é possível resolver" mensagem de erro. Este problema foi corrigido.
* Se o início de sessão era necessária embora utilizar uma ligação direta, clicar na linha de comandos 'Início de sessão' apresentaria uma caixa de diálogo em branco. Este problema foi corrigido.
* No Linux, se o Explorador de armazenamento não consegue iniciar devido a uma falha de processo GPU, será agora informado da falha, disse para utilizar o ' – desativar gpu "comutador e que serão de Explorador de armazenamento, em seguida, reiniciar automaticamente com a opção ativada.
* Políticas de acesso inválido foram difíceis de identidade na caixa de diálogo de políticas de acesso. Política de acesso inválido IDs agora são descritos em vermelho para obter mais visibilidade.
* O registo de atividades, por vezes, teria grandes áreas de espaço em branco entre as diferentes partes de uma atividade. Este problema foi corrigido.
* No editor de consulta de tabela, se deixado uma cláusula timestamp num estado inválido e, em seguida, tentou modificar a cláusula de outro, o editor seria congelar. O editor de agora irá restaurar a cláusula timestamp para o último Estado válido quando for detetada alguma alteração na cláusula de outro.
* Se está em pausa durante a digitação na sua consulta de pesquisa na vista de árvore, pode começar a pesquisa e roubado foco da caixa de texto. Agora, explicitamente que deve começar a procurar, pressionando a tecla 'Enter', ou ao clicar no botão de pesquisa de início.
* O comando "Obter assinatura de acesso partilhado" às vezes, será desativado quando o botão direito do rato clicar num ficheiro numa partilha de ficheiros. Este problema foi corrigido.
* Se o nó de árvore de recursos com o foco foi filtrado durante a pesquisa, não foi possível separador na árvore de recursos e usar as teclas de seta para navegar pela árvore de recursos. Agora, se o nó de árvore de recursos focada está oculta, o primeiro nó na árvore de recursos vai ser automaticamente concentra-se.
* Um separador extra, às vezes, seria visível na barra de ferramentas da editor. Este problema foi corrigido.
* A caixa de texto de trilha, às vezes, seria overflow. Este problema foi corrigido.
* Os editores de BLOBs e a partilha de ficheiros, por vezes, constantemente seriam atualizar ao carregar vários ficheiros ao mesmo tempo. Este problema foi corrigido.
* A funcionalidade de estatísticas de pasta não tinha nenhuma outra finalidade na vista de gestão de instantâneos de partilha de ficheiros. Agora foi desativada.
* No Linux, o menu de ficheiro não sejam apresentados. Este problema foi corrigido.
* Ao carregar uma pasta para uma partilha de ficheiros, por predefinição, apenas o conteúdo da pasta foram carregado. Agora, o comportamento padrão é carregar o conteúdo da pasta para uma pasta correspondente na partilha de ficheiros.
* A ordenação dos botões em várias caixas de diálogo tinha foi revertida. Este problema foi corrigido.
* Segurança de várias relacionadas com as correções.

### <a name="known-issues"></a>Problemas Conhecidos
* Em casos raros, o foco de árvore pode ficar bloqueado no acesso rápido. Para unstick o foco, pode atualizar tudo.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito aqui.
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Os utilizadores do Linux, terá de instalar [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Versão 0.9.6
02/28/2018

### <a name="fixes"></a>Correções
* Um problema impediu a serem listadas no editor de ficheiros/blobs esperados. Este problema foi corrigido.
* Um problema causado alternar entre modos de exibição de instantâneo para exibir itens incorretamente. Este problema foi corrigido.

### <a name="known-issues"></a>Problemas Conhecidos
* Explorador de armazenamento não suporta contas ADFS.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar as subscrições.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Versão 0.9.5
02/06/2018

### <a name="new"></a>Novo

* Suporte para instantâneos de partilhas de ficheiros:
    * Criar e gerir instantâneos para as partilhas de ficheiros.
    * Alterne facilmente vistas entre os instantâneos de partilhas de ficheiros à medida que explora.
    * Restaure versões anteriores dos seus ficheiros.
* Suporte de pré-visualização para o Azure Data Lake Store:
    * Ligue-se aos seus recursos do ADLS em várias contas.
    * Ligar a e partilhar os recursos do ADLS com URIs do ADL.
    * Execute recursivamente de operações de ficheiro/pasta básica.
    * Pastas individuais afixar para acesso rápido.
    * Apresentar estatísticas de pasta.

### <a name="fixes"></a>Correções
* Melhorias de desempenho de inicialização.
* Várias correções de erros.

### <a name="known-issues"></a>Problemas Conhecidos
* Explorador de armazenamento não suporta contas ADFS.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito aqui.
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar as subscrições.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Versão 0.9.4 e 0.9.3
21/01/2018

### <a name="new"></a>Novo
* A janela do Explorador de armazenamento existente irão ser reutilizadas quando:
    * Abrir ligações diretas geradas no Explorador de armazenamento.
    * Abrir Explorador de armazenamento a partir do portal.
    * Abrir o Explorador de armazenamento a partir de extensão do VS Code com armazenamento do Azure (brevemente).
* Foi adicionada a capacidade para abrir uma nova janela do Explorador de armazenamento no Explorador de armazenamento.
    * Para Windows, há uma opção de "Nova janela" no Menu de ficheiro e no menu de contexto da barra de tarefas.
    * Para Mac, existe uma opção de "Nova janela" no Menu da aplicação.

### <a name="fixes"></a>Correções
* Foi corrigido um problema de segurança. Atualize para 0.9.4 o quanto.
* Actividades antigas não foram adequadamente sejam limpos. Isso afetou o desempenho das tarefas de longa execução. Eles são agora a ser limpos corretamente.
* Ações que envolvem um grande número de ficheiros e diretórios, ocasionalmente, faria com que o Explorador de armazenamento congelar. Pedidos para o Azure para partilhas de ficheiros agora são limitados para limitar o uso de recursos do sistema.

### <a name="known-issues"></a>Problemas Conhecidos
* Explorador de armazenamento não suporta contas ADFS.
* Teclas de atalho para "Vista Explorer" e "Vista de gestão de contas" devem ser Ctrl / Cmd + Shift + E e Ctrl / Cmd + Shift + A respectivamente.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito aqui.
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar as subscrições.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Versão 0.9.2
11/01/2017

### <a name="hotfixes"></a>Correções
* As alterações de dados inesperado eram possíveis quando editar valores de Edm.DateTime para entidades da tabela consoante o fuso horário local. Agora, o editor utiliza uma caixa de texto sem formatação, fornecendo preciso e consistente controle sobre os valores de Edm.DateTime.
* A carregar/transferir um grupo de blobs, quando anexado com nome e a chave não arrancam. Este problema foi corrigido.
* Anteriormente, o Explorador de armazenamento seria apenas pedir-lhe para autenticar uma conta obsoleta, se um ou mais das subscrições da conta tiver sido selecionada. Agora o Explorador de armazenamento irá solicitar-lhe, mesmo que a conta é totalmente filtrada.
* O domínio de pontos de extremidade para Azure US Government estava incorreto. Foi corrigido.
* No botão aplicar no painel de gerir contas, às vezes, era difícil de clique. Isto já não deve acontecer.

### <a name="new"></a>Novo
* Suporte de pré-visualização do Azure Cosmos DB:
    * [Documentação online](./cosmos-db/storage-explorer.md)
    * Criar bases de dados e coleções
    * Manipular dados
    * Consultar, criar ou eliminar documentos
    * Atualizar procedimentos armazenados, funções definidas pelo utilizador ou disparadores
    * Utilizar cadeias de ligação para ligar e gerir as suas bases de dados
* Melhorámos o desempenho de carregamento/transferência vários pequenos blobs.
* Adicionar uma ação de "All Repita" se existirem falhas num grupo de carregamento de BLOBs ou grupo de download do blob.
* Explorador de armazenamento irá agora colocar em pausa iteração durante blob carregar/transferir se detetar que perdeu-se a ligação de rede. Em seguida, pode retomar a iteração assim que a ligação de rede foi restabelecida.
* Foi adicionada a capacidade "Fechar tudo", "Fechar outros" e "Fechar" separadores através do menu de contexto.
* Explorador de armazenamento utiliza agora as caixas de diálogo nativas e menus de contexto nativo.
* Explorador de armazenamento agora é mais acessível. Melhoramentos incluem:
    * Suporte de leitor de ecrã melhorado, para NVDA no Windows e para VoiceOver no Mac
    * Melhorada a personalização de alto contraste
    * Correções de tabulação e do teclado o foco do teclado

### <a name="fixes"></a>Correções
* Se tentou abrir ou transferir um blob com um nome de ficheiro inválido do Windows, a operação falha. Explorador de armazenamento irá agora detetar se um nome de blob é inválido e perguntar se pretende codificá-lo ou ignorar o blob. Explorador de armazenamento também Deteta se um nome de ficheiro parece ser codificados e pedir-lhe se pretende decodificá-la antes de carregar.
* Durante o carregamento de BLOBs, o editor para o contentor de blob de destino seria, às vezes, não corretamente atualizar. Este problema foi corrigido.
* O suporte para vários formulários de cadeias de ligação e SAS URIs regredido. Podemos corrigir esses todos os problemas conhecidos, mas envie comentários, se ainda ocorrerem problemas.
* A notificação de atualização foi quebrada para alguns usuários no 0.9.0. Este problema e para aqueles afetados pelo bug, pode transferir manualmente a versão mais recente do Explorador de armazenamento [aqui](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Problemas Conhecidos
* Explorador de armazenamento não suporta contas ADFS.
* Teclas de atalho para "Vista Explorer" e "Vista de gestão de contas" devem ser Ctrl / Cmd + Shift + E e Ctrl / Cmd + Shift + A respectivamente.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito aqui.
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar as subscrições.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Versão 0.9.1 e 0.9.0
10/20/2017
### <a name="new"></a>Novo
* Suporte de pré-visualização do Azure Cosmos DB:
    * [Documentação online](./cosmos-db/storage-explorer.md)
    * Criar bases de dados e coleções
    * Manipular dados
    * Consultar, criar ou eliminar documentos
    * Atualizar procedimentos armazenados, funções definidas pelo utilizador ou disparadores
    * Utilizar cadeias de ligação para ligar e gerir as suas bases de dados
* Melhorámos o desempenho de carregamento/transferência vários pequenos blobs.
* Adicionar uma ação de "All Repita" se existirem falhas num grupo de carregamento de BLOBs ou grupo de download do blob.
* Explorador de armazenamento irá agora colocar em pausa iteração durante blob carregar/transferir se detetar que perdeu-se a ligação de rede. Em seguida, pode retomar a iteração assim que a ligação de rede foi restabelecida.
* Foi adicionada a capacidade "Fechar tudo", "Fechar outros" e "Fechar" separadores através do menu de contexto.
* Explorador de armazenamento utiliza agora as caixas de diálogo nativas e menus de contexto nativo.
* Explorador de armazenamento agora é mais acessível. Melhoramentos incluem:
    * Suporte de leitor de ecrã melhorado, para NVDA no Windows e para VoiceOver no Mac
    * Melhorada a personalização de alto contraste
    * Correções de tabulação e do teclado o foco do teclado

### <a name="fixes"></a>Correções
* Se tentou abrir ou transferir um blob com um nome de ficheiro inválido do Windows, a operação falha. Explorador de armazenamento irá agora detetar se um nome de blob é inválido e perguntar se pretende codificá-lo ou ignorar o blob. Explorador de armazenamento também Deteta se um nome de ficheiro parece ser codificados e pedir-lhe se pretende decodificá-la antes de carregar.
* Durante o carregamento de BLOBs, o editor para o contentor de blob de destino seria, às vezes, não corretamente atualizar. Este problema foi corrigido.
* O suporte para vários formulários de cadeias de ligação e SAS URIs regredido. Podemos corrigir esses todos os problemas conhecidos, mas envie comentários, se ainda ocorrerem problemas.
* A notificação de atualização foi quebrada para alguns usuários no 0.9.0. Este problema e para aqueles afetados pelo bug, pode transferir manualmente a versão mais recente do Explorador de armazenamento [aqui](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Problemas Conhecidos
* Explorador de armazenamento não suporta contas ADFS.
* Teclas de atalho para "Vista Explorer" e "Vista de gestão de contas" devem ser Ctrl / Cmd + Shift + E e Ctrl / Cmd + Shift + A respectivamente.
* Para criar aplicativos para o Azure Stack, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Isso é porque estamos usando o trabalho de filtro de cancelar em torno descrito aqui.
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar as subscrições.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* O shell de Bombardeador utilizado pelo Explorador de armazenamento tem problemas com alguns aceleração de hardware GPU (unidade de processamento gráfico). Se o Explorador de armazenamento está exibindo uma janela principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento a partir da linha de comandos e desabilitando aceleração por GPU, adicionando o `--disable-gpu` mudar:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Versão 0.8.16
8/21/2017

### <a name="new"></a>Novo
* Quando abre um blob, Explorador de armazenamento irá solicitar-lhe carregar o ficheiro transferido, se for detetada alguma alteração
* Avançada experiência de início de sessão do Azure Stack
* Melhorámos o desempenho de carregamento/transferência tantos arquivos pequenos em simultâneo


### <a name="fixes"></a>Correções
* Para alguns tipos de BLOBs, optando por "substituir" durante um conflito de carregamento, às vezes, resultaria no carregamento a ser reiniciado.
* Na versão 0.8.15, carregamentos seriam, às vezes, manipulação compartimento em 99%.
* Falha ao carregar ficheiros para uma partilha de ficheiros, se optar por carregar para um diretório que não o foi ainda existe, o carregamento.
* Explorador de armazenamento foi incorretamente a geração de carimbos de data / hora para assinaturas de acesso partilhado e consultas de tabela.


### <a name="known-issues"></a>Problemas Conhecidos
* Utilizando um nome e a cadeia de ligação da chave não funciona. Ela será corrigida na próxima versão. Até lá, pode usar anexar com nome e a chave.
* Se tentar abrir um ficheiro com um nome de ficheiro inválido do Windows, o download resultará num arquivo não encontrou o erro.
* Depois de clicar em "Cancelar" numa tarefa, poderá demorar algum tempo para essa tarefa Cancelar. Esta é uma limitação da biblioteca do nó de armazenamento do Azure.
* Depois de concluir o carregamento de um blob, a guia que iniciou o carregamento é atualizada. Esta é uma mudança do comportamento anterior e também fará com que ser direcionado novamente para a raiz do contentor que estiver no.
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça dessa decisão.
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar as subscrições.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* Para os utilizadores no Ubuntu 14.04, precisará garantir GCC é atualizado - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - isso pode ser feito ao executar os seguintes comandos e, em seguida, reiniciar a máquina:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Versão 0.8.14
06/22/2017

### <a name="new"></a>Novo

* Versão atualizada do Bombardeador para 1.7.2 para tirar o máximo partido das várias atualizações de segurança críticas
* Pode agora aceder rapidamente o guia de resolução de problemas online no menu Ajuda
* Explorador de armazenamento de resolução de problemas [guia][2]
* [Instruções] [ 3] sobre como ligar a uma subscrição do Azure Stack

### <a name="known-issues"></a>Problemas Conhecidos

* Botões da caixa de diálogo de confirmação de pasta delete não se registar com os cliques do mouse no Linux. trabalho em torno é usar a tecla Enter
* Se escolher o certificado PIN/smart card errado, em seguida, terá de reiniciar para ter o Explorador de armazenamento se esqueça a decisão
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo, poderá causar erros
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar subscrições
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* Instalação de Ubuntu 14.04 precisar de versão de gcc atualizada ou atualizado – passos para atualizar estão abaixo:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Versão 0.8.13
05/12/2017

#### <a name="new"></a>Novo

* Explorador de armazenamento de resolução de problemas [guia][2]
* [Instruções] [ 3] sobre como ligar a uma subscrição do Azure Stack

#### <a name="fixes"></a>Correções

* Corrigido: Carregamento de ficheiros tido a oportunidade de causar um fora de erro de memória elevada
* Corrigido: Pode agora iniciar sessão com PIN/smart card
* Corrigido: Abrir no Portal agora funciona com o Azure China 21Vianet, Azure Alemanha, Azure US Government e Azure Stack
* Corrigido: Ao carregar uma pasta para um contentor de BLOBs, às vezes, poderia ocorrer um erro de "Operação ilegal"
* Corrigido: Selecionar tudo foi desativado, gerindo instantâneos
* Corrigido: Os metadados do base blob talvez seja substituído depois de visualizar as propriedades dos respetivos instantâneos

#### <a name="known-issues"></a>Problemas Conhecidos

* Se escolher o certificado PIN/smart card errado, em seguida, terá de reiniciar para ter o Explorador de armazenamento se esqueça a decisão
* Embora ampliado dentro ou para fora, o nível de zoom momentaneamente pode repor para o nível predefinido
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo, poderá causar erros
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar subscrições
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* Instalação de Ubuntu 14.04 precisar de versão de gcc atualizada ou atualizado – passos para atualizar estão abaixo:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Versão 0.8.12 e 0.8.11 e 0.8.10
04/07/2017

#### <a name="new"></a>Novo

* Explorador de armazenamento agora fechará automaticamente quando instala uma atualização da notificação de atualização
* Acesso rápido de no local fornece uma experiência aprimorada para trabalhar com os seus recursos acedidos com frequência
* No editor de contentor de BLOBs, agora pode ver que o virtual machine um blob de concessão pertence a
* Agora pode fechar o painel do lado esquerdo
* A deteção é executada ao mesmo tempo para download
* Estatísticas de utilização nos editores de contentor de BLOBs, a partilha de ficheiros e a tabela para ver o tamanho do seu recurso ou a seleção
* Pode agora o início de sessão ao Azure Active Directory (AAD) com base em contas do Azure Stack.
* Agora, pode carregar ficheiros de arquivo mais de 32MB para contas de armazenamento Premium
* Melhoraram o suporte de acessibilidade
* Agora, pode adicionar fidedigno para a Base 64 codificada certificados X.509 SSL acedendo à edição -&gt; certificados SSL -&gt; importar certificados

#### <a name="fixes"></a>Correções

* Corrigido: depois de atualizar as credenciais de uma conta, a vista de árvore poderia, por vezes, atualiza automaticamente
* Corrigido: gerar uma SAS para tabelas e filas de emulador resultaria num URL inválido
* Foi corrigido: contas de armazenamento premium podem agora ser expandidas enquanto um proxy está ativado
* Corrigido: o botão de aplicar a página de gestão de contas não funcionaria se tiver selecionadas a contas de 1 ou 0
* Foi corrigido: carregar blobs que requerem resolução de conflito poderão falhar - corrigidos no 0.8.11
* Corrigido: enviar comentários foi dividida em 0.8.11 - corrigidos no 0.8.12

#### <a name="known-issues"></a>Problemas Conhecidos

* Após a atualização para 0.8.10, terá de atualizar todas as suas credenciais.
* Embora ampliado dentro ou para fora, o nível de zoom momentaneamente pode repor para o nível predefinido.
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo que pode causar erros.
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar subscrições.
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são mantidas durante uma mudança de nome.
* Embora o Azure Stack atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros ainda for exibido numa conta de armazenamento do Azure Stack anexada.
* Instalação de Ubuntu 14.04 precisar de versão de gcc atualizada ou atualizado – passos para atualizar estão abaixo:

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

* Explorador de armazenamento 0.8.9 irá transferir automaticamente a versão mais recente de atualizações.
* URI de SAS gerado para anexar uma conta de armazenamento a correção: utilizar um portal iria resultar num erro.
* Agora pode criar, gerenciar e promover instantâneos de blob.
* Pode agora iniciar sessão no Azure China 21Vianet, o Azure Alemanha e contas do Azure US Government.
* Agora, pode alterar o nível de zoom. Utilize as opções no menu exibir para aplicar Zoom no, ampliar horizontalmente e repor Zoom.
* Caracteres Unicode agora são suportados nos metadados de utilizador para blobs e ficheiros.
* Melhorias de acessibilidade.
* Notas de versão a próxima versão podem ser visualizadas a notificação de atualização. Também pode ver as notas de versão atual do menu de ajuda.

#### <a name="fixes"></a>Correções

* Foi corrigido: o número de versão é agora apresentado corretamente no painel de controle no Windows
* Foi corrigido: pesquisa já não está limitada a 50 000 nós
* Foi corrigido: a carregar para uma partilha de ficheiros criada para sempre se o diretório de destino já não existe
* Foi corrigido: maior de estabilidade de longo carregamentos e transferências

#### <a name="known-issues"></a>Problemas Conhecidos

* Embora ampliado dentro ou para fora, o nível de zoom momentaneamente pode repor para o nível predefinido.
* Acesso rápido só funciona com itens de subscrição com base. Recursos locais ou a recursos anexados através de chave ou SAS token não é suportada nesta versão.
* Pode demorar alguns segundos para navegar para o recurso de destino, dependendo de quantos recursos que tenha acesso rápido.
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo que pode causar erros.

12/16/2016
### <a name="version-087"></a>Versão 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Novo

* Pode escolher como resolver conflitos no início de uma sessão de atualização, download ou cópia na janela de atividades
* Coloque o cursor sobre uma guia para ver o caminho completo do recurso de armazenamento
* Quando clica numa guia, sincronizá-lo com a sua localização no painel de navegação do lado esquerdo

#### <a name="fixes"></a>Correções

* Corrigido: Explorador de armazenamento agora é um aplicativo confiável no Mac
* Corrigido: Ubuntu 14.04 novamente é suportada
* Corrigido: Por vezes, a adicionar conta da interface do Usuário pisca quando a carregar subscrições
* Corrigido: Por vezes, nem todos os recursos de armazenamento listados no painel de navegação do lado esquerdo
* Corrigido: O painel de ações, às vezes, apresentado ações vazias
* Corrigido: O tamanho da janela na última sessão fechada agora é mantido
* Corrigido: Pode abrir vários separadores para o mesmo recurso utilizando o menu de contexto

#### <a name="known-issues"></a>Problemas Conhecidos

* Acesso rápido só funciona com itens de subscrição com base. Recursos locais ou a recursos anexados através de chave ou SAS token não é suportada nesta versão
* Poderá demorar alguns segundos para navegar para o recurso de destino, dependendo de quantos recursos que tenha acesso rápido
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo, poderá causar erros
* Identificadores de pesquisa, pesquisar, em aproximadamente 50 000 nós - depois disso, o desempenho poderá ser afetado ou pode fazer com que uma exceção não processada
* Pela primeira vez utilizando o Explorador de armazenamento no macOS, poderá ver vários pedidos solicitando permissão do utilizador aceder a keychain. Sugerimos que selecionar permitir sempre para que a linha de comandos não aparecerá novamente

11/18/2016
### <a name="version-086"></a>Versão 0.8.6

#### <a name="new"></a>Novo

* Pode agora pin utilizados mais frequentemente serviços para o acesso rápido para uma navegação fácil
* Agora pode abrir vários editores em separadores diferentes. Único clique para abrir um separador temporário; Faça duplo clique para abrir um separador permanente. Também pode clicar na guia temporária para que seja uma guia permanente
* Tornámos o desempenho considerável e melhorias de estabilidade para carrega e transfere, especialmente para arquivos grandes em máquinas rápidas
* Pastas vazias de "virtuais" agora podem ser criadas em contentores de BLOBs
* Introduzimos novamente procura de âmbito com nossa nova pesquisa de subcadeia aprimorada, portanto, agora tem duas opções de pesquisa:
    * Global de pesquisa – basta inserir um termo de pesquisa na caixa de texto de pesquisa
    * Procura de âmbito - clique no ícone de lupa junto a um nó, em seguida, adicione um termo de pesquisa no final do caminho, ou com o botão direito e selecione "Pesquisa de aqui"
* Adicionámos vários temas: Claro (padrão), escuro, elevado contraste preto e alto contraste White. Aceda a edição -&gt; temas para alterar a sua preferência de personalização
* Pode modificar as propriedades de BLOBs e ficheiros
* Suportamos agora codificado (base64) e mensagens de fila não codificado
* No Linux, um sistema operacional de 64 bits é agora necessário. Para esta versão só oferecemos suporte a 64-bit Ubuntu 16.04.1 LTS
* Que atualizamos nosso logótipo!

#### <a name="fixes"></a>Correções

* Corrigido: Ecrã congelando problemas
* Corrigido: Segurança avançada
* Corrigido: Podem aparecer contas anexadas às vezes, duplicadas
* Corrigido: Um blob com um tipo de conteúdo indefinido foi possível gerar uma exceção
* Corrigido: Não foi possível abrir o painel de consulta numa tabela vazia
* Corrigido: Varia bugs na pesquisa
* Corrigido: Aumentar o número de recursos carregado de 50 a 100, quando clicar em "Mais carga"
* Corrigido: Na primeira execução, se uma conta está conectada, podemos agora selecione todas as subscrições para essa conta por predefinição

#### <a name="known-issues"></a>Problemas Conhecidos

* Esta versão do Explorador de armazenamento não é executado no Ubuntu 14.04
* Para abrir vários separadores para o mesmo recurso, não continuamente clique no mesmo recurso. Clique em outro recurso e, em seguida, volte atrás e, em seguida, clique no recurso original para abri-lo novamente no outro separador
* Acesso rápido só funciona com itens de subscrição com base. Recursos locais ou a recursos anexados através de chave ou SAS token não é suportada nesta versão
* Poderá demorar alguns segundos para navegar para o recurso de destino, dependendo de quantos recursos que tenha acesso rápido
* Ter mais de 3 grupos de blobs ou ficheiros a carregar ao mesmo tempo, poderá causar erros
* Identificadores de pesquisa, pesquisar, em aproximadamente 50 000 nós - depois disso, o desempenho poderá ser afetado ou pode fazer com que uma exceção não processada

10/03/2016
### <a name="version-085"></a>Versão 0.8.5

#### <a name="new"></a>Novo

* Agora pode utilizar chaves SAS gerado pelo Portal para anexar a contas de armazenamento e recursos

#### <a name="fixes"></a>Correções

* Corrigido: a condição de corrida durante a pesquisa, às vezes, causado nós para se tornar não expansível
* Corrigido: "Utilizar HTTP" não funciona ao ligar a contas de armazenamento com o nome da conta e chave
* Corrigido: Chaves SAS (especialmente gerado pelo Portal aqueles) retornam um erro de "à direita barra"
* Foi corrigido: tabela problemas de importação
    * Por vezes, chave de partição e chave de linha foram revertidas
    * Não é possível ler as chaves de partição "null"

#### <a name="known-issues"></a>Problemas Conhecidos

* Identificadores de pesquisa, pesquisar, em aproximadamente 50 000 nós - depois disso, o desempenho poderá ser afetado
* O Azure Stack atualmente não suporta ficheiros, para que tentar expandir ficheiros mostrará um erro

09/12/2016
### <a name="version-084"></a>Versão 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Novo

* Gerar ligações diretas para contas de armazenamento, contentores, filas, tabelas ou partilhas de ficheiros para a partilha e suporte de acesso fácil aos seus recursos - Windows e Mac OS
* Procure os seus contentores de BLOBs, tabelas, filas, partilhas de ficheiros ou as contas de armazenamento da caixa de pesquisa
* Agora pode agrupar cláusulas no construtor de consultas de tabela
* Mudar o nome e copiar/colar contentores de BLOBs, partilhas de ficheiros, tabelas, blobs, pastas de BLOBs, ficheiros e diretórios da dentro de contas anexados a SAS e contentores
* Mudar o nome e a copiar contentores de BLOBs e partilhas de ficheiros agora preservar as propriedades e metadados

#### <a name="fixes"></a>Correções

* Foi corrigido: não é possível editar entidades da tabela se contiverem Propriedades booleanas ou binárias

#### <a name="known-issues"></a>Problemas Conhecidos

* Identificadores de pesquisa, pesquisar, em aproximadamente 50 000 nós - depois disso, o desempenho poderá ser afetado

08/03/2016
### <a name="version-083"></a>Versão 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Novo

* Mudar o nome de contentores, tabelas, partilhas de ficheiros
* Experiência melhorada de construtor de consultas
* Capacidade de salvar e carregar consultas
* Direcione links para contas ou de contentores, filas, tabelas de armazenamento ou partilhas para compartilhamento e facilmente acedem aos seus recursos de ficheiros (apenas Windows - macOS suporte disponível em breve!)
* Capacidade para gerir e configurar regras CORS

#### <a name="fixes"></a>Correções

* Corrigido: Accounts Microsoft requerem reautenticação a cada 8 a 12 horas

#### <a name="known-issues"></a>Problemas Conhecidos

* Por vezes, a IU poderá ser congelada – maximiza a janela de ajuda a resolver este problema
* instalação de macOS pode exigir permissões elevadas
* Painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar subscrições
* Mudar o nome de partilhas de ficheiros, tabelas e contentores de BLOBs não preserva a metadados ou outras propriedades no contentor, por exemplo, a quota de partilha de ficheiros, o nível de acesso público ou políticas de acesso
* Mudar o nome de blobs (individualmente ou dentro de um contentor de BLOBs nome mudado) não preserva a instantâneos. Todas as outras propriedades e metadados de blobs, ficheiros e entidades são preservados durante uma mudança de nome
* Copiar ou mudar o nome de recursos não funciona dentro de contas anexados a SAS

07/07/2016
### <a name="version-082"></a>Versão 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Novo

* Contas de armazenamento estão agrupadas por subscrições; armazenamento de desenvolvimento e recursos ligados através de SAS ou de chave são apresentados no nó (Local e ligado)
* Terminar sessão das contas no painel "Definições de conta do Azure"
* Configurar definições de proxy para ativar e gerir o início de sessão
* Criar e dividir concessões de blob
* Contentores de BLOBs aberto, filas, tabelas e ficheiros com o clique único

#### <a name="fixes"></a>Correções

* Corrigido: mensagens na fila inseridas com bibliotecas .NET ou Java são corretamente dekódovat de base64
* Foi corrigido: $metrics tabelas não são apresentadas para contas de armazenamento de BLOBs
* Foi corrigido: nó de tabelas não funciona para o armazenamento local de (desenvolvimento)

#### <a name="known-issues"></a>Problemas Conhecidos

* instalação de macOS pode exigir permissões elevadas

06/15/2016
### <a name="version-080"></a>Versão 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Novo

* Suporte de partilha de ficheiros: ver, carregar, transferir, copiar arquivos e diretórios, os URIs de SAS (criar e ligar-se)
* Melhorada a experiência de utilizador para ligar ao armazenamento com chaves de conta ou dos URIs de SAS
* Exportar resultados de consultas de tabela
* Reordenação de coluna de tabela e personalização
* Exibir o $logs contentores de BLOBs e tabelas de $metrics para contas de armazenamento com a métrica ativada
* Melhorada a exportar e importar o comportamento, agora inclui o tipo de valor de propriedade

#### <a name="fixes"></a>Correções

* Foi corrigido: carregar ou transferir blobs grandes pode resultar em downloads/carregamentos incompletas
* Foi corrigido: Editar, adicionar ou importar uma entidade com um valor de cadeia de caracteres numérica ("1") irá convertê-lo para duplo
* Corrigido: Não é possível expandir o nó de tabela no ambiente de desenvolvimento local

#### <a name="known-issues"></a>Problemas Conhecidos

* tabelas de $metrics não estão visíveis para contas de armazenamento de BLOBs
* Adicionado por meio de programação de fila de mensagens pode não ser apresentada corretamente se as mensagens são codificadas usando a codificação de Base64

05/17/2016
### <a name="version-07201605090"></a>Versão 0.7.20160509.0

#### <a name="new"></a>Novo

* Falhas de melhor tratamento de erros por aplicação

#### <a name="fixes"></a>Correções

* Foi corrigido o erro em que as mensagens de barra de informações, às vezes, não é exibido quando as credenciais de início de sessão eram necessárias

#### <a name="known-issues"></a>Problemas Conhecidos

* Tabelas: Adicionar, editar ou importar uma entidade que tem uma propriedade com um valor numérico ambiguously, como "1" ou "1.0" e o utilizador tenta enviá-la como um `Edm.String`, o valor irá chegar através do cliente de API, como um Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Versão 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Novo

* Suporte de tabela: visualização, consulta, exportar, importar e operações de CRUD para entidades
* Suporte da fila: visualizar, adicionar, mensagens de retirar da fila
* Geração dos URIs de SAS para contas de armazenamento
* Ligar a contas de armazenamento com URIs SAS
* Notificações de atualização para as futuras atualizações Explorador de armazenamento
* Aspeto e funcionalidade atualizada

#### <a name="fixes"></a>Correções

* Aprimoramentos de confiabilidade e desempenho

### <a name="known-issues-amp-mitigations"></a>Problemas conhecidos &amp; atenuações

* Transferência de ficheiros de grandes BLOBs não funciona corretamente – Recomendamos que utilize o AzCopy enquanto estamos a resolver este problema
* As credenciais da conta não irão ser recuperadas nem em cache se a pasta raiz não é possível localizar ou não pode ser escrita para
* Se nós são adicionar, editar ou importar uma entidade que tem uma propriedade com um valor numérico ambiguously, como "1" ou "1.0", e o utilizador tentar enviá-la como um `Edm.String`, o valor irá chegar através do cliente de API, como um Edm.Double
* Ao importar ficheiros CSV com os registos de várias linhas, os dados podem obter cortados ou Misturou

02/03/2016

### <a name="version-07201601291"></a>Versão 0.7.20160129.1

#### <a name="fixes"></a>Correções

* Desempenho global melhorado durante o carregamento, transferência e cópia de blobs

01/14/2016

### <a name="version-07201601050"></a>Versão 0.7.20160105.0

#### <a name="new"></a>Novo

* Apoio técnico para Linux (recursos de paridade para OSX)
* Adicionar contentores de Blobs com a chave de assinaturas de acesso partilhado (SAS)
* Adicionar contas de armazenamento para o Azure na China 21Vianet
* Adicionar contas de armazenamento com os pontos finais personalizados
* Abrir e ver os blobs de texto e imagem de conteúdo
* Ver e editar propriedades de BLOBs e metadados

#### <a name="fixes"></a>Correções

* Corrigido: a carregar ou transferir um grande número de blobs (500 +) pode por vezes, fazer com que a aplicação tenha uma tela em branca
* Foi corrigido: ao definir o nível de acesso público do contentor de blob, o novo valor não é atualizado até voltar a definir o foco no contentor. Além disso, a caixa de diálogo sempre sejam padrão para "Nenhum acesso público" e não o valor atual real.
* Suportam de teclado e acessibilidade geral melhor e a interface do Usuário
* Texto do histórico de navegação estrutural encapsula quando ele for longo com espaço em branco
* Validação de entrada oferece suporte a caixa de diálogo SAS
* Armazenamento local continua a estar disponível, mesmo que as credenciais do utilizador expiraram
* Quando é eliminado um contentor de BLOBs aberta, o Explorador do blob no lado direito está fechado

#### <a name="known-issues"></a>Problemas Conhecidos

* Instalação para Linux tem a versão de gcc atualizada ou atualizado – passos para atualizar estão abaixo:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Versão 0.7.20151116.0

#### <a name="new"></a>Novo

* macOS e versões do Windows
* Início de sessão para ver as contas de armazenamento – utilizar a conta de organização, Account Microsoft, 2FA, entre outras.
* Armazenamento de desenvolvimento local (utilizar o emulador de armazenamento, apenas Windows)
* Suporte de recursos do Azure Resource Manager e clássica
* Criar e eliminar blobs, filas ou tabelas
* Procurar blobs específicos, filas ou tabelas
* Explorar o conteúdo de contentores de BLOBs
* Exibir e navegar através de diretórios
* Carregar, transferir e eliminar blobs e pastas
* Ver e editar propriedades de BLOBs e metadados
* Gerar chaves SAS
* Gerir e criar políticas de acesso armazenadas (SAP)
* Procurar blobs pelo prefixo
* Arraste ' n largar ficheiros para carregar ou transferir

#### <a name="known-issues"></a>Problemas Conhecidos

* Ao definir o nível de acesso público do contentor de blob, o novo valor não é atualizado até voltar a definir o foco no contentor
* Quando abre a caixa de diálogo para definir o nível de acesso público, ele sempre mostra "Sem acesso de público" como a predefinição e não o real valor atual
* Não é possível mudar o nome de blobs transferidos
* Quando ocorre um erro e não é apresentado o erro de estado de entradas de registo de atividade irá, por vezes, obter ficou "preso" em curso
* Por vezes, falha ou fica completamente branco ao tentar carregar ou transferir um grande número de blobs
* Por vezes, cancelar uma operação de cópia não funciona
* Durante a criação de um contentor (blob/tabela/fila), se de que introduz um nome inválido e continuar a criar outro num tipo de contentor diferente não é possível definir o foco no novo tipo
* Não é possível criar a nova pasta ou mudar o nome de pasta




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
