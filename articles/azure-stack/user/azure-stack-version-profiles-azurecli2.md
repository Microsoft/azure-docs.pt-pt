---
title: Ligar ao Azure Stack com a CLI | Documentos da Microsoft
description: Saiba como utilizar a interface de linha de comandos para várias plataformas (CLI) para gerir e implementar recursos no Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 261efda18b7cecc6370743c604622a8884ff8364
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57732313"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Utilizar perfis de versão de API com a CLI do Azure no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode seguir os passos neste artigo para definir a Interface de linha de comandos do Azure (CLI) para gerir recursos do Azure Stack Development Kit (ASDK) a partir de plataformas de cliente de Linux, Mac e Windows.

## <a name="prepare-for-azure-cli"></a>Preparar para a CLI do Azure

Terá do certificado de AC de raiz para o Azure Stack utilizar a CLI do Azure no seu computador de desenvolvimento. Utilizar o certificado para gerir os recursos através da CLI.

 - **O certificado de raiz da AC do Azure Stack** é necessário se estiver a utilizar a CLI a partir de uma estação de trabalho fora do ASDK.  

 - **O ponto final de aliases de máquina virtual** fornece um alias, como "UbuntuLTS" ou "Win2012Datacenter", que faz referência a um publicador de imagem, oferta, SKU e versão como um único parâmetro durante a implantação de VMs.  

As secções seguintes descrevem como obter estes valores.

### <a name="export-the-azure-stack-ca-root-certificate"></a>Exporte o certificado de raiz de AC do Azure Stack

Se estiver a utilizar um sistema integrado, não terá de exportar o certificado de raiz da AC. Terá de exportar o certificado de raiz de AC num ASDK.

Para exportar o certificado de raiz ASDK no formato PEM:

1. [Criar um VM do Windows no Azure Stack](azure-stack-quick-windows-portal.md).

2. Entrar para a máquina, abra uma linha de comandos elevada do PowerShell e, em seguida, execute o seguinte script:

      ```powershell  
      $label = "AzureStackSelfSignedRootCert"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }
      
    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
```

3. Copie o certificado no seu computador local.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configurar o ponto final de aliases de máquina virtual

Pode configurar um ponto final acessível publicamente que aloja um arquivo de alias da máquina virtual. O ficheiro de alias da máquina virtual é um ficheiro JSON que fornece um nome comum para uma imagem. Irá utilizar o nome ao implementar uma VM como um parâmetro de CLI do Azure.

1. Se publicar uma imagem personalizada, anote as informações do publicador, oferta, SKU e versão que especificou durante a publicação. Se for uma imagem do marketplace, pode ver as informações utilizando o ```Get-AzureVMImage``` cmdlet.  

2. Transfira o [ficheiro de exemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) do GitHub.

4. Crie uma conta de armazenamento no Azure Stack. Quando isso é feito, crie um contentor de Blobs. Definir a política de acesso para "public".  

3. Carregar o ficheiro JSON para o novo contentor. Quando isso for concluído, pode ver o URL do blob. Selecione o nome do blob e, em seguida, selecionar o URL de propriedades do blob.

### <a name="install-or-ugrade-cli"></a>Instalar ou atualizar os CLI

Inicie sessão na sua estação de trabalho de desenvolvimento e instalar a CLI. O Azure Stack requer a versão 2.0 ou posterior da CLI do Azure. A versão mais recente dos perfis de API requer uma versão atual da CLI.  Pode instalar a CLI ao utilizar os passos descritos na [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) artigo. Para verificar se a instalação foi concluída com êxito, abra um terminal ou janela de linha de comandos e execute o seguinte comando:

```azurecli
az --version
```

Deverá ver a versão da CLI do Azure e outras bibliotecas dependentes instaladas no seu computador.

### <a name="install-python-on-windows"></a>Instalar o Python no Windows

