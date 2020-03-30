---
title: Desenvolver uma aplicação web segura [ Microsoft Docs
description: Esta simples aplicação de amostra implementa as melhores práticas de segurança que melhoram a sua aplicação e a postura de segurança da sua organização quando se desenvolve no Azure.
keywords: na
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 75890efebc42b74c56fb95ed1803152b516588b9
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385219"
---
# <a name="develop-a-secure-web-app"></a>Desenvolver uma aplicação Web segura

Esta amostra é uma simples aplicação Python que exibe uma página web contendo links para recursos de segurança para o desenvolvimento de aplicações no Azure. A aplicação implementa as melhores práticas de segurança que podem ajudar a melhorar a sua aplicação e a postura de segurança da sua organização quando desenvolve aplicações no Azure.

Deve seguir os passos descritos neste artigo sequencialmente para garantir que os componentes da aplicação estão corretamente configurados. A base de dados, o Azure App Service, a instância Azure Key Vault e a instância de Gateway de Aplicação Azure dependem umas das outras.

Os scripts de implantação configuram a infraestrutura. Depois de executar os scripts de implementação, terá de fazer alguma configuração manual no portal Azure para ligar os componentes e serviços em conjunto.

A aplicação de amostras destina-se a iniciantes que desenvolvam aplicações no Azure que pretendam implementar medidas de segurança nas suas aplicações.

Ao desenvolver e implementar esta aplicação, você vai aprender a:

- Crie uma instância azure key vault, guarde e recupere segredos dele.
- Implemente a Base de Dados Azure para PostgreSQL, configura risque senhas seguras e autorize o acesso à si.
- Faça um recipiente Alpine Linux em Web Apps Azure para Linux e permita identidades geridas para recursos Azure.
- Crie e configure uma instância de Gateway de aplicação Azure com uma firewall que utilize [o Top 10 Ruleset OWASP](https://coreruleset.org/).
- Ativar a encriptação de dados em trânsito e em repouso utilizando os serviços Azure.

Depois de desenvolver e implementar esta aplicação, terá configurado a seguinte aplicação web de amostra, juntamente com as medidas de configuração e segurança descritas.

![App web de amostra](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Arquitetura

A aplicação é uma aplicação típica de n-tier com três níveis. A parte frontal, a parte traseira e a camada de base de dados com componentes de monitorização e gestão secreta integrados são mostradas aqui:

![Arquitetura de aplicações](./media/secure-web-app/architecture.png)

A arquitetura é constituída por estes componentes:

- [Gateway de aplicação Azure](../../application-gateway/index.yml). Fornece o portal e firewall para a nossa arquitetura de aplicação.
- [Aplicativos Web Azure no Linux.](../../app-service/containers/app-service-linux-intro.md) Fornece o tempo de execução do contentor para executar a app Python em um ambiente Linux.
- [Cofre de Chaves Azure.](../../key-vault/index.yml) Armazena e encripta os segredos da nossa aplicação e gere a criação de políticas de acesso à sua volta.
- [Base de Dados Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql/). Armazena de forma segura os dados da nossa aplicação.
- [Azure Security Center](../../security-center/index.yml) e [Azure Application Insights.](../../azure-monitor/app/app-insights-overview.md) Fornece monitorização e alertas sobre o funcionamento da nossa app.

## <a name="threat-model"></a>Modelo de ameaça

A modelação de ameaças é o processo de identificação de potenciais ameaças à segurança para o seu negócio e aplicação e, em seguida, garantir que um plano de mitigação adequado está em vigor.

Esta amostra utilizou a Ferramenta de [Modelação de Ameaças](threat-modeling-tool.md) da Microsoft para implementar o modelamento de ameaças para a aplicação de amostra segura. Ao diagramar os componentes e os fluxos de dados, pode identificar problemas e ameaças no início do processo de desenvolvimento. Isto poupa tempo e dinheiro mais tarde.

Este é o modelo de ameaça para a aplicação de amostras:

![Modelo de ameaça](./media/secure-web-app/threat-model.png)

Algumas ameaças de amostra e potenciais vulnerabilidades que a ferramenta de modelação de ameaças gera são mostradas na seguinte imagem. O modelo de ameaça dá uma visão geral da superfície de ataque exposta e leva os desenvolvedores a pensar em como mitigar os problemas.

![Saída do modelo de ameaça](./media/secure-web-app/threat-model-output.png)

Por exemplo, a injeção SQL na saída do modelo de ameaça anterior é atenuada pela sanitização das inputs dos utilizadores e utilizando funções armazenadas na Base de Dados Azure para postgreSQL. Esta mitigação impede a execução arbitrária de consultas durante leituras e escritos de dados.

Os desenvolvedores melhoram a segurança geral do sistema atenuando cada uma das ameaças na saída do modelo de ameaça.

## <a name="deployment"></a>Implementação

As seguintes opções permitem-lhe executar o Linux no Serviço de Aplicações Azure:

- Escolha um recipiente da lista de recipientes pré-construídos da Microsoft no Azure que tenham sido criados com tecnologias de suporte (Python, Ruby, PHP, Java, Node.js, .NET Core).
- Utilize um recipiente personalizado. Selecione os seus próprios registos de contentores como fonte da imagem e baseie-se nas muitas tecnologias disponíveis que suportam http.

Neste exemplo, você executará o script de implementação que irá implementar a webapp no App Service e criar os recursos.

A aplicação pode utilizar os diferentes modelos de implementação apresentados abaixo:

![Diagrama de fluxo de dados de implementação](./media/secure-web-app/deployment.png)

Existem muitas formas de implementar aplicações no Azure, incluindo:

- Modelos do Azure Resource Manager
- PowerShell
- CLI do Azure
- Portal do Azure
- DevOps do Azure

Esta aplicação utilizou:

- [Docker](https://docs.docker.com/) para criar e construir as imagens do recipiente.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para implantação.
- [Docker Hub](https://hub.docker.com/) como registo de contentores.

## <a name="security-considerations"></a>Considerações de segurança

### <a name="network"></a>Rede

A aplicação da amostra utiliza encriptação SSL de ponta a ponta para dados em trânsito que fluem para dentro e para fora da rede. O portal está configurado com um certificado auto-assinado.
> [!IMPORTANT]
> Um certificado auto-assinado é usado nesta demonstração. Em ambiente de produção, deverá obter certificados de uma Autoridade de Certificados Verificados (CA).

A firewall da aplicação também inspeciona o tráfego de entrada e alerta os administradores quando o tráfego malicioso é detetado no tráfego da rede.
O Application Gateway atenua a possibilidade de ameaças de injeção DDoS e SQL descobertas no modelo de ameaça.

### <a name="identity"></a>Identidade

Para iniciar sessão no portal, a aplicação de amostra utiliza a Autenticação Multi-Factor para administradores do Azure Ative Directory (Azure AD) a quem é atribuído acesso aos recursos.
A aplicação de amostra sustá-a usa identidades geridas para obter permissões para ler e recuperar segredos do Azure Key Vault, garantindo que a aplicação não precisa de credenciais de código rígido e fichas para ler os segredos. A Azure AD cria automaticamente os diretores de serviço que a app precisa de ler e modifica os segredos quando são utilizadas identidades geridas.

Identidades geridas para os recursos Azure e MFA dificultam que os adversários ganhem privilégios e aumentem os seus privilégios no sistema. Esta ameaça foi apontada no modelo de ameaça.
A aplicação utiliza a OAuth, que permite aos utilizadores registados na aplicação OAuth iniciars sessão na aplicação.

### <a name="storage"></a>Storage

Os dados na base de dados PostgreSQL são encriptados automaticamente pela Base de Dados Azure para PostgreSQL. A base de dados autoriza os endereços IP do Serviço de Aplicações para que apenas a aplicação web do Serviço de Aplicações implementada possa aceder aos recursos da base de dados com as credenciais de autenticação adequadas.

### <a name="logging-and-auditing"></a>Registo e auditoria

A aplicação implementa o registo através da utilização de Insights de Aplicação para rastrear métricas, registos e exceções que ocorrem. Esta exploração de registos fornece metadados de aplicações suficientes para informar os desenvolvedores e membros da equipa de operações sobre o estado da app. Também fornece dados suficientes para recuar em caso de incidentes de segurança.

## <a name="cost-considerations"></a>Considerações de custos

Se ainda não tem uma conta Azure, pode criar uma gratuita. Vá à página de [conta gratuita](https://azure.microsoft.com/free/) para começar, veja o que pode fazer com uma conta Azure gratuita e saiba quais os produtos gratuitos durante 12 meses.

Para implementar os recursos na aplicação de amostracom as funcionalidades de segurança, precisa de pagar algumas funcionalidades premium. À medida que as escalas de aplicações e os níveis e testes gratuitos oferecidos pelo Azure precisam de ser atualizados para satisfazer os requisitos de aplicação, os seus custos podem aumentar. Utilize a [calculadora](https://azure.microsoft.com/pricing/calculator/) de preços Azure para estimar os seus custos.

## <a name="deploy-the-solution"></a>Implementar a solução

### <a name="prerequisites"></a>Pré-requisitos

Para que a aplicação esteja a funcionar, é necessário instalar estas ferramentas:

- Um editor de código para modificar e ver o código de aplicação. [Visual Studio Code](https://code.visualstudio.com/) é uma opção de código aberto.
- [Azure CLI](/cli/azure/install-azure-cli) no seu computador de desenvolvimento.
- [Git](https://git-scm.com/) no seu sistema. Git é usado para clonar o código fonte localmente.
- [jq](https://stedolan.github.io/jq/), uma ferramenta UNIX para consulta jSON de uma forma amigável para o utilizador.

Precisa de uma subscrição Azure para implementar os recursos da aplicação de amostra. Se não tiver uma subscrição azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/) para testar a aplicação de amostras.

Depois de instalar estas ferramentas, está pronto para implementar a aplicação no Azure.

### <a name="environment-setup"></a>Configuração de ambiente

Executar os scripts de implementação para configurar o ambiente e a subscrição:

1. Para clonar o repositório de código fonte, utilize este comando Git:

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. Para avançar para o diretório, use este comando:

   ```shell
   cd tutorial-project/scripts
   ```

3. Existem ficheiros na pasta de scripts que são específicos da plataforma que está a utilizar (Windows ou Linux). Como o Azure CLI já foi instalado, inscreva-se na conta Azure no pedido de comando, executando este comando Azure CLI:

   ```azurecli-interactive
   az login
   ```

O navegador abrirá, inscreva-se com as suas credenciais. Depois de iniciar a sua inscrição, pode começar a mobilizar os recursos a partir do pedido de comando.

Os scripts `deploy-powershell.ps1` `deploy-bash.sh` de implantação e contêm código que implementa toda a aplicação.
Para implementar a solução:

1. Se estiver na PowerShell `deploy-powershell.ps1` execute `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` o ficheiro escrevendo substituindo a região e o nome do grupo de recursos por regiões Azure adequadas e um nome para o grupo de recursos
2. Se estiver no Linux `deploy-bash.sh` executar `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`o ficheiro digitando, poderá ter de tornar o ficheiro executável escrevendo`chmod +x deploy-bash.sh`

Os exemplos que se seguem mostram fragmentos dos componentes-chave. Pode implementar os exemplos individualmente ou com o resto dos componentes executando os ficheiros de implementação.

### <a name="implementation-guidance"></a>Orientação de implementação

O script de implantação é um script que pode ser dividido em quatro fases. Cada fase implanta e configura um recurso Azure que está no diagrama de [arquitetura.](#architecture)

As quatro fases são:

- Implante o cofre da chave Azure.
- Implementar base de dados Azure para PostgreSQL.
- Implemente aplicações Web Azure no Linux.
- Implementar Gateway de aplicações com firewall de aplicação web.

Cada fase baseia-se na anterior utilizando a configuração dos recursos previamente implantados.

Para completar os passos de implementação, certifique-se de que instalou as ferramentas listadas em [pré-requisitos](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Implementar cofre de chave azure

Nesta secção, cria-se e implementa-se uma instância azure key vault que é usada para armazenar segredos e certificados.

Depois de completar a implantação, tem uma instância azure key vault implantada no Azure.

Para implantar o Cofre chave Azure utilizando o Azure CLI:

1. Declare as variáveis para o Cofre chave Azure.
2. Registe o fornecedor azure key vault.
3. Crie o grupo de recursos, por exemplo.
4. Crie a instância Azure Key Vault no grupo de recursos criado no passo 3.

   ```powershell-interactive

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```

É uma melhor prática usar identidades geridas para recursos Azure em apps que usam Key Vault para aceder a recursos. A sua postura de segurança aumenta quando as chaves de acesso ao Cofre chave não são armazenadas em código ou na configuração.

#### <a name="deploy-azure-database-for-postgresql"></a>Implementar base de dados Azure para PostgreSQL

A Base de Dados Azure para PostgreSQL funciona da seguinte forma, primeiro criar o servidor de base de dados e depois criar a base de dados para armazenar o esquema e os dados.

Depois de concluir a implementação, tem um servidor PostgreSQL e uma base de dados em funcionamento no Azure.

Para implementar a Base de Dados Azure para PostgreSQL utilizando o Azure CLI:

1. Abra um terminal com o Azure CLI e a sua configuração de subscrição Azure.
2. Gere uma combinação segura de nome de utilizador e palavra-passe que seja usada para aceder à base de dados. (Estes devem ser armazenados no Cofre chave Azure para aplicações que as utilizam.)
3. Crie a instância do servidor PostgreSQL.
4. Crie uma base de dados na instância do servidor que criou no passo 3.
5. Executar scripts PostgreSQL na instância PostgreSQL.

O código abaixo baseia-se nos segredos PGUSERNAME e PGPASSWORD armazenados no Azure KeyVault a partir do keyVault de implantação acima.

   ```powershell-interactive
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

Depois de implementar a base de dados, tem de armazenar as suas credenciais e cordas de ligação no Cofre de Chaves Azure.
Na pasta de scripts, `functions.sql` há um ficheiro que contém o código PL/pgSQL que cria funções armazenadas quando o executa. Executar este ficheiro parametriza as inputs para limitar a injeção de SQL.

O PostgreSQL é agregado `psql` com uma ferramenta chamada que é usada para ligar à base de dados. Para `functions.sql`ser executado, precisa de ligar à Base de Dados Azure para a instância PostgreSQL a partir da sua máquina local e executá-la a partir daí. A instalação da ferramenta psql está incluída na instalação predefinida para PostgreSQL em cada sistema operativo.
Para mais informações, consulte a [Documentação psql](https://www.postgresql.org/docs/9.3/app-psql.html).

A Azure Cloud `psql` Shell também inclui a ferramenta. Pode utilizar a Cloud Shell diretamente a partir do portal Azure, selecionando o Ícone cloud Shell.

Para permitir o acesso remoto à instância PostgreSQL, tem de autorizar o endereço IP no PostgreSQL.
Ativa este acesso indo ao separador de **segurança Connection,** selecionando Adicionar IP do **cliente,** e guardando as novas definições.

![Autorizar o cliente IP](./media/secure-web-app/add-client-ip-postgres.png)

Se estiver a utilizar a Cloud Shell em vez da ferramenta psql local, selecione **Permitir o acesso aos serviços Azure** e alterar o seu valor para **ON** para permitir o acesso à Cloud Shell.

Em seguida, ligue-se à instância executando o comando psql abaixo com parâmetros de cadeia de ligação do separador de cordas de **ligação** da instância PostgreSQL no portal Azure.
Substitua os aparelhos vazios por parâmetros da lâmina de linha de base de dados e a palavra-passe pela palavra-passe do Cofre de Chaves Azure.

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Execute o seguinte script PL/pgSQL depois de se certificar de que está ligado à base de dados. O script cria as funções armazenadas utilizadas para inserir dados na base de dados.

```shell
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;

CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```

Para obter mais informações sobre como configurar a verificação da SSL e da Autoridade de Certificados (CA) para postgreSQL, consulte a [conectividade Configure SSL na Base de Dados Azure para PostgreSQL](/azure/postgresql/concepts-ssl-connection-security).

Um certificado de raiz está incluído no recipiente. As medidas tomadas para obter o certificado são:

1. Faça o download do ficheiro de certificado da Autoridade de [Certificados.](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)
2. [Descarregue e instale o OpenSSL na sua máquina](/azure/postgresql/concepts-ssl-connection-security).
3. Descodifique o seu ficheiro de certificado:

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Leia mais sobre como configurar a segurança SSL para PostgreSQL aqui Configurar a [Segurança da Ligação SSL](/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Implementar aplicações web azure no Linux

Você pode facilmente construir serviços Linux em cima do Azure App Service, uma vez que o Azure fornece um conjunto de recipientes e imagens pré-construídos para idiomas amplamente utilizados como Python, Ruby, C#, e Java. O Azure também suporta recipientes personalizados, que podem permitir que praticamente todos os idiomas de programação possam funcionar na plataforma Azure App Service.

A aplicação que está a ser implementada é uma simples aplicação Python que funciona na mais recente distribuição ubuntu Linux. Liga-se aos casos Azure Key Vault e PostgreSQL que foram criados nas secções anteriores para gestão de credenciais e armazenamento de dados, respectivamente.

O seguinte ficheiro Docker é fornecido na pasta raiz da aplicação:

```dockerfile
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

O Dockerfile acima é utilizado para construir o recipiente que está `mcr.microsoft.com/samples/basic-linux-app`alojado no Registo de Contentores Azure em .

O código abaixo:

1. Declara as variáveis e nomes para a instância do Serviço de Aplicações.
2. Cria o grupo de recursos para o plano de Serviço de Aplicações.
3. Disponibiliza uma aplicação Web Azure em contentores Linux.
4. Permite o registo do contentor da aplicação web.
5. Define algumas configurações de aplicações nas definições da aplicação do contentor.

   ```powershell-interactive
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }
   ```

Este script cria uma identidade atribuída para a instância do Serviço de Aplicações que pode ser usada com MSI para interagir com o Cofre chave Azure sem segredos de codificação rígidos em código ou configuração.

Vá à instância do Cofre chave Azure no portal para autorizar a identidade atribuída no separador de política de acesso. **Selecione Adicionar nova política**de acesso . Em **'Selecionar principal',** procure o nome da aplicação semelhante ao nome da instância do Serviço de Aplicações criada.
Deve ser visível um diretor de serviço ligado à aplicação. Selecione-o e guarde a página da política de acesso, como mostra a seguinte imagem.

Como a aplicação só precisa de recuperar chaves, selecione a permissão **Obter** nas opções de segredos, permitindo o acesso reduzindo os privilégios concedidos.

![Política de acesso ao cofre chave](./media/secure-web-app/kv-access-policy.png)

*Criar uma política de acesso ao Cofre Chave*

Guarde a política de acesso e, em seguida, guarde a nova alteração no separador Políticas de **Acesso** para atualizar as políticas.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Implementar gateway de aplicação com firewall de aplicação web ativado

Nas aplicações web, não é aconselhável expor serviços diretamente ao mundo exterior na internet.
As regras de equilíbrio de carga e firewall proporcionam mais segurança e controlo sobre o tráfego de entrada e ajudam-no a geri-lo.

Para implementar uma instância de Gateway de Aplicação:

1. Crie o grupo de recursos para alojar o portal de aplicação.
2. Fornecer uma rede virtual para anexar ao portal.
3. Crie uma sub-rede para o portal na rede virtual.
4. Fornecer um endereço IP público.
5. Providenciar a porta de entrada de candidatura.
6. Ativar a firewall de aplicação web no portal.

   ```powershell-interactive
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

O guião anterior:

1. Cria um novo certificado auto-assinado no Azure.
2. Descarrega o certificado auto-assinado como um ficheiro codificado base64.
3. Gera uma senha para o certificado auto-assinado.
4. Exporta o certificado como ficheiro PFX assinado com a palavra-passe.
5. Armazena a senha do certificado no Cofre de Chaves Azure.

Esta secção implementa o portal de aplicação:

```powershell-interactive
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

Depois de concluir a implementação, tem um portal de aplicação com firewall de aplicação web ativado.

A instância de gateway expõe a porta 443 para HTTPS. Esta configuração garante que a nossa aplicação só está acessível na porta 443 através HTTPS.

Bloquear portas não utilizadas e limitar a exposição à superfície de ataque é uma boa prática de segurança.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Adicione grupos de segurança de rede à instância do Serviço de Aplicações

As instâncias do Serviço de Aplicações podem ser integradas com redes virtuais. Esta integração permite-lhes ser configurados com políticas de grupo de segurança de rede que gerem o tráfego de entrada e saída da app.

1. Para ativar esta funcionalidade, na lâmina de instância de instância do serviço Da Aplicação Azure, em **Definições,** **selecione Networking**. No painel direito, sob **integração VNet,** selecione **Clique aqui para configurar**.

   ![Nova integração de rede virtual](./media/secure-web-app/app-vnet-menu.png)

    *Nova integração de rede virtual para o Serviço de Aplicações*

1. Na página seguinte, selecione **Adicionar VNET (pré-visualização)**.

1. No menu seguinte, selecione a rede virtual `hello-vnet`criada na implementação que começa com . Pode criar uma nova sub-rede ou selecionar uma existente.
   Neste caso, crie uma nova sub-rede. Detete a **gama 'Endereço'** para **10.0.3.0/24** e nomeie a **sub-sub-rede**de aplicações da sub-rede .

   ![Configuração de rede virtual do Serviço de Aplicações](./media/secure-web-app/app-vnet-config.png)

    *Configuração de rede virtual para serviço de aplicações*

Agora que permitiu a integração virtual da rede, pode adicionar grupos de segurança de rede à nossa aplicação.

1. Utilize a caixa de pesquisa, procure **grupos**de segurança de rede . Selecione **grupos de segurança da Rede** nos resultados.

    ![Pesquisa de grupos de segurança da rede](./media/secure-web-app/nsg-search-menu.png)

    *Pesquisa de grupos de segurança da rede*

2. No menu seguinte, selecione **Adicionar**. Insira o **nome** do NSG e do **grupo Derecursos** no qual deve ser localizado. Este NSG será aplicado à sub-rede do gateway da aplicação.

    ![Criar um NSG](./media/secure-web-app/nsg-create-new.png)

    *Criar um NSG*

3. Depois de criado o NSG, selecione-o. Na sua lâmina, em **Definições,** selecione regras de **segurança de entrada**. Configure estas definições para permitir que as ligações entrem no gateway da aplicação sobre a porta 443.

   ![Configure o NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Configure o NSG*

4. Nas regras de saída para o gateway NSG, adicione uma regra que permite ligações de saída `AppService`à instância do Serviço de Aplicações, criando uma regra que visa a etiqueta de serviço:

   ![Adicione regras de saída para o NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Adicione regras de saída para o NSG*

    Adicione outra regra de saída para permitir que o portal envie regras de saída para uma rede virtual.

   ![Adicione outra regra de saída](./media/secure-web-app/nsg-outbound-vnet.png)

    *Adicione outra regra de saída*

5. Na lâmina das sub-redes do NSG, selecione **Associate,** selecione a rede virtual criada na implementação e selecione a subnet de gateway denominada **gw-subnet**. O NSG é aplicado à sub-rede.

6. Crie outro NSG como no passo anterior, desta vez para a instância do Serviço de Aplicações. Dá-lhe um nome. Adicione a regra de entrada para a porta 443 como fez para o gateway de aplicação NSG.

   Se tiver uma instância do Serviço de Aplicações implementada numa instância do App Service Environment, o que não é o caso desta aplicação, pode adicionar regras de entrada para permitir as sondas Azure Service Health abrindo portas 454-455 nos grupos de segurança de entrada do seu Serviço de Aplicações NSG. Aqui está a configuração:

   ![Adicione regras para sondas Azure Service Health](./media/secure-web-app/nsg-create-healthprobes.png)

    *Adicionar regras para sondas de saúde de serviço Azure (apenas App Service Environment)*

7. Nas regras de segurança de saída, crie uma nova regra de segurança de saída que permita à instância do Serviço de Aplicações comunicar com a base de dados PostgreSQL. Configure assim:

   ![Regra para permitir ligações de saída PostgreSQL](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Adicione uma regra para permitir ligações postais de saída*

Para limitar a superfície de ataque, modifique as definições da rede do Serviço de Aplicações para permitir apenas o portal de aplicação para aceder à aplicação.
Fá-lo indo ao separador de rede do Serviço de Aplicações, selecionando o separador **de restrições IP** e criando uma regra de licenciamento que permite apenas o IP do portal de aplicações aceder diretamente ao serviço.

Pode recuperar o endereço IP do portal a partir da sua página geral. No **separador IP Address CIDR,** introduza `<GATEWAY_IP_ADDRESS>/32`o endereço IP neste formato: .

![Permitir apenas o portal](./media/secure-web-app/app-allow-gw-only.png)

*Permitir apenas o gateway IP para aceder ao Serviço de Aplicações*

#### <a name="implement-azure-active-directory-oauth"></a>Implementar Diretório Ativo Azure OAuth

Os documentos Azure distribuídos na página da web da amostra são recursos na nossa aplicação que podem necessitar de proteção. Pode utilizar o Azure Ative Directory (Azure AD) para implementar a autenticação para aplicações web, desktop e mobile utilizando diferentes fluxos de autenticação.
A aplicação utiliza **o Login com**a Microsoft , que permite que a aplicação leia perfis de utilizadores que tenham sido adicionados à lista de utilizadores do Azure AD de um único inquilino.

No portal Azure, configure a app para utilizar as credenciais necessárias:

1. Selecione **Azure Ative Directory**, ou procure-o utilizando a caixa de pesquisa.

2. Selecione **Novo registo:**

   ![Criar uma inscrição](./media/secure-web-app/ad-auth-create.png)

   *Criar uma inscrição de aplicação Azure AD*

3. Na página seguinte, introduza o nome da aplicação. Nos tipos de **conta suportada,** selecione **Contas apenas neste diretório organizacional**.
    Em **Redirect URI,** introduza o domínio base em que a aplicação estará em execução mais uma com o ponto final do símbolo. Por exemplo: *GATEWAY_HASH*.cloudapp.net/token.

   ![Configure Registo de aplicativos DaD Azure](./media/secure-web-app/ad-auth-type.png)

   *Configure Registo de aplicativos DaD Azure*

4. É-lhe apresentado um ecrã que mostra a aplicação registada e as suas informações. Tens de adicionar esta informação à instância do Cofre chave azure.
   1. Copie o ID da aplicação (cliente) e guarde-o no Cofre chave como `CLIENTID`.
   2. Copie o URI redirecionado que introduziu `REDIRECTURI`no passo anterior e guarde-o como .
   3. Copie o nome de diretório padrão azure AD, que tem o `TENANT` *nome*de formato .microsoftonline.com, e guarde-o no Cofre chave como .
   4. Vá ao separador de **segredos de Certificados &** da aplicação Azure AD que criou anteriormente e selecione novo segredo de **cliente**, como mostra a seguinte imagem. Estabeleça uma data de validade e, em seguida, copie `CLIENTSECRET`o valor gerado e guarde-o no Cofre chave como .

      ![Segredo de autorização da AD Azure](./media/secure-web-app/ad-auth-secrets.png)

      *Segredo de autorização da AD Azure*

   5. Gere uma chave secreta segura e aleatória utilizando qualquer ferramenta de linha de comando/online. Guarde-o para `FLASKSECRETKEY`o Cofre chave como. O quadro de aplicação utiliza esta chave para criar sessões.
        Para aprender a gerar uma chave secreta, consulte [o Flask Sessions](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. Depois de configurar o início de sessão, tem de adicionar utilizadores ao link Azure AD para permitir que eles inscrevam o recurso. Para adicioná-las, vá ao separador **Utilizadores** em AD Azure, selecione **Todos os utilizadores**, e, em seguida, selecione Novo **utilizador** ou novo **utilizador convidado**. Para testes, pode adicionar um utilizador convidado e convidar o utilizador para o diretório. Ou pode adicionar um novo utilizador se o domínio em que a aplicação está em execução tiver sido validado. Neste exemplo, apenas os utilizadores registados no inquilino da AD Azure podem ser registados para acesso. Para obter informações sobre acesso a inscrição multiarrendatária, consulte a documentação.

   ![Adicione utilizadores ao domínio padrão](./media/secure-web-app/ad-auth-add-user.png)

   *Adicione utilizadores ao domínio de Diretório Ativo Do Azure padrão*

Depois de adicionar a configuração e os segredos do Azure AD ao Key Vault, os utilizadores podem ser autenticados na aplicação utilizando a autenticação Azure OAuth.
No código da aplicação, este é tratado pela Biblioteca de Autenticação do Diretório Ativo Azure (ADAL).

Depois de os segredos estarem no Key Vault e a aplicação ter acesso aos segredos ehttps://GATEWAY_HASH.cloudapp.net)à base de dados, o serviço de aplicação pode ser alcançado através do URL de aplicação do gateway ( que pode obter a partir da sua lâmina.

Se, ao iniciar sessão no Azure AD, tiver um erro que diz "O utilizador não está registado no diretório em que está a tentar iniciar sessão", tem de adicionar o utilizador. Para adicionar o utilizador, vá ao separador **Utilizadores** do Azure AD e adicione o utilizador manualmente, introduzindo os seus dados ou convidando o utilizador introduzindo o seu endereço de e-mail como utilizador convidado do Azure AD na lâmina **convidar** convidado.

#### <a name="deploy-application-insights"></a>Implementar o Application Insights
Agora que a aplicação está implantada e a funcionar, é necessário lidar com erros que ocorram dentro da app juntamente com o registo e a recolha de dados.
A recolha de registos e rastreios de dados fornece uma visão de eventos de auditoria que ocorrem na app.

Application Insights é um serviço que recolhe registos que podem ser gerados pelos utilizadores ou pelo sistema.

Para criar uma instância de Insights de Aplicação:

1. Procure insights de **aplicação** utilizando a caixa de pesquisa no portal Azure.
2. Selecione **Application Insights**. Forneça os detalhes aqui mostrados para criar uma instância.

   ![Criar uma instância de Insights de Aplicação](./media/secure-web-app/app-insights-data.png)

Após a implementação estar concluída, tem uma instância de Insights de Aplicação.

Depois de criar a instância Deinsights de Aplicações, é necessário sensibilizar a aplicação para a chave de instrumentação que lhe permite enviar registos para a nuvem. Fá-lo recuperando a chave Insights de Aplicação e utilizando-a dentro das bibliotecas de aplicações que o Azure fornece para Insights de Aplicação. A melhor prática é armazenar chaves e segredos no Cofre chave Azure para mantê-los seguros.

Para a aplicação de amostrabásica, depois de criar a instância Deinsights de Aplicações, é necessário sensibilizar a aplicação para a chave de instrumentação que lhe permite enviar registos para a nuvem.
No Cofre chave, `APPINSIGHTSKEY` desempõe um segredo e define o seu valor como chave de instrumentação. Ao fazê-lo, a aplicação permite que a aplicação envie registos e métricas para a Aplicação Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementar autenticação multi-factor para diretório ativo azure

Os administradores têm de garantir que as contas de subscrição no portal estão protegidas. A subscrição é vulnerável a ataques porque gere os recursos que criou. Para proteger a subscrição, ative a autenticação multi-factor no separador De **Diretório Ativo Azure** da subscrição.

A Azure AD opera com base em políticas que são aplicadas a um utilizador ou grupos de utilizadores que se enquadram em determinados critérios.
O Azure cria uma política predefinida especificando que os administradores precisam de autenticação de dois fatores para iniciar sessão no portal.
Depois de ativar esta política, pode ser solicitado a assinar e a assinar de volta para o portal Azure.

Para permitir o MFA para inscrições de administração:

1. Vá ao separador **Azure Ative Diretório** no portal Azure
2. Na categoria de segurança, selecione acesso condicional. Verá este ecrã:

   ![Acesso Condicional - Políticas](./media/secure-web-app/ad-mfa-conditional-add.png)

Se não conseguir criar uma nova política:

1. Vá ao separador **MFA.**
2. Selecione o link de **teste Azure** AD Premium Free para subscrever o teste gratuito.

   ![Teste gratuito do Azure AD Premium](./media/secure-web-app/ad-trial-premium.png)

Volte ao ecrã de acesso condicional.

1. Selecione o novo separador de política.
2. Introduza o nome da política.
3. Selecione os utilizadores ou grupos para os quais pretende ativar o MFA.
4. Sob **os controlos de Acesso,** selecione o separador **Grant** e, em seguida, selecione **Exigir autenticação de vários fatores** (e outras definições se desejar).

   ![Requerer MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

Pode ativar a apólice selecionando a caixa de verificação na parte superior do ecrã ou fazê-lo no separador **Acesso Condicional.** Quando a política está ativada, os utilizadores precisam de MFA para iniciar sessão no portal.

Há uma política de base que requer MFA para todos os administradores do Azure. Pode permitir imediatamente no portal. Permitir esta política pode invalidar a sessão atual e forçá-lo a assinar novamente.

Se a política de base não estiver ativada:

1. Selecione **Exigir MFA para administradores**.
2. Selecione **a política de utilização imediatamente**.

   ![Selecione a política de utilização imediatamente](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Use o Azure Sentinel para monitorizar apps e recursos

À medida que uma aplicação cresce, torna-se difícil agregar todos os sinais e métricas de segurança recebidos dos recursos e torná-los úteis de forma orientada para a ação.

O Azure Sentinel foi concebido para recolher dados, detetar os tipos de ameaças possíveis e fornecer visibilidade em incidentes de segurança.
Enquanto aguarda a intervenção manual, o Azure Sentinel pode contar com playbooks pré-escritos para dar início a alertas e processos de gestão de incidentes.

A aplicação de amostras é composta por vários recursos que o Azure Sentinel pode monitorizar.
Para configurar o Azure Sentinel, é necessário criar primeiro um espaço de trabalho log Analytics que armazene todos os dados recolhidos a partir dos vários recursos.

Para criar este espaço de trabalho:

1. Na caixa de pesquisa no portal Azure, procure o **Log Analytics.** Selecione espaços de **trabalho Log Analytics**.

   ![Pesquisa de espaços de trabalho de Log Analytics](./media/secure-web-app/sentinel-log-analytics.png)

    *Pesquisa de espaços de trabalho de Log Analytics*

2. Na página seguinte, selecione **Adicionar** e, em seguida, fornecer um nome, grupo de recursos e localização para o espaço de trabalho.
   ![Criar uma área de trabalho do Log Analytics](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Criar uma área de trabalho do Log Analytics*

3. Utilize a caixa de pesquisa para procurar **o Azure Sentinel**.

   ![Procurar o Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *Procurar o Azure Sentinel*

4. Selecione **Adicionar** e, em seguida, selecione o espaço de trabalho log Analytics que criou anteriormente.

   ![Adicione um espaço de trabalho log Analytics](./media/secure-web-app/sentinel-workspace-add.png)

    *Adicione um espaço de trabalho log Analytics*

5. Na página **Azure Sentinel - Conectores** de dados, sob **Configuração,** selecione **conectores**de dados . Você vê uma variedade de serviços Azure que você pode ligar à instância de armazenamento Log Analytics para análise em Azure Sentinel.

   ![Conectores de dados de Log Analytics](./media/secure-web-app/sentinel-connectors.png)

    *Adicione um conector de dados ao Azure Sentinel*

   Por exemplo, para ligar o gateway da aplicação, tome estas medidas:

   1. Abra a lâmina de instância de gateway de aplicação Azure.
   2. Em **Monitorização**, selecione **Definições de diagnóstico**.
   3. **Selecione Adicionar definição de diagnóstico**.

      ![Adicionar diagnósticos de Gateway de aplicação](./media/secure-web-app/sentinel-gateway-connector.png)

      *Adicionar diagnósticos de Gateway de aplicação*

   4. Na página de definições de **Diagnóstico,** selecione o espaço de trabalho do Log Analytics que criou e, em seguida, selecione todas as métricas que pretende recolher e enviar para o Azure Sentinel. Selecione **Guardar**.

        ![Definições do conector Azure Sentinel](./media/secure-web-app/sentinel-connector-settings.png)

        *Definições do conector Azure Sentinel*

  As métricas do recurso estão em Azure Sentinel, onde você pode questioná-las e investigá-las.

   Adicione as mesmas métricas em configurações de diagnóstico para o Cofre de Chaves Azure, o endereço IP público, base de dados Azure para PostgreSQL e quaisquer serviços que suportem registos de diagnóstico na sua conta.

Depois de configurar as métricas, o Azure Sentinel tem dados para analisar.

## <a name="evaluate-and-verify"></a>Avaliar e verificar

Depois de desenvolver e implementar a arquitetura, tem de garantir que o código e os serviços implantados cumprem os padrões de segurança. Estes são alguns passos que pode tomar para verificar o software:

- Análise de código estático
- Digitalização de vulnerabilidades
- Encontrar e corrigir vulnerabilidades nas dependências de aplicações

Estes são os blocos básicos de construção para as melhores práticas em desenvolvimento seguro.

### <a name="static-code-analysis"></a>Análise de código estático

Para a aplicação de amostra, a verificação com ferramentas de análise estática envolve encontrar vulnerabilidades no código da aplicação utilizando técnicas como a verificação de manchas e análise de fluxo de dados. As ferramentas de análise estática python dão-lhe mais confiança de que a sua aplicação é segura.

**Linting**

PyFlakes, uma biblioteca de linting Python, ajuda-o a remover código morto e funções não utilizadas de apps, como mostrado aqui:

![Flocos-pigs](./media/secure-web-app/pyflakes.png)

O linting fornece dicas e possíveis alterações que podem tornar o seu código mais limpo e menos propenso a erros durante o tempo de execução.

**PyLint**

PyLint forneceu o maior valor para este projeto. Executa verificações padrão de código, verificação de erros e dicas de refactoring para garantir que o código em execução no servidor é seguro. Ao utilizar o PyLint para atualizar o seu código, pode eliminar bugs e melhorar a classificação pyLint, como mostram as seguintes imagens.

![Antes de PyLint](./media/secure-web-app/before-pylint.png)

*Antes de PyLint*

Depois de corrigir alguns dos erros de código encontrados pelas ferramentas de fiação, tem mais confiança de que o código não é propenso a erros. A correção dos erros reduz significativamente os riscos de segurança que podem ocorrer quando o código é implantado em ambientes de produção.

![Depois de Pylint](./media/secure-web-app/after-pylint.png)

*Depois de PyLint*

### <a name="vulnerability-scanning"></a>Digitalização de vulnerabilidades

A ferramenta [ZAP da OWASP](https://www.zaproxy.org/) é um scanner de vulnerabilidade de aplicação web de código aberto que pode usar para verificar se a aplicação de amostras está a verificar se há vulnerabilidades. Executar a ferramenta na aplicação de amostras revela alguns possíveis erros e vetores de ataque.

![Ferramenta ZAP](./media/secure-web-app/zap-tool.png)

*Ferramenta ZAP*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Encontrar e corrigir vulnerabilidades em dependências de apps

Para encontrar e corrigir dependências de aplicações, pode utilizar a [Verificação de Dependência da OWASP](https://www.owasp.org/index.php/OWASP_Dependency_Check).

A segurança é uma aplicação semelhante que verifica as dependências. Pode encontrá-lo no [GitHub.](https://github.com/pyupio/safety) Verificações de segurança para vulnerabilidades encontradas em bases de dados de vulnerabilidades bem conhecidas.

![Segurança](./media/secure-web-app/pysafety.png)

*Segurança*

## <a name="next-steps"></a>Passos seguintes

Os seguintes artigos podem ajudá-lo a projetar, desenvolver e implementar aplicações seguras.

- [Design](secure-design.md)
- [Desenvolver](secure-develop.md)
- [Implementar](secure-deploy.md)
