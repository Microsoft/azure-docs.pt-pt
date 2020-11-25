---
title: Instale e desenvolva localmente com o Azure Cosmos DB Emulator
description: Aprenda a instalar e utilizar o Emulador Azure Cosmos DB nos ambientes Windows, Linux, macOS e Windows Docker. Utilizando o emulador pode desenvolver e testar a sua aplicação localmente gratuitamente, sem criar uma subscrição Azure.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/22/2020
ms.custom: devx-track-csharp, contperfq1
ms.openlocfilehash: 6fb1ef45ff4e77ab21480a1013a3cec6bcc7cfa1
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030905"
---
# <a name="install-and-use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Instale e utilize o Emulador Azure Cosmos DB para desenvolvimento e testes locais
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço do Azure Cosmos DB para fins de desenvolvimento. Ao utilizar o Emulador do Azure Cosmos DB, pode programar e testar a sua aplicação localmente, sem criar uma subscrição do Azure ou incorrer em custos. Quando estiver satisfeito com o funcionamento da sua aplicação no Emulador Azure Cosmos DB, pode mudar para usar uma conta Azure Cosmos na nuvem. Este artigo descreve como instalar e utilizar o emulador em ambientes Windows, Linux, macOS e Windows Docker.

## <a name="download-the-emulator"></a>Transferir o emulador

Para começar, descarregue e instale a versão mais recente do Azure Cosmos DB Emulator no seu computador local. O [artigo de lançamento do emulador](local-emulator-release-notes.md) lista todas as versões disponíveis e as atualizações de funcionalidades que foram feitas em cada versão.