1. Instale [Python 3 no seu sistema](https://www.python.org/downloads/).

2. Atualize o PIP. PIP é um Gestor de pacotes do Python. Abra um prompt de comando ou uma linha de comandos elevada do PowerShell e escreva o seguinte comando:

    ```PowerShell  
    python -m pip install --upgrade pip
    ```

3. Instalar o **certifi** módulo. [Certifi](https://pypi.org/project/certifi/) um módulo e uma coleção de certificados de raiz para validar a fidedignidade de certificados SSL ao verificar a identidade dos anfitriões TLS. Abra um prompt de comando ou uma linha de comandos elevada do PowerShell e escreva o seguinte comando:

    ```PowerShell
    pip install certifi
    ```

### <a name="install-python-on-linux"></a>Instalar o Python no Linux

1. A imagem de Ubuntu 16.04 vem com o Python 2.7 e o Python 3.5 instalado por predefinição. Pode verificar a sua versão do Python 3 executando o seguinte comando:

    ```bash  
    python3 --version
    ```

2. Atualize o PIP. PIP é um Gestor de pacotes do Python. Abra um prompt de comando ou uma linha de comandos elevada do PowerShell e escreva o seguinte comando:

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. Instalar o **certifi** módulo. [Certifi](https://pypi.org/project/certifi/) é uma coleção de certificados de raiz para validar a fidedignidade de certificados SSL ao verificar a identidade dos anfitriões TLS. Abra um prompt de comando ou uma linha de comandos elevada do PowerShell e escreva o seguinte comando:

    ```bash
    pip3 install certifi
    ```

### <a name="install-python-on-macos"></a>Instalar o Python em macOS

1. Instale [Python 3 no seu sistema](https://www.python.org/downloads/). Para versões de Python 3.7, Python.org fornece duas opções de binários do instalador para download. A variante de padrão é 64-bit-só e funciona em macOS 10.9 (Mavericks) e sistemas posteriores. Verificar a sua versão de python ao abrir o terminal e escrevendo o seguinte comando:

    ```bash  
    python3 --version
    ```

2. Atualize o PIP. PIP é um Gestor de pacotes do Python. Abra um prompt de comando ou uma linha de comandos elevada do PowerShell e escreva o seguinte comando:

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. Instalar o **certifi** módulo. [Certifi](https://pypi.org/project/certifi/) um módulo e uma coleção de certificados de raiz para validar a fidedignidade de certificados SSL ao verificar a identidade dos anfitriões TLS. Abra um prompt de comando ou uma linha de comandos elevada do PowerShell e escreva o seguinte comando:

    ```bash
    pip3 install certifi
    ```

## <a name="windows-azure-ad"></a>Windows (Azure AD)

Esta secção explica como configurar a CLI se estiver a utilizar o Azure AD como o serviço de gestão de identidade e estiver a utilizar o CLI numa máquina Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar no certificado de raiz de AC do Azure Stack

Se estiver a utilizar o ASDK, terá de confiar no certificado de raiz da AC no seu computador remoto. Não terá de fazê-lo com os sistemas de intregrated.

Para confiar no certificado de raiz de AC do Azure Stack, anexe-o para o certificado de Python existente.

1. Encontre a localização do certificado no seu computador. A localização pode variar dependendo de onde instalou o Python. Abra um prompt de comando ou uma linha de comandos elevada do PowerShell e escreva o seguinte comando:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Tome nota da localização do certificado. Por exemplo, `~/lib/python3.5/site-packages/certifi/cacert.pem`. O caminho específico dependerá seu sistema operacional e a versão do Python que tenha instalado.

2. Confie no certificado de raiz de AC do Azure Stack, acrescentando-lo para o certificado de Python existente.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Ligar ao Azure Stack

1. Registe o seu ambiente do Azure Stack executando o `az cloud register` comando.

    Em alguns cenários, a conectividade de internet de saída direta é encaminhada através de um proxy ou firewall, que impõe a interceptação de SSL. Nestes casos, o `az cloud register` comando pode falhar com um erro, como "Não é possível obter pontos finais de cloud". Para contornar este erro, pode definir as seguintes variáveis de ambiente:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Registe o seu ambiente. Utilize os seguintes parâmetros ao executar `az cloud register`.
    | Value | Exemplo | Descrição |
    | --- | --- | --- |
    | Nome do ambiente | AzureStackUser | Utilize `AzureStackUser` para o ambiente do utilizador. Se estiver a operador, especificar `AzureStackAdmin`. |
    | Ponto final do Gestor de recursos | https://management.local.azurestack.external | O **ResourceManagerUrl** no Azure Stack Development Kit (ASDK) é: `https://management.local.azurestack.external/` O **ResourceManagerUrl** em sistemas integrados é: `https://management.<region>.<fqdn>/` Para obter os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Se tiver uma pergunta sobre o ponto de extremidade do sistema integrado, entre em contato com o operador da cloud. |
    | Ponto final de armazenamento | local.azurestack.external | `local.azurestack.external` destina-se a ASDK. Para um sistema de intregrated, deverá utilizar um ponto final para o seu sistema.  |
    | Sufixo de Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` destina-se a ASDK. Para um sistema integrado, deverá utilizar um ponto final para o seu sistema.  |
    | Imagem da VM alias doc ponto final, | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI do documento que contenha aliases de imagem de máquina virtual. Para obter mais informações, consulte [# # # configurar o ponto final de aliases de máquina virtual](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Defina o ambiente do Active Directory com os seguintes comandos.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Atualize a configuração do seu ambiente para utilizar o perfil de versão de API específico do Azure Stack. Para atualizar a configuração, execute o seguinte comando:

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Se estiver a executar uma versão do Azure Stack antes da compilação de 1808, tem de utilizar o perfil da versão de API **2017-03-09-perfil** em vez do perfil da versão de API **2018-03-01-híbrida**. Terá de estar a utilizar uma versão recente da CLI do Azure.
 
1. Inicie sessão no seu ambiente do Azure Stack, utilizando o `az login` comando. Pode iniciar sessão no ambiente do Azure Stack como um utilizador ou como um [principal de serviço](../../active-directory/develop/app-objects-and-service-principals.md). 

  - Inicie sessão como um *utilizador*: 

    Pode especificar o nome de utilizador e palavra-passe diretamente dentro do `az login` comando ou autenticar com um browser. Terá de efetuar a última opção se a sua conta tiver a autenticação multifator ativada:

    ```azurecli
    az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
    ```

    > [!NOTE]
    > Se a sua conta de utilizador tiver a autenticação multifator ativada, pode utilizar o `az login` comando sem fornecer o `-u` parâmetro. Executar o comando seguinte dá-lhe um URL e um código que tem de utilizar a autenticação.

  - Inicie sessão como um *principal de serviço*: 
    
    Antes de iniciar sessão, [criar um principal de serviço através do portal do Azure](azure-stack-create-service-principals.md) ou a CLI e atribuí-lo uma função. Agora, inicie sessão com o seguinte comando:

    ```azurecli  
    az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
    ```

### <a name="test-the-connectivity"></a>Testar a conectividade

Com tudo o que configurar, utilizar a CLI para criar recursos no Azure Stack. Por exemplo, pode criar um grupo de recursos para uma aplicação e adicionar uma máquina virtual. Utilize o seguinte comando para criar um grupo de recursos com o nome "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Se o grupo de recursos é criado com êxito, o comando anterior devolve as seguintes propriedades do recurso recentemente criado:

![Saída de criar grupo de recursos](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

Esta secção explica como configurar a CLI se estiver a utilizar o Active Directory Federated Services (AD FS) que o seu serviço de gestão de identidade e estiver a utilizar o CLI numa máquina Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar no certificado de raiz de AC do Azure Stack

Se estiver a utilizar o ASDK, terá de confiar no certificado de raiz da AC no seu computador remoto. Não terá de fazê-lo com os sistemas de intregrated.

1. Encontre a localização do certificado no seu computador. A localização pode variar dependendo de onde instalou o Python. Abra um prompt de comando ou uma linha de comandos elevada do PowerShell e escreva o seguinte comando:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Tome nota da localização do certificado. Por exemplo, `~/lib/python3.5/site-packages/certifi/cacert.pem`. O caminho específico dependerá seu sistema operacional e a versão do Python que tenha instalado.

2. Confie no certificado de raiz de AC do Azure Stack, acrescentando-lo para o certificado de Python existente.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Ligar ao Azure Stack

1. Registe o seu ambiente do Azure Stack executando o `az cloud register` comando.

    Em alguns cenários, a conectividade de internet de saída direta é encaminhada através de um proxy ou firewall, que impõe a interceptação de SSL. Nestes casos, o `az cloud register` comando pode falhar com um erro, como "Não é possível obter pontos finais de cloud". Para contornar este erro, pode definir as seguintes variáveis de ambiente:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Registe o seu ambiente. Utilize os seguintes parâmetros ao executar `az cloud register`.
    | Value | Exemplo | Descrição |
    | --- | --- | --- |
    | Nome do ambiente | AzureStackUser | Utilize `AzureStackUser` para o ambiente do utilizador. Se estiver a operador, especificar `AzureStackAdmin`. |
    | Ponto final do Gestor de recursos | https://management.local.azurestack.external | O **ResourceManagerUrl** no Azure Stack Development Kit (ASDK) é: `https://management.local.azurestack.external/` O **ResourceManagerUrl** em sistemas integrados é: `https://management.<region>.<fqdn>/` Para obter os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Se tiver uma pergunta sobre o ponto de extremidade do sistema integrado, entre em contato com o operador da cloud. |
    | Ponto final de armazenamento | local.azurestack.external | `local.azurestack.external` destina-se a ASDK. Para um sistema de intregrated, deverá utilizar um ponto final para o seu sistema.  |
    | Sufixo de Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` destina-se a ASDK. Para um sistema integrado, deverá utilizar um ponto final para o seu sistema.  |
    | Imagem da VM alias doc ponto final, | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI do documento que contenha aliases de imagem de máquina virtual. Para obter mais informações, consulte [# # # configurar o ponto final de aliases de máquina virtual](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Defina o ambiente do Active Directory com os seguintes comandos.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Atualize a configuração do seu ambiente para utilizar o perfil de versão de API específico do Azure Stack. Para atualizar a configuração, execute o seguinte comando:

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Se estiver a executar uma versão do Azure Stack antes da compilação de 1808, tem de utilizar o perfil da versão de API **2017-03-09-perfil** em vez do perfil da versão de API **2018-03-01-híbrida**. Terá de estar a utilizar uma versão recente da CLI do Azure.

1. Inicie sessão no seu ambiente do Azure Stack, utilizando o `az login` comando. Pode iniciar sessão no ambiente do Azure Stack como um utilizador ou como um [principal de serviço](../../active-directory/develop/app-objects-and-service-principals.md). 

  - Inicie sessão como um *utilizador*:

    Pode especificar o nome de utilizador e palavra-passe diretamente dentro do `az login` comando ou autenticar com um browser. Terá de efetuar a última opção se a sua conta tiver a autenticação multifator ativada:

    ```azurecli
    az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/" --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2018-03-01-hybrid"
    ```

    > [!NOTE]
    > Se a sua conta de utilizador tiver a autenticação multifator ativada, pode utilizar o `az login` comando sem fornecer o `-u` parâmetro. Executar o comando seguinte dá-lhe um URL e um código que tem de utilizar a autenticação.

  - Inicie sessão como um *principal de serviço*: 
    
    Prepare o ficheiro. pem a ser utilizado para início de sessão principal de serviço.

    No computador cliente em que o principal foi criado, a exportar o certificado de principal de serviço, como um pfx com a chave privada localizado em `cert:\CurrentUser\My`; o nome tem o mesmo nome que o principal de certificado.

    Converta o pfx em pem (utilize o utilitário de OpenSSL).

    Entrar para a CLI:
  
    ```azurecli  
    az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
    ```

### <a name="test-the-connectivity"></a>Testar a conectividade

Com tudo o que configurar, utilizar a CLI para criar recursos no Azure Stack. Por exemplo, pode criar um grupo de recursos para uma aplicação e adicionar uma máquina virtual. Utilize o seguinte comando para criar um grupo de recursos com o nome "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Se o grupo de recursos é criado com êxito, o comando anterior devolve as seguintes propriedades do recurso recentemente criado:

![Saída de criar grupo de recursos](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

Esta secção explica como configurar a CLI se estiver a utilizar o Azure AD como o serviço de gestão de identidade e estiver a utilizar o CLI num computador Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar no certificado de raiz de AC do Azure Stack

Se estiver a utilizar o ASDK, terá de confiar no certificado de raiz da AC no seu computador remoto. Não terá de fazê-lo com os sistemas de intregrated.

Confie no certificado de raiz de AC do Azure Stack, acrescentando-lo para o certificado de Python existente.

1. Encontre a localização do certificado no seu computador. A localização pode variar dependendo de onde instalou o Python. Tem de ter o pip e o certifi [módulo instalado](#install-python-on-linux). Pode utilizar o seguinte comando de Python de linha de comandos do bash:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Tome nota da localização do certificado; Por exemplo, `~/lib/python3.5/site-packages/certifi/cacert.pem`. O caminho específico depende do seu sistema operativo e a versão do Python que tenha instalado.

2. Execute o seguinte comando de bash com o caminho para o certificado.

  - Para uma máquina Linux remota:

    ```bash  
    sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
    ```

  - Para uma máquina Linux dentro do ambiente do Azure Stack:

    ```bash  
    sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
    ```

### <a name="connect-to-azure-stack"></a>Ligar ao Azure Stack

Utilize os seguintes passos para ligar ao Azure Stack:

1. Registe o seu ambiente do Azure Stack executando o `az cloud register` comando. Em alguns cenários, a conectividade de internet de saída direta é encaminhada através de um proxy ou firewall, que impõe a interceptação de SSL. Nestes casos, o `az cloud register` comando pode falhar com um erro, como "Não é possível obter pontos finais de cloud". Para contornar este erro, pode definir as seguintes variáveis de ambiente:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Registe o seu ambiente. Utilize os seguintes parâmetros ao executar `az cloud register`.
    | Value | Exemplo | Descrição |
    | --- | --- | --- |
    | Nome do ambiente | AzureStackUser | Utilize `AzureStackUser` para o ambiente do utilizador. Se estiver a operador, especificar `AzureStackAdmin`. |
    | Ponto final do Gestor de recursos | https://management.local.azurestack.external | O **ResourceManagerUrl** no Azure Stack Development Kit (ASDK) é: `https://management.local.azurestack.external/` O **ResourceManagerUrl** em sistemas integrados é: `https://management.<region>.<fqdn>/` Para obter os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Se tiver uma pergunta sobre o ponto de extremidade do sistema integrado, entre em contato com o operador da cloud. |
    | Ponto final de armazenamento | local.azurestack.external | `local.azurestack.external` destina-se a ASDK. Para um sistema de intregrated, deverá utilizar um ponto final para o seu sistema.  |
    | Sufixo de Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` destina-se a ASDK. Para um sistema integrado, deverá utilizar um ponto final para o seu sistema.  |
    | Imagem da VM alias doc ponto final, | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI do documento que contenha aliases de imagem de máquina virtual. Para obter mais informações, consulte [# # # configurar o ponto final de aliases de máquina virtual](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Defina o ambiente do Active Directory. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Atualize a configuração do seu ambiente para utilizar o perfil de versão de API específico do Azure Stack. Para atualizar a configuração, execute o seguinte comando:

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Se estiver a executar uma versão do Azure Stack antes da compilação de 1808, tem de utilizar o perfil da versão de API **2017-03-09-perfil** em vez do perfil da versão de API **2018-03-01-híbrida**. Terá de estar a utilizar uma versão recente da CLI do Azure.

5. Inicie sessão no seu ambiente do Azure Stack, utilizando o `az login` comando. Pode iniciar sessão no ambiente do Azure Stack como um utilizador ou como um [principal de serviço](../../active-directory/develop/app-objects-and-service-principals.md). 

    * Inicie sessão como um *utilizador*:

    Pode especificar o nome de utilizador e palavra-passe diretamente dentro do `az login` comando ou autenticar com um browser. Terá de efetuar a última opção se a sua conta tiver a autenticação multifator ativada:

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

    > [!NOTE]
    > Se a sua conta de utilizador tiver a autenticação multifator ativada, pode utilizar o `az login` comando sem fornecer o `-u` parâmetro. Executar o comando seguinte dá-lhe um URL e um código que tem de utilizar a autenticação.
   
    * Inicie sessão como um *principal de serviço*
    
    Antes de iniciar sessão, [criar um principal de serviço através do portal do Azure](azure-stack-create-service-principals.md) ou a CLI e atribuí-lo uma função. Agora, inicie sessão com o seguinte comando:

      ```azurecli  
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

### <a name="test-the-connectivity"></a>Testar a conectividade

Com tudo o que configurar, utilizar a CLI para criar recursos no Azure Stack. Por exemplo, pode criar um grupo de recursos para uma aplicação e adicionar uma máquina virtual. Utilize o seguinte comando para criar um grupo de recursos com o nome "MyResourceGroup":

```azurecli
    az group create -n MyResourceGroup -l local
```

Se o grupo de recursos é criado com êxito, o comando anterior devolve as seguintes propriedades do recurso recentemente criado:

![Saída de criar grupo de recursos](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

Esta secção explica como configurar a CLI se estiver a utilizar o Active Directory Federated Services (AD FS) que o seu serviço de gestão e estiver a utilizar o CLI num computador Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar no certificado de raiz de AC do Azure Stack

Se estiver a utilizar o ASDK, terá de confiar no certificado de raiz da AC no seu computador remoto. Não terá de fazê-lo com os sistemas de intregrated.

Confie no certificado de raiz de AC do Azure Stack, acrescentando-lo para o certificado de Python existente.

1. Encontre a localização do certificado no seu computador. A localização pode variar dependendo de onde instalou o Python. Tem de ter o pip e o certifi [módulo instalado](#install-python-on-linux). Pode utilizar o seguinte comando de Python de linha de comandos do bash:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Tome nota da localização do certificado; Por exemplo, `~/lib/python3.5/site-packages/certifi/cacert.pem`. O caminho específico depende do seu sistema operativo e a versão do Python que tenha instalado.

2. Execute o seguinte comando de bash com o caminho para o certificado.

  - Para uma máquina Linux remota:

    ```bash  
    sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
    ```

  - Para uma máquina Linux dentro do ambiente do Azure Stack:

    ```bash  
    sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
    ```

### <a name="connect-to-azure-stack"></a>Ligar ao Azure Stack

Utilize os seguintes passos para ligar ao Azure Stack:

1. Registe o seu ambiente do Azure Stack executando o `az cloud register` comando. Em alguns cenários, a conectividade de internet de saída direta é encaminhada através de um proxy ou firewall, que impõe a interceptação de SSL. Nestes casos, o `az cloud register` comando pode falhar com um erro, como "Não é possível obter pontos finais de cloud". Para contornar este erro, pode definir as seguintes variáveis de ambiente:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Registe o seu ambiente. Utilize os seguintes parâmetros ao executar `az cloud register`.
    | Value | Exemplo | Descrição |
    | --- | --- | --- |
    | Nome do ambiente | AzureStackUser | Utilize `AzureStackUser` para o ambiente do utilizador. Se estiver a operador, especificar `AzureStackAdmin`. |
    | Ponto final do Gestor de recursos | https://management.local.azurestack.external | O **ResourceManagerUrl** no Azure Stack Development Kit (ASDK) é: `https://management.local.azurestack.external/` O **ResourceManagerUrl** em sistemas integrados é: `https://management.<region>.<fqdn>/` Para obter os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Se tiver uma pergunta sobre o ponto de extremidade do sistema integrado, entre em contato com o operador da cloud. |
    | Ponto final de armazenamento | local.azurestack.external | `local.azurestack.external` destina-se a ASDK. Para um sistema de intregrated, deverá utilizar um ponto final para o seu sistema.  |
    | Sufixo de Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` destina-se a ASDK. Para um sistema integrado, deverá utilizar um ponto final para o seu sistema.  |
    | Imagem da VM alias doc ponto final, | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI do documento que contenha aliases de imagem de máquina virtual. Para obter mais informações, consulte [# # # configurar o ponto final de aliases de máquina virtual](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Defina o ambiente do Active Directory. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Atualize a configuração do seu ambiente para utilizar o perfil de versão de API específico do Azure Stack. Para atualizar a configuração, execute o seguinte comando:

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Se estiver a executar uma versão do Azure Stack antes da compilação de 1808, tem de utilizar o perfil da versão de API **2017-03-09-perfil** em vez do perfil da versão de API **2018-03-01-híbrida**. Terá de estar a utilizar uma versão recente da CLI do Azure.

5. Inicie sessão no seu ambiente do Azure Stack, utilizando o `az login` comando. Pode iniciar sessão no ambiente do Azure Stack como um utilizador ou como um [principal de serviço](../../active-directory/develop/app-objects-and-service-principals.md). 

6. Início de sessão: 

  *  Como um **utilizador** usando um navegador da web com um código de dispositivo:  

  ```azurecli  
    az login --use-device-code
  ```

  > [!NOTE]  
  >Executar o comando dá-lhe um URL e um código que tem de utilizar a autenticação.

  * Como um principal de serviço:
        
    Prepare o ficheiro. pem a ser utilizado para início de sessão principal de serviço.

      * No computador cliente em que o principal foi criado, a exportar o certificado de principal de serviço, como um pfx com a chave privada localizado em `cert:\CurrentUser\My`; o nome tem o mesmo nome que o principal de certificado.
  
      * Converta o pfx em pem (utilize o utilitário de OpenSSL).

    Entrar para a CLI:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>Testar a conectividade

Com tudo o que configurar, utilizar a CLI para criar recursos no Azure Stack. Por exemplo, pode criar um grupo de recursos para uma aplicação e adicionar uma máquina virtual. Utilize o seguinte comando para criar um grupo de recursos com o nome "MyResourceGroup":

```azurecli
  az group create -n MyResourceGroup -l local
```

Se o grupo de recursos é criado com êxito, o comando anterior devolve as seguintes propriedades do recurso recentemente criado:

![Saída de criar grupo de recursos](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Problemas conhecidos

Aqui estão os problemas conhecidos com a CLI no Azure Stack:

 - O modo interativo da CLI; Por exemplo, o `az interactive` comando este problema, ainda não é suportado no Azure Stack.
 - Para obter a lista de imagens de máquinas virtuais disponíveis no Azure Stack, utilize o `az vm image list --all` comando, em vez do `az vm image list` comando. Especificar o `--all` opção garante que a resposta retorna apenas as imagens que estão disponíveis no seu ambiente do Azure Stack.
 - Aliases de imagem de máquina virtual que estão disponíveis no Azure podem não ser aplicáveis para o Azure Stack. Quando utilizar imagens de máquinas virtuais, tem de utilizar o parâmetro URN inteiro (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) em vez do alias da imagem. Este URN tem de corresponder as especificações de imagem como deriva a `az vm images list` comando.

## <a name="next-steps"></a>Passos Seguintes

- [Implementar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)
- [Ativar a CLI do Azure para utilizadores do Azure Stack (operador)](../azure-stack-cli-admin.md)
- [Gerir permissões de utilizador](azure-stack-manage-permissions.md) 
