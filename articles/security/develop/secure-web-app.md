---
title: Desenvolver uma aplicação web segura Microsoft Docs
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
ms.custom: has-adal-ref, tracking-python
ms.openlocfilehash: 857303009b31945b0fe4f5555cb7e545cd16719d
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558882"
---
# <a name="develop-a-secure-web-app"></a>Desenvolver uma aplicação Web segura

Esta amostra é uma simples aplicação Python que exibe uma página web contendo links para recursos de segurança para o desenvolvimento de aplicações no Azure. A aplicação implementa as melhores práticas de segurança que podem ajudar a melhorar a sua aplicação e a postura de segurança da sua organização quando desenvolve aplicações no Azure.

Deve seguir os passos descritos neste artigo sequencialmente para garantir que os componentes da aplicação estão configurados corretamente. A base de dados, o Azure App Service, o Azure Key Vault e o exemplo do Gateway de Aplicações Azure dependem uns dos outros.

Os scripts de implantação configuram a infraestrutura. Depois de executar os scripts de implementação, terá de fazer alguma configuração manual no portal Azure para ligar os componentes e serviços em conjunto.

A aplicação da amostra destina-se a principiantes que desenvolvem aplicações no Azure que pretendem implementar medidas de segurança nas suas aplicações.

Ao desenvolver e implementar esta aplicação, aprenderá a:

- Crie uma instância Azure Key Vault, guarde e recupere segredos dele.
- Implementar a Base de Dados Azure para PostgreSQL, configurar senhas seguras e autorizar o acesso à base de dados.
- Executar um contentor Alpine Linux em Azure Web Apps para Linux e permitir identidades geridas para recursos Azure.
- Crie e configura uma instância Azure Application Gateway com uma firewall que utiliza [o Top 10 Ruleset do OWASP](https://coreruleset.org/).
- Ativar a encriptação de dados em trânsito e em repouso utilizando os serviços Azure.

Depois de desenvolver e implementar esta aplicação, terá configurado a seguinte aplicação web de amostra, juntamente com as medidas de configuração e segurança que são descritas.

![Aplicação web de amostra](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Arquitetura

A aplicação é uma aplicação típica de n-tier com três níveis. A parte frontal, a parte traseira e a camada de base de dados com componentes de monitorização e gestão secreta integrados são mostradas aqui:

![Arquitetura de aplicações](./media/secure-web-app/architecture.png)

A arquitetura é constituída por estes componentes:

- [Gateway de aplicação Azure](../../application-gateway/index.yml). Fornece a porta de entrada e firewall para a nossa arquitetura de aplicações.
- [Azure Web Apps em Linux](../../app-service/containers/app-service-linux-intro.md). Fornece o tempo de funcionação do recipiente para executar a aplicação Python em um ambiente Linux.
- [Cofre da Chave Azure.](../../key-vault/index.yml) Armazena e encripta os segredos da nossa app e gere a criação de políticas de acesso à sua volta.
- [Base de Dados Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql/). Armazena de forma segura os dados da nossa aplicação.
- [Centro de Segurança Azure](../../security-center/index.yml) e [Insights de Aplicação Azure](../../azure-monitor/app/app-insights-overview.md). Fornece monitorização e alertas sobre o funcionamento da nossa app.

## <a name="threat-model"></a>Modelo de ameaça

A modelação de ameaças é o processo de identificação de potenciais ameaças à segurança para o seu negócio e aplicação e, em seguida, garantir que está em vigor um plano de mitigação adequado.

Esta amostra utilizou a [Ferramenta de Modelação de Ameaças](threat-modeling-tool.md) da Microsoft para implementar modelação de ameaças para a aplicação de amostras seguras. Ao diagramar os componentes e os fluxos de dados, pode identificar problemas e ameaças no início do processo de desenvolvimento. Isto poupa tempo e dinheiro mais tarde.

Este é o modelo de ameaça para a aplicação da amostra:

![Modelo de ameaça](./media/secure-web-app/threat-model.png)

Algumas ameaças de amostra e potenciais vulnerabilidades que a ferramenta de modelação de ameaças gera são mostradas na imagem seguinte. O modelo de ameaça dá uma visão geral da superfície de ataque exposta e leva os desenvolvedores a pensar em como mitigar os problemas.

![Saída de modelo de ameaça](./media/secure-web-app/threat-model-output.png)

Por exemplo, a injeção de SQL na saída do modelo de ameaça anterior é atenuada pela higienização das entradas do utilizador e pela utilização de funções armazenadas na Base de Dados Azure para PostgreSQL. Esta mitigação impede a execução arbitrária de consultas durante a leitura e escrita de dados.

Os desenvolvedores melhoram a segurança geral do sistema, atenuando cada uma das ameaças na produção do modelo de ameaça.

## <a name="deployment"></a>Implementação

As seguintes opções permitem-lhe executar Linux no Azure App Service:

- Escolha um recipiente da lista de recipientes microsoft pré-construídos em Azure que foram criados com tecnologias de suporte (Python, Ruby, PHP, Java, Node.js, .NET Core).
- Use um recipiente personalizado. Selecione os seus próprios registos de contentores como a fonte da imagem e baseie-se nas muitas tecnologias disponíveis que suportam HTTP.

Neste exemplo, irá executar o script de implementação que irá implementar o webapp no Serviço de Aplicações e criar os recursos.

A aplicação pode utilizar os diferentes modelos de implementação apresentados abaixo:

![Diagrama de fluxo de dados de implantação](./media/secure-web-app/deployment.png)

Existem muitas formas de implementar aplicações no Azure, incluindo:

- Modelos do Azure Resource Manager
- PowerShell
- CLI do Azure
- Portal do Azure
- DevOps do Azure

Esta aplicação utilizou:

- [Docker](https://docs.docker.com/) para criar e construir as imagens do contentor.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para implantação.
- [Docker Hub](https://hub.docker.com/) como registo de contentores.

## <a name="security-considerations"></a>Considerações de segurança

### <a name="network"></a>Rede

A aplicação da amostra utiliza encriptação TLS/SSL de ponta a ponta para dados em trânsito que fluem para dentro e para fora da rede. O portal está configurado com um certificado auto-assinado.
> [!IMPORTANT]
> Um certificado auto-assinado é usado nesta demonstração. Em ambiente de produção, deverá obter certificados de uma Autoridade de Certificados Verificada (CA).

A firewall da aplicação também inspeciona o tráfego de entrada e alerta os administradores quando o tráfego malicioso é detetado no tráfego da rede.
O Application Gateway atenua a possibilidade de ameaças de injeção de DDoS e SQL descobertas no modelo de ameaça.

### <a name="identity"></a>Identidade

Para iniciar sessão no portal, a aplicação da amostra utiliza administradores de autenticação multi-factor para Diretório Ativo Azure (Azure AD) a quem foi atribuído acesso aos recursos.
A aplicação da amostra utiliza identidades geridas para obter permissões para ler e recuperar segredos do Azure Key Vault, garantindo que a aplicação não precisa de credenciais de código rígido e fichas para ler os segredos. O Azure AD cria automaticamente os principais serviços que a app precisa para ler e modificar os segredos quando são utilizadas identidades geridas.

Identidades geridas para os recursos Azure e MFA dificultam que os adversários ganhem privilégios e aumentem os seus privilégios no sistema. Esta ameaça foi apontada no modelo de ameaça.
A aplicação utiliza o OAuth, que permite aos utilizadores registados na aplicação OAuth iniciar sômposição na aplicação.

### <a name="storage"></a>Armazenamento

Os dados na base de dados PostgreSQL são encriptados em repouso automaticamente pela Base de Dados Azure para PostgreSQL. A base de dados autoriza os endereços IP do Serviço de Aplicações para que apenas a aplicação web implementada do Serviço de Aplicações possa aceder aos recursos da base de dados com as credenciais de autenticação certas.

### <a name="logging-and-auditing"></a>Registo e auditoria

A aplicação implementa o registo através do recurso Application Insights para rastrear métricas, registos e exceções que ocorrem. Esta sessão fornece metadados de aplicações suficientes para informar os desenvolvedores e membros da equipa de operações sobre o estado da aplicação. Também fornece dados suficientes para retroceder em caso de incidentes de segurança.

## <a name="cost-considerations"></a>Considerações de custos

Se ainda não tiver uma conta Azure, pode criar uma conta gratuita. Vá à [página de conta gratuita](https://azure.microsoft.com/free/) para começar, veja o que pode fazer com uma conta Azure gratuita e saiba quais os produtos gratuitos durante 12 meses.

Para implementar os recursos na aplicação da amostra com as funcionalidades de segurança, precisa de pagar por algumas funcionalidades premium. À medida que as escalas de aplicações e os níveis e testes gratuitos oferecidos pela Azure precisam de ser atualizados para satisfazer os requisitos da aplicação, os seus custos podem aumentar. Use a [calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os seus custos.

## <a name="deploy-the-solution"></a>Implementar a solução

### <a name="prerequisites"></a>Pré-requisitos

Para pôr a aplicação a funcionar, é necessário instalar estas ferramentas:

- Um editor de código para modificar e ver o código de aplicação. [Visual Studio Code](https://code.visualstudio.com/) é uma opção de código aberto.
- [Azure CLI](/cli/azure/install-azure-cli) no seu computador de desenvolvimento.
- [Git](https://git-scm.com/) no seu sistema. Git é usado para clonar o código de origem localmente.
- [jq](https://stedolan.github.io/jq/), uma ferramenta UNIX para consulta de JSON de forma amigável.

Precisa de uma subscrição do Azure para implementar os recursos da aplicação da amostra. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/) para testar a aplicação da amostra.

Depois de instalar estas ferramentas, está pronto para implementar a aplicação no Azure.

### <a name="environment-setup"></a>Configuração de ambiente

Executar os scripts de implementação para configurar o ambiente e a subscrição:

1. Para clonar o repositório de código fonte, use este comando Git:

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. Para entrar no diretório, use este comando:

   ```shell
   cd tutorial-project/scripts
   ```

3. Existem ficheiros na pasta scripts que são específicos da plataforma que está a utilizar (Windows ou Linux). Como o CLI Azure já foi instalado, inscreva-se na conta Azure no comando, executando este comando Azure CLI:

   ```azurecli-interactive
   az login
   ```

O navegador abrirá, iniciará s.000 com as suas credenciais. Depois de iniciar a sessão, pode começar a implantar os recursos a partir do pedido de comando.

Os scripts de implementação `deploy-powershell.ps1` e contêm código que implementa toda a `deploy-bash.sh` aplicação.
Para implementar a solução:

1. Se estiver no PowerShell, execute o `deploy-powershell.ps1` ficheiro `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` digitando substituindo a região e o nome do grupo de recursos por regiões Azure adequadas e um nome para o grupo de recursos
2. Se estiver no Linux, execute o `deploy-bash.sh` ficheiro `/deploy-bash.sh REGION RESOURCE_GROUP_NAME` digitando, poderá ter de tornar o ficheiro executável digitando`chmod +x deploy-bash.sh`

Os exemplos a seguir mostram os fragmentos dos componentes-chave. Pode implementar os exemplos individualmente ou com o resto dos componentes executando os ficheiros de implantação.

### <a name="implementation-guidance"></a>Orientação de implementação

O script de implementação é um script que pode ser dividido em quatro fases. Cada fase implementa e configura um recurso Azure que está no [diagrama](#architecture)de arquitetura.

As quatro fases são:

- Desdobre o cofre da chave Azure.
- Implementar base de dados de Azure para PostgreSQL.
- Implementar aplicações web Azure no Linux.
- Implementar Gateway de aplicação com firewall de aplicação web.

Cada fase baseia-se na anterior utilizando a configuração dos recursos previamente utilizados.

Para completar as etapas de implementação, certifique-se de que instalou as ferramentas listadas nos [Pré-Requisitos](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Implementar cofre de chave Azure

Nesta secção, você cria e implementa uma instância Azure Key Vault que é usada para armazenar segredos e certificados.

Depois de completar a colocação, tem uma instância Azure Key Vault implantada no Azure.

Para implantar o Cofre da Chave Azure utilizando o Azure CLI:

1. Declare as variáveis para Azure Key Vault.
2. Registe o fornecedor Azure Key Vault.
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

É uma boa prática usar identidades geridas para recursos Azure em apps que usam o Key Vault para aceder a recursos. A sua postura de segurança aumenta quando as teclas de acesso ao Key Vault não são armazenadas em código ou na configuração.

#### <a name="deploy-azure-database-for-postgresql"></a>Implementar base de dados de Azure para PostgreSQL

A Azure Database for PostgreSQL funciona da seguinte forma, primeiro criar o servidor de base de dados e depois criar a base de dados para armazenar o esquema e os dados.

Depois de completar a implementação, tem um servidor PostgreSQL e uma base de dados em execução no Azure.

Para implementar a Base de Dados Azure para PostgreSQL utilizando o Azure CLI:

1. Abra um terminal com O Azure CLI e a sua configuração de subscrição Azure.
2. Gere um nome de utilizador seguro e uma combinação de palavra-passe que é usada para aceder à base de dados. (Estes devem ser armazenados no Cofre da Chave Azure para aplicações que as utilizem.)
3. Crie a instância do servidor PostgreSQL.
4. Crie uma base de dados sobre a instância do servidor que criou no passo 3.
5. Executar scripts PostgreSQL na instância PostgreSQL.

O código abaixo baseia-se nos segredos PGUSERNAME e PGPASSWORD armazenados em Azure KeyVault a partir do passo keyVault de implementação.

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

Depois de implementar a base de dados, tem de armazenar as suas credenciais e cadeias de ligação no Cofre da Chave Azure.
Na pasta scripts, há um `functions.sql` ficheiro que contém o código PL/pgSQL que cria funções armazenadas quando o executa. A execução deste ficheiro parametriza as entradas para limitar a injeção de SQL.

PostgreSQL é agregado com uma ferramenta chamada `psql` que é usada para ligar à base de dados. Para `functions.sql` executar, é necessário ligar à Base de Dados Azure para a instância PostgreSQL da sua máquina local e executá-la a partir daí. A instalação da ferramenta psql está incluída na instalação predefinido para PostgreSQL em cada sistema operativo.
Para mais informações, consulte a [Documentação do PSQL.](https://www.postgresql.org/docs/9.3/app-psql.html)

A Azure Cloud Shell também inclui a `psql` ferramenta. Pode utilizar o Cloud Shell diretamente a partir do portal Azure selecionando o Ícone cloud Shell.

Para permitir o acesso remoto à instância PostgreSQL, é necessário autorizar o endereço IP em PostgreSQL.
Ativa este acesso indo ao separador **de segurança 'Ligação',** selecionando **O IP do cliente**e guardando as novas definições.

![Autoriza o COMPUTADOR do cliente](./media/secure-web-app/add-client-ip-postgres.png)

Se estiver a utilizar a Cloud Shell em vez da ferramenta psql local, selecione **Deixe o acesso aos serviços Azure** e altere o seu valor para **ON** para permitir o acesso à Cloud Shell.

Em seguida, ligue-se ao caso executando o comando psql abaixo com parâmetros de cadeia de ligação a partir do separador de **cordas de ligação** da instância PostgreSQL no portal Azure.
Substitua os aparelhos vazios por parâmetros da lâmina de ligação da base de dados e a palavra-passe com a palavra-passe do Cofre de Chaves Azure.

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

Para obter mais informações sobre como configurar a verificação da TLS and Certificate Authority (CA) para postgreSQL, consulte [a conectividade Configure TLS na Base de Dados Azure para PostgreSQL](/azure/postgresql/concepts-ssl-connection-security).

Um certificado de raiz está incluído no recipiente. As medidas tomadas para obter o certificado são:

1. Faça o download do ficheiro de certificado da [Autoridade de Certificados.](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)
2. [Faça o download e instale o OpenSSL na sua máquina](/azure/postgresql/concepts-ssl-connection-security).
3. Descodificar o seu ficheiro de certificado:

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Leia mais sobre como configurar a segurança TLS para PostgreSQL aqui [Configure A Garantia de Conexão TLS](/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Implementar aplicações web Azure no Linux

Você pode facilmente construir serviços Linux em cima do Azure App Service, uma vez que a Azure fornece um conjunto de recipientes e imagens pré-construídos para idiomas amplamente utilizados como Python, Ruby, C#e Java. O Azure também suporta recipientes personalizados, o que pode permitir que praticamente todas as linguagens de programação sejam executadas na plataforma Azure App Service.

A aplicação que está a ser implementada é uma simples aplicação Python que funciona na mais recente distribuição do Ubuntu Linux. Liga-se aos casos Azure Key Vault e PostgreSQL que foram criados nas secções anteriores para gestão de credenciais e armazenamento de dados, respectivamente.

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

O Dockerfile acima é utilizado para construir o contentor que está alojado no Registo do Contentor Azure em `mcr.microsoft.com/samples/basic-linux-app` .

O código abaixo:

1. Declara as variáveis e nomes para a instância do Serviço de Aplicações.
2. Cria o grupo de recursos para o plano de Serviço de Aplicações.
3. Disposi uma aplicação web Azure na instância de contentores Linux.
4. Permite o registo do recipiente de aplicações web.
5. Define algumas configurações de aplicações nas definições de aplicações do recipiente.

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

Este script cria uma identidade atribuída para a instância do Serviço de Aplicações que pode ser usada com MSI para interagir com o Azure Key Vault sem segredos de codificação rígido em código ou configuração.

Aceda à instância Azure Key Vault no portal para autorizar a identidade atribuída no separador política de acesso. **Selecione Adicione nova política de acesso**. Em **Select principal,** procure o nome da aplicação que seja semelhante ao nome da instância do Serviço de Aplicações criada.
Deve ser visível um princípio de serviço anexado à aplicação. Selecione-o e guarde a página da política de acesso, como mostrado na imagem seguinte.

Como a aplicação apenas precisa de recuperar chaves, selecione a permissão **obter** nas opções secretas, permitindo o acesso enquanto reduz os privilégios concedidos.

![Política de acesso a cofre chave](./media/secure-web-app/kv-access-policy.png)

*Criar uma política de acesso ao Cofre de Chaves*

Guarde a política de acesso e, em seguida, guarde a nova alteração no separador **Políticas de Acesso** para atualizar as políticas.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Implementar Gateway de aplicação com firewall de aplicação web ativado

Nas aplicações web, não é recomendável que exponha serviços diretamente ao mundo exterior na internet.
As regras de equilíbrio de carga e firewall proporcionam mais segurança e controlo sobre o tráfego de entrada e ajudam-no a geri-lo.

Para implementar uma instância de Gateway de aplicação:

1. Crie o grupo de recursos para alojar o gateway de aplicação.
2. Provisionar uma rede virtual para anexar ao portal.
3. Crie uma sub-rede para o gateway na rede virtual.
4. Disponibilização de um endereço IP público.
5. Provisione o portal de aplicação.
6. Ative a firewall da aplicação web no gateway.

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
2. Descarrega o certificado auto-assinado como um ficheiro codificado de base64.
3. Gera uma senha para o certificado auto-assinado.
4. Exporta o certificado como um ficheiro PFX assinado com a senha.
5. Armazena a senha do certificado no Cofre da Chave Azure.

Esta secção implementa o gateway de aplicação:

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

Depois de concluir a implementação, tem um gateway de aplicação com firewall de aplicação web ativado.

A porta de entrada expõe a porta 443 para HTTPS. Esta configuração garante que a nossa aplicação só está acessível na porta 443 através do HTTPS.

Bloquear portas não uusadas e limitar a exposição à superfície de ataque é uma boa prática de segurança.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Adicione grupos de segurança de rede à instância do Serviço de Aplicações

As instâncias do Serviço de Aplicações podem ser integradas com redes virtuais. Esta integração permite-lhes ser configurados com políticas de grupos de segurança de rede que gerem o tráfego de entrada e saída da aplicação.

1. Para ativar esta função, na lâmina de instância de serviço da App Azure, em **Definições**, selecione **Networking**. No painel direito, em **Integração VNet,** selecione **Clique aqui para configurar**.

   ![Nova integração de rede virtual](./media/secure-web-app/app-vnet-menu.png)

    *Nova integração de rede virtual para o Serviço de Aplicações*

1. Na página seguinte, **selecione Add VNET (pré-visualização)**.

1. No menu seguinte, selecione a rede virtual criada na implementação que começa por `hello-vnet` . Pode criar uma nova sub-rede ou selecionar uma existente.
   Neste caso, crie uma nova sub-rede. Deslote o **intervalo address** para **10.0.3.0/24** e nomeie a **sub-rede de aplicações da sub-rede**.

   ![Configuração de rede virtual do Serviço de Aplicações](./media/secure-web-app/app-vnet-config.png)

    *Configuração de rede virtual para Serviço de Aplicações*

Agora que ativou a integração da rede virtual, pode adicionar grupos de segurança de rede à nossa aplicação.

1. Utilize a caixa de pesquisa, procure **grupos de segurança de rede.** Selecione **grupos de segurança de rede** nos resultados.

    ![Pesquisa de grupos de segurança de rede](./media/secure-web-app/nsg-search-menu.png)

    *Pesquisa de grupos de segurança de rede*

2. No menu seguinte, **selecione Adicionar**. Insira o **nome** do NSG e do **grupo de recursos** em que deve ser localizado. Este NSG será aplicado na sub-rede do gateway de aplicações.

    ![Criar um NSG](./media/secure-web-app/nsg-create-new.png)

    *Criar um NSG*

3. Depois de o NSG ser criado, selecione-o. Na sua lâmina, em **Definições,** selecione **as regras de Segurança de Entrada**. Configure estas definições para permitir que as ligações entrem no gateway de aplicação sobre a porta 443.

   ![Configure o NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Configure o NSG*

4. Nas regras de saída para o gateway NSG, adicione uma regra que permite ligações de saída à instância do Serviço de Aplicações, criando uma regra que visa a etiqueta de `AppService` serviço:

   ![Adicionar regras de saída para o NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Adicionar regras de saída para o NSG*

    Adicione outra regra de saída para permitir que o portal envie regras de saída para uma rede virtual.

   ![Adicione outra regra de saída](./media/secure-web-app/nsg-outbound-vnet.png)

    *Adicione outra regra de saída*

5. Na lâmina das sub-redes do NSG, selecione **Associate**, selecione a rede virtual criada na implementação e selecione a sub-rede de gateway denominada **gw-subnet**. O NSG é aplicado na sub-rede.

6. Crie outro NSG como no passo anterior, desta vez para a instância do Serviço de Aplicações. Dê-lhe um nome. Adicione a regra de entrada para a porta 443, como fez para o gateway de aplicação NSG.

   Se tiver uma instância do Serviço de Aplicações implantada numa instância do App Service Environment, o que não é o caso desta aplicação, pode adicionar regras de entrada para permitir que as sondas Azure Service Health abrissem as portas 454-455 nos grupos de segurança de entrada do seu Serviço de Aplicações NSG. Aqui está a configuração:

   ![Adicionar regras para sondas de saúde do Serviço Azure](./media/secure-web-app/nsg-create-healthprobes.png)

    *Adicionar regras para sondas de saúde do serviço Azure (apenas Ambiente de Serviço de Aplicações)*

7. Nas regras de segurança de saída, crie uma nova regra de segurança de saída que permita à instância do Serviço de Aplicações comunicar com a base de dados PostgreSQL. Configuure-o assim:

   ![Regra para permitir ligações postgresQL de saída](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Adicione uma regra para permitir ligações postgreSQL de saída*

Para limitar a superfície de ataque, modifique as definições da rede de Serviço de Aplicações para permitir apenas o gateway da aplicação aceder à aplicação.
Fá-lo entrando no separador de rede do Serviço de Aplicações, selecionando o separador **restrições IP** e criando uma regra de licenciamento que permite apenas o IP do gateway de aplicação aceder diretamente ao serviço.

Pode recuperar o endereço IP do gateway a partir da sua página geral. No **separador IP Address CIDR,** introduza o endereço IP neste formato: `<GATEWAY_IP_ADDRESS>/32` .

![Permitir apenas o portal](./media/secure-web-app/app-allow-gw-only.png)

*Permitir apenas o gateway IP para aceder ao Serviço de Aplicações*

#### <a name="implement-azure-active-directory-oauth"></a>Implementar OAuth Ative Azure

Os documentos Azure distribuídos na página de aplicações da amostra web são recursos na nossa app que podem necessitar de proteção. Pode utilizar o Azure Ative Directory (Azure AD) para implementar a autenticação para aplicações web, desktop e mobile utilizando diferentes fluxos de autenticação.
A aplicação utiliza o Login com a **Microsoft,** que permite que a aplicação leia perfis de utilizadores que tenham sido adicionados à lista de utilizadores AZure AD de um único inquilino.

No portal Azure, configurar a app para utilizar as credenciais necessárias:

1. Selecione **Azure Ative Directory**, ou procure-o utilizando a caixa de pesquisa.

2. Selecione **Novo registo:**

   ![Criar um registo](./media/secure-web-app/ad-auth-create.png)

   *Criar um registo de aplicativo AD Azure*

3. Na página seguinte, insira o nome da aplicação. Nos **tipos de conta suportada,** selecione Contas **apenas neste diretório organizacional**.
    Em **Redirecionar URI,** insira o domínio base em que a aplicação estará em execução em mais uma com o ponto final simbólico. Por exemplo: *GATEWAY_HASH*.cloudapp.net/token.

   ![Configure Registo de aplicações AD AD](./media/secure-web-app/ad-auth-type.png)

   *Configure Registo de aplicações AD AD*

4. É apresentado com um ecrã que mostra a aplicação registada e as suas informações. Tens de adicionar esta informação à instância do Cofre da Chave Azure.
   1. Copie o ID da aplicação (cliente) e guarde-o no Key Vault como `CLIENTID` .
   2. Copie o URI de redirecionamento que introduziu no passo anterior e guarde-o como `REDIRECTURI` .
   3. Copie o nome de diretório padrão AZure AD, que tem o *nome*de formato .microsoftonline.com, e guarde-o no Key Vault como `TENANT` .
   4. Aceda ao separador **Certificados & segredos** da aplicação AZure AD que criou anteriormente e selecione **Novo segredo de cliente**, como mostra a imagem seguinte. Estabeleça uma data de validade e, em seguida, copie o valor gerado e guarde-o no Cofre de Chaves como `CLIENTSECRET` .

      ![Segredo de autorização da AD AD AZure](./media/secure-web-app/ad-auth-secrets.png)

      *Segredo de autorização da AD AD AZure*

   5. Gere uma chave secreta aleatória segura utilizando qualquer linha de comando/ferramenta on-line. Guarde-o no Cofre de Chaves como `FLASKSECRETKEY` . O quadro de aplicações utiliza esta chave para criar sessões.
        Para aprender a gerar uma chave secreta, consulte [As Sessões de Flask](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. Depois de configurar o súdin, precisa de adicionar utilizadores ao link Azure AD para permitir que eles entrem no recurso. Para adicioná-los, aceda ao separador **Utilizadores** em Azure AD, selecione **Todos os utilizadores**e, em seguida, selecione **Novo utilizador** ou Novo utilizador **de hóspedes**. Para testes, pode adicionar um utilizador convidado e convidar o utilizador para o diretório. Ou pode adicionar um novo utilizador se o domínio em que a aplicação está em execução tiver sido validado. Neste exemplo, apenas os utilizadores registados no inquilino Azure AD podem estar registados para acesso. Para obter informações sobre o acesso a um sinal de acesso multitenante, consulte a documentação.

   ![Adicionar utilizadores ao domínio predefinido](./media/secure-web-app/ad-auth-add-user.png)

   *Adicionar utilizadores ao domínio padrão do Azure Ative Directory*

Depois de adicionar a configuração AD AD do Azure e os segredos ao Key Vault, os utilizadores podem ser autenticados na aplicação utilizando a autenticação Azure OAuth.
No código da aplicação, este é tratado pela Azure Ative Directory Authentication Library (ADAL).

Depois de os segredos estarem em Key Vault e a aplicação ter acesso aos segredos e à base de dados, o serviço de aplicação pode ser alcançado através do URL de aplicação do gateway (https: \/ /GATEWAY_HASH.cloudapp.net), que pode obter a partir da sua lâmina.

Se, ao iniciar sessão no Azure AD, tiver um erro que diga "O utilizador não está registado no diretório em que está a tentar iniciar sessão", tem de adicionar o utilizador. Para adicionar o utilizador, aceda ao separador **Utilizadores** do Azure AD e adicione o utilizador manualmente introduzindo os seus dados ou convidando o utilizador inserindo o seu endereço de e-mail como utilizador convidado para Azure AD na lâmina **convidar** convidado.

#### <a name="deploy-application-insights"></a>Implementar o Application Insights
Agora que a aplicação está implementada e a funcionar, é necessário lidar com erros que ocorram dentro da app, juntamente com o registo e a recolha de dados.
O registo e a recolha de dados de rastreio proporcionam uma visão de eventos de auditoria que ocorrem na aplicação.

Application Insights é um serviço que recolhe registos que podem ser gerados pelos utilizadores ou pelo sistema.

Para criar uma instância de Insights de Aplicação:

1. Procure **por Insights de Aplicação** utilizando a caixa de pesquisa no portal Azure.
2. Selecione **Application Insights**. Forneça os detalhes mostrados aqui para criar um caso.

   ![Criar uma instância de Insights de Aplicação](./media/secure-web-app/app-insights-data.png)

Depois de concluída a implementação, tem uma instância de Insights de Aplicação.

Depois de criar a instância Applications Insights, precisa de sensibilizar a app para a chave de instrumentação que lhe permite enviar registos para a nuvem. Fá-lo recuperando a chave Application Insights e utilizando-a dentro das bibliotecas de aplicações que o Azure fornece para Insights de Aplicação. A melhor prática é guardar chaves e segredos no Cofre da Chave Azure para mantê-los seguros.

Para a aplicação de amostra básica, depois de criar a instância Applications Insights, é necessário sensibilizar a app para a chave de instrumentação que lhe permite enviar registos para a nuvem.
Em Key Vault, estabeleça um `APPINSIGHTSKEY` segredo e desebente o seu valor como chave de instrumentação. Ao fazê-lo, a aplicação envia registos e métricas para o Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementar autenticação multi-factor para diretório ativo Azure

Os administradores devem garantir que as contas de subscrição no portal estão protegidas. A subscrição é vulnerável a ataques porque gere os recursos que criou. Para proteger a subscrição, ative a autenticação multi-factor no separador **Azure Ative Directory** da subscrição.

O Azure AD funciona com base em políticas que são aplicadas a um utilizador ou grupos de utilizadores que se enquadram num determinados critérios.
O Azure cria uma política predefinida especificando que os administradores precisam de autenticação de dois fatores para iniciar súbdista no portal.
Depois de ativar esta política, poderá ser solicitado que assine e volte a entrar no portal Azure.

Para ativar o MFA para os pré-ins de administração:

1. Aceda ao separador **Azure Ative Directory** no portal Azure
2. Na categoria de segurança, selecione o acesso condicional. Verá este ecrã:

   ![Acesso Condicional - Políticas](./media/secure-web-app/ad-mfa-conditional-add.png)

Se não conseguir criar uma nova política:

1. Vá ao separador **MFA.**
2. Selecione o link de teste Azure AD Premium **Grátis** para subscrever o teste gratuito.

   ![Julgamento gratuito Azure AD Premium](./media/secure-web-app/ad-trial-premium.png)

Volte ao ecrã de acesso condicional.

1. Selecione o novo separador de política.
2. Introduza o nome da política.
3. Selecione os utilizadores ou grupos para os quais pretende ativar o MFA.
4. Nos **controlos de Acesso**, selecione o separador **Grant** e, em seguida, selecione **Requera a autenticação de vários fatores** (e outras definições se quiser).

   ![Requerer MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

Pode ativar a política selecionando a caixa de verificação na parte superior do ecrã ou fazê-la no **separador Acesso Condicional.** Quando a política está ativada, os utilizadores precisam de MFA para iniciar serção no portal.

Há uma política de base que requer MFA para todos os administradores do Azure. Pode ative-lo imediatamente no portal. Permitir esta política pode invalidar a sessão atual e forçá-lo a assinar novamente.

Se a política de base não estiver ativada:

1. Selecione **Exigir MFA para administradores**.
2. Selecione **a política de utilização imediatamente**.

   ![Selecione a política de utilização imediatamente](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Use o Azure Sentinel para monitorizar apps e recursos

À medida que uma aplicação cresce, torna-se difícil agregar todos os sinais e métricas de segurança recebidos dos recursos e torná-los úteis de forma orientada para a ação.

O Azure Sentinel foi concebido para recolher dados, detetar os tipos de ameaças possíveis e fornecer visibilidade em incidentes de segurança.
Enquanto aguarda a intervenção manual, o Azure Sentinel pode contar com livros pré-escritos para dar início a alertas e processos de gestão de incidentes.

A aplicação da amostra é composta por vários recursos que o Azure Sentinel pode monitorizar.
Para configurar o Azure Sentinel, primeiro precisa de criar um espaço de trabalho Log Analytics que armar todos os dados recolhidos dos vários recursos.

Para criar este espaço de trabalho:

1. Na caixa de pesquisa no portal Azure, procure **por Log Analytics**. Selecione **log analytics espaços de trabalho**.

   ![Pesquisa de espaços de trabalho Log Analytics](./media/secure-web-app/sentinel-log-analytics.png)

    *Pesquisa de espaços de trabalho Log Analytics*

2. Na página seguinte, **selecione Add** e, em seguida, forneça um nome, grupo de recursos e localização para o espaço de trabalho.
   ![Criar uma área de trabalho do Log Analytics](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Criar uma área de trabalho do Log Analytics*

3. Utilize a caixa de pesquisa para procurar **o Azure Sentinel**.

   ![Procurar o Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *Procurar o Azure Sentinel*

4. **Selecione Adicionar** e, em seguida, selecione o espaço de trabalho Log Analytics que criou anteriormente.

   ![Adicione um espaço de trabalho Log Analytics](./media/secure-web-app/sentinel-workspace-add.png)

    *Adicione um espaço de trabalho Log Analytics*

5. Na página **Azure Sentinel - Conectores de dados,** em **Configuração,** selecione **Conectores de Dados**. Você vê uma variedade de serviços Azure que você pode ligar ao caso de armazenamento Log Analytics para análise em Azure Sentinel.

   ![Log Analytics conectores de dados](./media/secure-web-app/sentinel-connectors.png)

    *Adicione um conector de dados ao Azure Sentinel*

   Por exemplo, para ligar o gateway de aplicações, tome estes passos:

   1. Abra a lâmina de exemplo de gateway de aplicação Azure.
   2. Em **Monitorização**, selecione **Definições de diagnóstico**.
   3. **Selecione Adicionar a definição de diagnóstico**.

      ![Adicionar diagnósticos de Gateway de Aplicação](./media/secure-web-app/sentinel-gateway-connector.png)

      *Adicionar diagnósticos de Gateway de Aplicação*

   4. Na página de **definições de Diagnóstico,** selecione o espaço de trabalho Log Analytics que criou e, em seguida, selecione todas as métricas que pretende recolher e enviar para Azure Sentinel. Selecione **Guardar**.

        ![Definições do conector Azure Sentinel](./media/secure-web-app/sentinel-connector-settings.png)

        *Definições do conector Azure Sentinel*

  As métricas do recurso estão em Azure Sentinel, onde pode consultar e investigar.

   Adicione as mesmas métricas em definições de diagnóstico para Azure Key Vault, o endereço IP público, Base de Dados Azure para PostgreSQL, e quaisquer serviços que suportem registos de diagnóstico na sua conta.

Depois de configurar as métricas, o Azure Sentinel tem dados para analisar.

## <a name="evaluate-and-verify"></a>Avaliar e verificar

Depois de desenvolver e implementar a arquitetura, tem de garantir que o código e os serviços implantados cumprem as normas de segurança. Estes são alguns passos que pode tomar para verificar o software:

- Análise de código estático
- Digitalização de vulnerabilidades
- Encontrar e corrigir vulnerabilidades nas dependências das aplicações

Estes são os blocos básicos de construção para as melhores práticas em desenvolvimento seguro.

### <a name="static-code-analysis"></a>Análise de código estático

Para a aplicação da amostra, a verificação com ferramentas de análise estática envolve encontrar vulnerabilidades no código da aplicação, utilizando técnicas como verificação de manchas e análise de fluxo de dados. As ferramentas de análise estática python dão-lhe mais confiança de que a sua aplicação está segura.

**Linting**

PyFlakes, uma biblioteca de linting Python, ajuda-o a remover código morto e funções não uusadas de aplicações, como mostrado aqui:

![PyFlakes](./media/secure-web-app/pyflakes.png)

O linting fornece pistas e possíveis alterações que podem tornar o seu código mais limpo e menos propenso a erros durante o tempo de funcionamento.

**Rio PyLint**

A PyLint forneceu o maior valor para este projeto. Executa verificações padrão de código, verificação de erros e dicas de refactor para garantir que o código em execução no servidor é seguro. Ao utilizar o PyLint para atualizar o seu código, pode eliminar bugs e melhorar a classificação de PyLint, como mostram as seguintes imagens.

![Antes de PyLint](./media/secure-web-app/before-pylint.png)

*Antes de PyLint*

Depois de corrigir alguns dos erros de código encontrados pelas ferramentas de linagem, tem mais confiança de que o código não é propenso a erros. A correção dos erros reduz significativamente os riscos de segurança que podem ocorrer quando o código é implantado em ambientes de produção.

![Depois de Pylint](./media/secure-web-app/after-pylint.png)

*Depois de PyLint*

### <a name="vulnerability-scanning"></a>Digitalização de vulnerabilidades

A ferramenta [ZAP da OWASP](https://www.zaproxy.org/) é um scanner de vulnerabilidade de aplicação web de código aberto que pode usar para verificar as vulnerabilidades da aplicação da amostra. Executar a ferramenta na aplicação da amostra revela alguns possíveis erros e vetores de ataque.

![Ferramenta ZAP](./media/secure-web-app/zap-tool.png)

*Ferramenta ZAP*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Encontrar e corrigir vulnerabilidades nas dependências de aplicações

Para encontrar e corrigir as dependências de aplicações, pode utilizar [o Controlo de Dependência da OWASP.](https://owasp.org/www-project-dependency-check/)

A segurança é uma aplicação semelhante que verifica as dependências. Pode encontrá-lo no [GitHub.](https://github.com/pyupio/safety) Verificações de segurança para vulnerabilidades encontradas em bases de dados de vulnerabilidades bem conhecidas.

![Segurança](./media/secure-web-app/pysafety.png)

*Segurança*

## <a name="next-steps"></a>Passos seguintes

Os seguintes artigos podem ajudá-lo a projetar, desenvolver e implementar aplicações seguras.

- [Design](secure-design.md)
- [Programar](secure-develop.md)
- [Implementar](secure-deploy.md)