:::image type="icon" source="media/local-emulator/download-icon.png" border="false":::**[Descarregue o Emulador Azure Cosmos DB](https://aka.ms/cosmosdb-emulator)**

Você pode desenvolver aplicações usando Azure Cosmos DB Emulator com as contas [SQL](local-emulator.md#sql-api), [Cassandra,](local-emulator.md#cassandra-api) [MongoDB,](local-emulator.md#azure-cosmos-dbs-api-for-mongodb) [Gremlin](local-emulator.md#gremlin-api)e [Table](local-emulator.md#table-api) API. Atualmente, o explorador de dados no emulador suporta totalmente a visualização de dados SQL; os dados criados com recurso a aplicações de clientes MongoDB, Gremlin/Graph e Cassandra não são visualizados neste momento. Para saber mais, consulte [como ligar-se ao ponto final do emulador](#connect-with-emulator-apis) de diferentes APIs.

## <a name="how-does-the-emulator-work"></a>Como funciona o emulador?

O Emulador do Azure Cosmos DB fornece uma emulação de alta-fidelidade do serviço Azure Cosmos DB. Suporta funcionalidades equivalentes como a DB Azure Cosmos, que inclui a criação de dados, consulta de dados, provisões e dimensionamento de contentores, e a execução de procedimentos e gatilhos armazenados. Você pode desenvolver e testar aplicações usando o Emulador Azure Cosmos DB, e implantá-las para Azure à escala global, atualizando o ponto final de ligação Azure Cosmos DB.

Apesar de a emulação do serviço do Azure Cosmos DB ser fiel, a implementação do emulador é diferente do serviço. Por exemplo, o emulador utiliza componentes standard do SO, como o sistema de ficheiros local para persistência e a pilha de protocolo HTTPS para conectividade. Funcionalidade que se baseia na infraestrutura Azure como a replicação global, latência milissegundo de um dígito para leituras/escritas, e níveis de consistência incapazes não são aplicáveis quando se utiliza o emulador.

Pode migrar dados entre o Emulador Azure Cosmos DB e o serviço DB Azure Cosmos utilizando a [Ferramenta de Migração de Dados Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>Diferenças entre o emulador e o serviço de nuvem

Como o Emulador DB Azure Cosmos proporciona um ambiente emulado que funciona na estação de trabalho do desenvolvedor local, existem algumas diferenças na funcionalidade entre o emulador e uma conta Azure Cosmos na nuvem:

* Atualmente, o painel **do Data Explorer** no emulador suporta totalmente apenas os clientes API SQL. A visão e operações do **Data Explorer** para APIs DB Azure Cosmos, tais como MongoDB, Tabela, Gráfico e ApIs cassandra não estão totalmente suportados.

* O emulador suporta apenas uma única conta fixa e uma chave primária bem conhecida. Não é possível regenerar a tecla quando utilizar o Emulador Azure Cosmos DB, no entanto pode alterar a tecla predefinida utilizando a opção [de linha de comando.](emulator-command-line-parameters.md)

* Com o emulador, pode criar uma conta Azure Cosmos apenas em modo [de produção a provisionado;](set-throughput.md) atualmente não suporta o modo [sem servidor.](serverless.md)

* O emulador não é um serviço escalável e não suporta um grande número de contentores. Ao utilizar o Emulador Azure Cosmos DB, por padrão, pode criar até 25 recipientes de tamanho fixo a 400 RU/s (apenas suportados com Azure Cosmos DB SDKs), ou 5 contentores ilimitados. Para obter mais informações sobre como alterar este valor, consulte definir o artigo [valor partitionCount.](emulator-command-line-parameters.md#set-partitioncount)

* O emulador não oferece [diferentes níveis de consistência Azure Cosmos DB](consistency-levels.md) como o serviço de nuvem oferece.

* O emulador não oferece [replicação multi-região.](distribute-data-globally.md)

* Como a cópia do seu Azure Cosmos DB Emulator pode nem sempre estar atualizada com as mudanças mais recentes no serviço DB Azure Cosmos, deve sempre consultar o planejador de [capacidades DB da Azure Cosmos](estimate-ru-with-capacity-planner.md) para estimar com precisão as necessidades de produção (RUs) da sua aplicação.

* O emulador suporta um tamanho máximo de propriedade de ID de 254 caracteres.

## <a name="install-the-emulator"></a>Instale o emulador

Antes de instalar o emulador, certifique-se de que tem os seguintes requisitos de hardware e software:

* Requisitos de software:
  * Atualmente o Windows Server 2012 R2, Windows Server 2016, 2019 ou Windows 8, 10 host OS são suportados. O sistema operativo anfitrião com Ative Directory ativado não está atualmente suportado.
  * Sistema operativo de 64 bits

* Requisitos mínimos de hardware:
  * 2 GB de RAM
  * 10 GB de espaço disponível no disco rígido

* Para instalar, configurar e executar o Emulador do Azure Cosmos DB, tem de ter privilégios de administrador no computador. O emulador adicionará um certificado e também definirá as regras de firewall para executar os seus serviços. Por conseguinte, os direitos administrativos são necessários para que o emulador possa executar tais operações.

Para começar, descarregue e instale a versão mais recente do [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) no seu computador local. Se encontrar algum problema ao instalar o emulador, consulte o artigo [de resolução de problemas do emulador](troubleshoot-local-emulator.md) para depurar.

Dependendo dos requisitos do seu sistema, pode executar o emulador no [Windows](#run-on-windows), [Docker para Windows](#run-on-windows-docker), [Linux ou macOS,](#run-on-linux-macos) conforme descrito nas próximas secções deste artigo.

## <a name="check-for-emulator-updates"></a>Verifique se há atualizações do emulador

Cada versão do emulador vem com um conjunto de atualizações de funcionalidades ou correções de erros. Para ver as versões disponíveis, leia o artigo de notas de lançamento do [emulador.](local-emulator-release-notes.md)

Após a instalação, se tiver utilizado as definições predefinidas, os dados correspondentes ao emulador são guardados na localização do %LOCALAPPDATA%\CosmosDBEmulator. Pode configurar uma localização diferente utilizando as definições opcionais do caminho dos dados; que é o `/DataPath=PREFERRED_LOCATION` parâmetro da linha de [comando.](emulator-command-line-parameters.md) Os dados criados numa versão do Azure Cosmos DB Emulator não estão garantidos para serem acessíveis quando se utiliza uma versão diferente. Se precisar de persistir os seus dados a longo prazo, recomenda-se que guarde esses dados numa conta Azure Cosmos, em vez do Emulador Azure Cosmos DB.

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>Utilize o emulador no Windows

O Emulador Azure Cosmos DB é instalado no `C:\Program Files\Azure Cosmos DB Emulator` local por defeito. Para iniciar o Emulador DB Azure Cosmos no Windows, selecione o botão **Iniciar** ou prima a tecla Windows. Comece a escrever **Emulador do Azure Cosmos DB** e selecione o emulador na lista de aplicações.

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="Selecione o botão Iniciar ou prima a tecla Windows, comece a digitar o Emulador Azure Cosmos DB e selecione o emulador da lista de aplicações":::

Quando o emulador tiver começado, verá um ícone na área de notificação da barra de tarefas do Windows. Abre automaticamente o explorador de dados Azure Cosmos no seu navegador neste `https://localhost:8081/_explorer/index.html` URL URL.

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Azure Cosmos DB notificação de barra de tarefa local":::

Também pode iniciar e parar o emulador a partir da linha de comando ou dos comandos PowerShell. Para obter mais informações, consulte o artigo [de referência da ferramenta da linha de comando.](emulator-command-line-parameters.md)

Por predefinição, o Emulador do Azure Cosmos DB é executado no computador local ("localhost") que está a escutar na porta 8081. O endereço é apresentado como `https://localhost:8081/_explorer/index.html`. Se fechar o explorador e quiser abri-lo novamente mais tarde, pode abrir o URL no browser ou iniciá-lo a partir do Emulador do Azure Cosmos DB no Ícone de Tabuleiro de Windows, conforme mostrado abaixo.

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Lançador local de explorador de dados Azure Cosmos":::

## <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>Use o emulador no Docker para windows

Pode executar o Emulador Azure Cosmos DB no recipiente Windows Docker. Consulte o [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) para o comando de puxar o estivador e [o GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) para `Dockerfile` obter mais informações. Atualmente, o emulador não trabalha no Docker para a Oracle Linux. Utilize as seguintes instruções para executar o emulador no Docker para windows:

1. Depois de ter [o Docker para o Windows](https://www.docker.com/docker-windows) instalado, mude para os recipientes do Windows clicando corretamente no ícone Docker na barra de ferramentas e selecionando o Switch para os **recipientes do Windows**.

1. Em seguida, extraia a imagem do emulador do Hub do Docker, executando o seguinte comando a partir da sua shell favorita.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. Para iniciar a imagem, executar os seguintes comandos dependendo da linha de comando ou do ambiente PowerShell:

   # <a name="command-line"></a>[Linha de comandos](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   As imagens do Docker baseadas no Windows podem não ser geralmente compatíveis com todos os SISTEMAS do Windows. Por exemplo, a imagem emuladora padrão do Azure Cosmos DB só é compatível com o Windows 10 e o Windows Server 2016. Se precisar de uma imagem compatível com o Windows Server 2019, em vez disso, execute o seguinte comando:

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   A resposta é semelhante à seguinte:

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > Ao executar o `docker run` comando, se vir um erro de conflito na porta (isto é, se a porta especificada já estiver em uso), passe uma porta personalizada alterando os números da porta. Por exemplo, pode alterar o parâmetro "-p 8081:8081" para "-p 443:8081"

1. Agora use o ponto final do emulador e a chave primária da resposta e importe o certificado TLS/SSL para o seu anfitrião. Para importar o certificado TLS/SSL, executar os seguintes passos a partir de um pedido de comando administrativo:

   # <a name="command-line"></a>[Linha de comandos](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. Se fechar a concha interativa depois do emulador ter começado, desligará o recipiente do emulador. Para reabrir o explorador de dados, navegue para o seguinte URL no seu navegador. O ponto final do emulador é fornecido na mensagem de resposta mostrada acima.

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Se tiver uma aplicação de cliente .NET a funcionar num recipiente de estival Linux e se estiver a executar o Emulador Azure Cosmos DB numa máquina hospedeira, utilize as instruções na secção seguinte para importar o certificado para o recipiente linux.

### <a name="regenerate-the-emulator-certificates-when-running-on-a-docker-container"></a>Regenerar os certificados de emulador ao correr num recipiente Docker

Ao executar o emulador num recipiente Docker, os certificados associados ao emulador são regenerados sempre que para e reinicia o respetivo recipiente. Por isso, tem de re importar os certificados após o início de cada contentor. Para contornar esta limitação, pode utilizar um ficheiro de composição do Docker para ligar o contentor Docker a um determinado endereço IP e a uma imagem de contentor.

Por exemplo, pode utilizar a seguinte configuração dentro do ficheiro de composição do Docker, certifique-se de que o forma de acordo com o seu requisito: 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>Utilize o emulador no Linux ou no macOS

Atualmente, o Emulador Azure Cosmos DB só pode ser executado no Windows. Se estiver a utilizar o Linux ou o macOS, pode executar o emulador numa máquina virtual Windows hospedada num hipervisor, como Paralelos ou VirtualBox.

> [!NOTE]
> Sempre que reinicia a máquina virtual do Windows que está hospedada num hipervisor, tem de reimportar o certificado porque o endereço IP da máquina virtual muda. A importação do certificado não é necessária no caso de ter configurado a máquina virtual para preservar o endereço IP.

Utilize os seguintes passos para utilizar o emulador em ambientes Linux ou macOS:

1. Executar o seguinte comando a partir da máquina virtual do Windows e tome nota do endereço IPv4:

   ```bash
   ipconfig.exe
   ```

1. Dentro da sua aplicação, altere o URL do ponto final para utilizar o endereço IPv4 devolvido `ipconfig.exe` em vez de `localhost` .

1. A partir do Windows VM, lance o Emulador Azure Cosmos DB a partir da linha de comando utilizando as seguintes opções. Para obter mais informações sobre os parâmetros suportados pela linha de comando, consulte a [referência da ferramenta da linha de comando do emulador:](emulator-command-line-parameters.md)

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. Finalmente, você precisa resolver o processo de confiança do certificado entre a aplicação em execução no ambiente Linux ou Mac e o emulador. Pode utilizar uma das duas opções seguintes para resolver o certificado:

   1. [Importar o certificado emulador TLS/SSL para o ambiente Linux ou Mac ou](#import-certificate)
   2. [Desativar a validação TLS/SSL na aplicação](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>Opção 1: Importar o certificado emulador TLS/SSL

As seguintes secções mostram como importar o certificado emulador TLS/SSL para ambientes Linux e macOS.

#### <a name="linux-environment"></a>Ambiente linux

Se estiver a trabalhar no Linux, relés .NET no OpenSSL para fazer a validação:

1. [Exportar o certificado em formato PFX.](local-emulator-export-ssl-certificates.md#export-emulator-certificate) A opção PFX está disponível na escolha de exportar a chave privada.

1. Copie o ficheiro PFX para o seu ambiente Linux.

1. Converter o ficheiro PFX num ficheiro CRT

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Copie o ficheiro CRT para a pasta que contém certificados personalizados na sua distribuição Linux. Comumente nas distribuições de Debian, está localizado em `/usr/local/share/ca-certificates/` .

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Atualize os certificados TLS/SSL, que atualizarão a `/etc/ssl/certs/` pasta.

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>ambiente macOS

Utilize os seguintes passos se estiver a trabalhar no Mac:

1. [Exportar o certificado em formato PFX.](local-emulator-export-ssl-certificates.md#export-emulator-certificate) A opção PFX está disponível na escolha de exportar a chave privada.

1. Copie o ficheiro PFX para o seu ambiente Mac.

1. Abra a aplicação *Keychain Access* e importe o ficheiro PFX.

1. Abra a lista de Certificados e identifique o com o `localhost` nome.

1. Abra o menu de contexto para esse item em particular, *selecione Get Item* e em *Trust* Ao utilizar  >  esta opção de *certificado,* selecione *Always Trust*. 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="Abra o menu de contexto para esse item em particular, selecione Get Item e em Trust - Quando utilizar esta opção de certificado, selecione Always Trust":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>Opção 2: Desativar a validação SSL na aplicação

A desativação da validação SSL só é recomendada para fins de desenvolvimento e não deve ser feita quando funciona num ambiente de produção. Os exemplos a seguir mostram como desativar a validação SSL para aplicações .NET e Node.js.

# <a name="net-standard-21"></a>[.NET Standard 2.1+](#tab/ssl-netstd21)

Para qualquer aplicação em execução num quadro compatível com .NET Standard 2.1 ou posterior, podemos aproveitar o `CosmosClientOptions.HttpClientFactory` seguinte:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2.0](#tab/ssl-netstd20)

Para qualquer aplicação em execução num quadro compatível com .NET Standard 2.0, podemos alavancar o `CosmosClientOptions.HttpClientFactory` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.js](#tab/ssl-nodejs)

Para Node.js aplicações, pode modificar o seu `package.json` ficheiro para definir o `NODE_TLS_REJECT_UNAUTHORIZED` enquanto inicia a aplicação:

```json
"start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
```
--- 

## <a name="enable-access-to-emulator-on-a-local-network"></a>Permitir o acesso ao emulador numa rede local

Se tiver várias máquinas utilizando uma única rede, e se configurar o emulador numa máquina e quiser aceder a ela a partir de outra máquina. Nesse caso, é necessário permitir o acesso ao emulador numa rede local.

Pode executar o emulador numa rede local. Para permitir o acesso à rede, especifique a `/AllowNetworkAccess` opção na [linha de comando,](emulator-command-line-parameters.md)que também requer que especifique `/Key=key_string` ou `/KeyFile=file_name` . Pode utilizar `/GenKeyFile=file_name` para gerar um ficheiro com uma chave aleatória. Então podes passar isso para `/KeyFile=file_name` `/Key=contents_of_file` ou.

Para permitir o acesso à rede pela primeira vez, o utilizador deve desligar o emulador e eliminar o diretório de dados do emulador *%LOCALAPPDATA%\CosmosDBEmulator*.

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>Autenticar ligações ao utilizar o emulador

Tal como no Azure Cosmos DB na cloud, todos os pedidos que fizer relativamente ao Emulador do Azure Cosmos DB têm de ser autenticados. O Emulador Azure Cosmos DB suporta apenas uma comunicação segura via TLS. O Emulador DB Azure Cosmos suporta uma única conta fixa e uma chave de autenticação bem conhecida para a autenticação da chave primária. Esta conta e chave são as únicas credenciais permitidas para utilização com o Emulador do Azure Cosmos DB. São:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> A chave primária suportada pelo Emulador Azure Cosmos DB destina-se a ser utilizada apenas com o emulador. Não é possível utilizar a conta e chave do Azure Cosmos DB com o Emulador do Azure Cosmos DB.

> [!NOTE]
> Se tiver iniciado o emulador com a opção /Chave, utilize a chave gerada em vez da tecla predefinida `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` . Para obter mais informações sobre a opção /Chave, consulte [a referência da ferramenta da linha de comando.](emulator-command-line-parameters.md)

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>Ligue-se a diferentes APIs com o emulador

### <a name="sql-api"></a>API SQL

Assim que tiver o emulador do Azure Cosmos DB em execução no seu ambiente de trabalho, pode utilizar qualquer SDK do [Azure Cosmos DB](sql-api-sdk-dotnet-standard.md) ou [API REST do Azure Cosmos DB](/rest/api/cosmos-db/) suportados para interagir com o emulador. O Azure Cosmos DB Emulator também inclui um explorador de dados incorporado que permite criar recipientes para API SQL ou Azure Cosmos DB para Mongo DB API. Ao utilizar o explorador de dados, pode visualizar e editar itens sem escrever qualquer código.

```csharp
// Connect to the Azure Cosmos DB Emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

Assim que tiver o Emulador Azure Cosmos DB a funcionar no seu ambiente de trabalho, pode utilizar a [API da Azure Cosmos DB para a MongoDB](mongodb-introduction.md) interagir com o emulador. Inicie o emulador a partir do pedido de [comando](emulator-command-line-parameters.md) como administrador com "/EnableMongoDbEndpoint". Em seguida, utilize o seguinte fio de ligação para ligar à conta API mongodb:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>API de Tabela

Assim que tiver o Emulador Azure Cosmos DB a funcionar no seu ambiente de trabalho, pode utilizar a [Azure Cosmos DB Table API SDK](./tutorial-develop-table-dotnet.md) para interagir com o emulador. Inicie o emulador a partir do pedido de [comando](emulator-command-line-parameters.md) como administrador com "/EnableTableEndpoint". Em seguida, executar o seguinte código para ligar à conta API tabela:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>API de Cassandra

Inicie o emulador a partir de um pedido de [comando](emulator-command-line-parameters.md) do administrador com "/EnableCassandraEndpoint". Em alternativa, também pode definir a variável `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true` ambiente.

1. [Instalar Python 2.7](https://www.python.org/downloads/release/python-2716/)

1. [Instalar Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

1. Executar os seguintes comandos numa janela de pedido de comando regular:

   ```bash
   set Path=c:\Python27;%Path%
   cd /d C:\sdk\apache-cassandra-3.11.3\bin
   set SSL_VERSION=TLSv1_2
   set SSL_VALIDATE=false
   cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
   ```

1. Na concha CQLSH, executar os seguintes comandos para ligar ao ponto final de Cassandra:

   ```bash
   CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
   DESCRIBE keyspaces;
   USE mykeyspace;
   CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
   INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
   SELECT * from table1;
   EXIT
   ```

### <a name="gremlin-api"></a>API do Gremlin

Inicie o emulador a partir de um pedido de [comando](emulator-command-line-parameters.md)do administrador com "/EnableGremlinEndpoint". Em alternativa, também pode definir a variável ambiental `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

1. [Instale apache-tinkerpop-gremlin-consola-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

1. A partir do explorador de dados do emulador, cria-se uma base de dados "db1" e uma coleção "coll1"; para a chave de partição, escolha "/nome"

1. Executar os seguintes comandos numa janela de pedido de comando regular:

   ```bash
   cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
   copy /y conf\remote.yaml conf\remote-localcompute.yaml
   notepad.exe conf\remote-localcompute.yaml
     hosts: [localhost]
     port: 8901
     username: /dbs/db1/colls/coll1
     password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
     connectionPool: {
     enableSsl: false}
     serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
     config: { serializeResultToString: true  }}

   bin\gremlin.bat
   ```

1. Na concha de Gremlin, executar os seguintes comandos para ligar ao ponto final de Gremlin:

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>Desinstalar o emulador local

Utilize os seguintes passos para desinstalar o emulador:

1. Saia de todas as instâncias abertas do emulador local clicando à direita no ícone **emulador Azure Cosmos DB** na bandeja do sistema e, em seguida, selecione **Saída**. Pode demorar um minuto para que todas as instâncias possam sair.

1. Na caixa de pesquisa do Windows, **escreva apps & funcionalidades** e selecione apps & resultado **(definições do sistema).**

1. Na lista de aplicações, percorra o **Emulador Azure Cosmos DB**, selecione-o, clique em **Desinstalar,** e depois confirme e selecione **Desinstalar** novamente.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar o emulador local para o desenvolvimento local gratuito. Pode agora passar para os próximos artigos:

* [Exporte os certificados emuladores Azure Cosmos DB para uso com aplicativos Java, Python e Node.js](local-emulator-export-ssl-certificates.md)
* [Utilize parâmetros de linha de comando e comandos PowerShell para controlar o emulador](emulator-command-line-parameters.md)
* [Problemas de depurar com o emulador](troubleshoot-local-emulator.md)
