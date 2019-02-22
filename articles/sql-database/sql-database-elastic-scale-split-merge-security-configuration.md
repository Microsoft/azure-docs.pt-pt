---
title: Configuração de segurança de dividir / unir | Documentos da Microsoft
description: Configurar x409 certificados para a encriptação com o serviço de divisão/intercalação para o dimensionamento flexível.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 051aa6b6ca8571fe948fa30e1e4a4320bb564a52
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593323"
---
# <a name="split-merge-security-configuration"></a>Configuração de segurança de dividir / unir

Para utilizar o serviço de divisão/intercalação, tem de configurar corretamente a segurança. O serviço é parte da funcionalidade do dimensionamento flexível da base de dados do Microsoft Azure SQL. Para obter mais informações, consulte [Split de escala elástica e intercalar Tutorial de serviço](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Configurar certificados

Certificados são configurados de duas formas. 

1. [Para configurar o certificado SSL](#to-configure-the-ssl-certificate)
2. [Configurar certificados de cliente](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Para obter certificados

Podem obter certificados de autoridades de certificação (AC) pública ou a partir da [serviço de certificados do Windows](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Estes são os métodos preferenciais para obter certificados.

Se essas opções não estão disponíveis, pode gerar **certificados autoassinados**.

## <a name="tools-to-generate-certificates"></a>Ferramentas para gerar certificados

* [makecert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Para executar as ferramentas

* De um desenvolvedor de linha de comandos para Visual Studio, consulte [Prompt de comando do Visual Studio](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    Se instalado, aceda a:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Obter o WDK de [Windows 8.1: Transferir kits e ferramentas](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Para configurar o certificado SSL

É necessário um certificado SSL para encriptar a comunicação e autenticar o servidor. Escolha o mais aplicável dos três cenários abaixo e executar todos os seus passos:

### <a name="create-a-new-self-signed-certificate"></a>Criar um novo certificado autoassinado

1. [Criar um certificado Autoassinado](#create-a-self-signed-certificate)
2. [Criar o ficheiro PFX para o certificado de SSL autoassinado](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Carregar certificado SSL para o serviço em nuvem](#upload-ssl-certificate-to-cloud-service)
4. [Atualizar o certificado SSL no ficheiro de configuração de serviço](#update-ssl-certificate-in-service-configuration-file)
5. [Autoridade de certificação de SSL de importação](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Para utilizar um certificado existente do arquivo de certificados
1. [Exportar o certificado SSL do Store de certificado](#export-ssl-certificate-from-certificate-store)
2. [Carregar certificado SSL para o serviço em nuvem](#upload-ssl-certificate-to-cloud-service)
3. [Atualizar o certificado SSL no ficheiro de configuração de serviço](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Para utilizar um certificado existente num ficheiro PFX
1. [Carregar certificado SSL para o serviço em nuvem](#upload-ssl-certificate-to-cloud-service)
2. [Atualizar o certificado SSL no ficheiro de configuração de serviço](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Configurar certificados de cliente
Certificados de cliente são necessárias para autenticar pedidos para o serviço. Escolha o mais aplicável dos três cenários abaixo e executar todos os seus passos:

### <a name="turn-off-client-certificates"></a>Desativar a certificados de cliente
1. [Desativar a autenticação baseada em certificados de cliente](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Emita novos certificados de cliente autoassinado
1. [Criar uma autoridade de certificação Autoassinado](#create-a-self-signed-certification-authority)
2. [Carregar certificado de AC para o serviço em nuvem](#upload-ca-certificate-to-cloud-service)
3. [Atualizar o certificado de AC no ficheiro de configuração de serviço](#update-ca-certificate-in-service-configuration-file)
4. [Emitir certificados de cliente](#issue-client-certificates)
5. [Criar ficheiros PFX para certificados de cliente](#create-pfx-files-for-client-certificates)
6. [Importar o certificado de cliente](#Import-Client-Certificate)
7. [Copie os Thumbprints de certificado de cliente](#copy-client-certificate-thumbprints)
8. [Configurar clientes permitidos no ficheiro de configuração do serviço](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Utilizar certificados de cliente existentes
1. [Localizar a chave pública de AC](#find-ca-public-key)
2. [Carregar certificado de AC para o serviço em nuvem](#Upload-CA-certificate-to-cloud-service)
3. [Atualizar o certificado de AC no ficheiro de configuração de serviço](#Update-CA-Certificate-in-Service-Configuration-File)
4. [Copie os Thumbprints de certificado de cliente](#Copy-Client-Certificate-Thumbprints)
5. [Configurar clientes permitidos no ficheiro de configuração do serviço](#configure-allowed-clients-in-the-service-configuration-file)
6. [Configurar a verificação de revogação de certificado de cliente](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Acesso aos pontos finais do serviço pode ser restringido a intervalos específicos de endereços IP.

## <a name="to-configure-encryption-for-the-store"></a>Para configurar a encriptação para o armazenamento
É necessário um certificado para encriptar as credenciais que são armazenadas no arquivo de metadados. Escolha o mais aplicável dos três cenários abaixo e executar todos os seus passos:

### <a name="use-a-new-self-signed-certificate"></a>Utilizar um novo certificado autoassinado
1. [Criar um certificado Autoassinado](#create-a-self-signed-certificate)
2. [Criar o ficheiro PFX para o certificado de encriptação autoassinado](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Carregar certificado de encriptação para o serviço em nuvem](#upload-encryption-certificate-to-cloud-service)
4. [Atualizar o certificado de encriptação no ficheiro de configuração de serviço](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Utilizar um certificado existente do arquivo de certificados
1. [Exportar o certificado de encriptação do certificado Store](#export-encryption-certificate-from-certificate-store)
2. [Carregar certificado de encriptação para o serviço em nuvem](#upload-encryption-certificate-to-cloud-service)
3. [Atualizar o certificado de encriptação no ficheiro de configuração de serviço](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Utilizar um certificado existente num ficheiro PFX
1. [Carregar certificado de encriptação para o serviço em nuvem](#upload-encryption-certificate-to-cloud-service)
2. [Atualizar o certificado de encriptação no ficheiro de configuração de serviço](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>A configuração predefinida
A configuração predefinida nega todo o acesso ao ponto final de HTTP. Esta é a definição recomendada, uma vez que os pedidos para estes pontos finais podem trazer a informações confidenciais, como credenciais de base de dados.
A configuração predefinida permite que todo o acesso ao ponto final HTTPS. Esta definição pode ser restringida ainda mais.

### <a name="changing-the-configuration"></a>A alteração da configuração
O grupo de regras de controlo de acesso que se aplicam a e o ponto final são configurados no  **\<EndpointAcls >** secção a **ficheiro de configuração do serviço**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

As regras num grupo de controlo de acesso são configuradas num \<AccessControl nome = "" > secção do ficheiro de configuração do serviço. 

O formato é explicado na documentação de listas de controlo de acesso de rede.
Por exemplo, para permitir que apenas os IPs no intervalo 100.100.0.0 para 100.100.255.255 para aceder ao ponto final HTTPS, as regras teria o seguinte aspeto:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Negação de prevenção de serviço
Existem dois mecanismos diferentes suportados para detetar e prevenir ataques Denial of Service:

* Restringir o número de pedidos em simultâneo por anfitrião remoto (desativado por predefinição)
* Restringir a taxa de acesso por anfitrião remoto (por predefinição)

Eles se baseiam nos recursos de documentado mais adiante no segurança IP dinâmica no IIS. Quando alterar esta configuração Tome cuidado com os seguintes fatores:

* O comportamento de proxies e dispositivos de tradução de endereços de rede sobre as informações do anfitrião remoto
* Cada solicitação a qualquer recurso na função da web é considerada (por exemplo, carregamento de scripts, imagens, etc)

## <a name="restricting-number-of-concurrent-accesses"></a>Restringir o número de acessos simultâneos
As definições que configurar esse comportamento são:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Alterar DynamicIpRestrictionDenyByConcurrentRequests como true para ativar esta proteção.

## <a name="restricting-rate-of-access"></a>Taxa de restrição de acesso
As definições que configurar esse comportamento são:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Configurar a resposta a um pedido negado
A definição seguinte configura a resposta a um pedido negado:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Consulte a documentação de segurança IP dinâmica no IIS para outros valores suportados.

## <a name="operations-for-configuring-service-certificates"></a>Operações como configurar certificados de serviço
Este tópico é apenas para referência. Siga os passos de configuração descritos em:

* Configurar o certificado SSL
* Configurar certificados de cliente

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Execute:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Para personalizar:

* -n com o URL do serviço. Carateres universais ("CN = * .cloudapp .net") e os nomes alternativos ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") são suportados.
* -e com a data de expiração do certificado criar uma palavra-passe forte e especificá-lo quando lhe for pedido.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Criar o ficheiro PFX de certificado SSL autoassinado
Execute:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Introduza a palavra-passe e, em seguida, exportar o certificado com estas opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades expandidas

## <a name="export-ssl-certificate-from-certificate-store"></a>Exportar o certificado SSL do arquivo de certificados
* Encontrar o certificado
* Clique em ações -> todas as tarefas -> exportação...
* Exportar o certificado para um. Ficheiro PFX com estas opções:
  * Sim, exportar a chave privada
  * Incluir todos os certificados no caminho de certificação se possível * exportar propriedades expandidas tudo

## <a name="upload-ssl-certificate-to-cloud-service"></a>Carregar o certificado SSL para o serviço em nuvem
Carregamento de certificados com o existente ou gerado. Ficheiro PFX com o par de chaves do SSL:

* Introduza a palavra-passe a proteger as informações da chave privadas

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Atualizar o certificado SSL no ficheiro de configuração de serviço
Atualize o valor do thumbprint da definição seguinte no ficheiro de configuração do serviço com o thumbprint do certificado carregado para o serviço em nuvem:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Autoridade de certificação de SSL de importação
Siga estes passos em todos os conta/máquina que irão comunicar com o serviço:

* Faça duplo clique na. Ficheiro CER no Explorador do Windows
* Na caixa de diálogo certificado, clique em instalar certificado...
* Importar o certificado para o arquivo de autoridades de certificação de raiz fidedigna

## <a name="turn-off-client-certificate-based-authentication"></a>Desativar a autenticação baseada em certificados de cliente
Apenas a autenticação de baseada em certificado cliente é suportada e respetiva desativação permitirá para acesso público para os pontos finais de serviço, a menos que sejam de outros mecanismos no local (por exemplo, Microsoft Azure Virtual Network).

Altere estas definições para false no ficheiro de configuração do serviço para desativar a funcionalidade:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Em seguida, copie o mesmo thumbprint que o certificado SSL na definição de certificado da AC:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Criar uma autoridade de certificação autoassinado
Execute os seguintes passos para criar um certificado autoassinado para atuar como uma autoridade de certificação:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Personalizá-lo

* -e com a data de expiração de certificação

## <a name="find-ca-public-key"></a>Localizar a chave pública de AC
Todos os certificados de cliente tem de ter foi emitidos por uma autoridade de certificação fidedigna pelo serviço. Encontre a chave pública para a autoridade de certificação que emitiu o cliente certificados que vão ser utilizados para autenticação para poder carregá-lo para o serviço em nuvem.

Se o ficheiro com a chave pública não estiver disponível, exporte-o do arquivo de certificados:

* Encontrar o certificado
  * Procurar um certificado de cliente emitido pela mesma autoridade de certificação
* Clique duas vezes o certificado.
* Selecione o separador de caminho de certificação na caixa de diálogo certificado.
* Faça duplo clique na entrada de AC no caminho.
* Anote as propriedades do certificado.
* Fechar o **certificado** caixa de diálogo.
* Encontrar o certificado
  * Pesquisa para a AC indicada acima.
* Clique em ações -> todas as tarefas -> exportação...
* Exportar o certificado para um. CER com estas opções:
  * **Não, não exportar a chave privada**
  * Inclua todos os certificados no caminho de certificação se possível.
  * Exporte todas as propriedades expandidas.

## <a name="upload-ca-certificate-to-cloud-service"></a>Carregar o certificado de AC para o serviço em nuvem
Carregamento de certificados com o existente ou gerado. Ficheiro CER com a chave pública de AC.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Certificado da AC de atualização no ficheiro de configuração de serviço
Atualize o valor do thumbprint da definição seguinte no ficheiro de configuração do serviço com o thumbprint do certificado carregado para o serviço em nuvem:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Atualize o valor da definição seguinte com o mesmo thumbprint:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Emitir certificados de cliente
Cada pessoa tem autorizada para aceder ao serviço deve ter um certificado de cliente emitido para seu uso exclusivo e deve escolher a sua própria palavra-passe segura para proteger a respetiva chave privada. 

Os seguintes passos tem de ser executados no mesmo computador em que o certificado de AC autoassinado foi gerado e armazenado:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personalizando:

* -n com um ID para o cliente que será autenticada com este certificado
* -e com a data de expiração do certificado
* MyID.pvk e MyID.cer com nomes de ficheiro exclusivo para este certificado de cliente

Este comando irá pedir uma palavra-passe ser criado e, em seguida, utilizado uma vez. Utilize uma palavra-passe segura.

## <a name="create-pfx-files-for-client-certificates"></a>Criar ficheiros PFX para o cliente certificados
Para cada certificado de cliente gerado, execute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizando:

    MyID.pvk and MyID.cer with the filename for the client certificate

Introduza a palavra-passe e, em seguida, exportar o certificado com estas opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades expandidas
* A pessoa a quem este certificado é emitido deve escolher a palavra-passe de exportação

## <a name="import-client-certificate"></a>Importar o certificado de cliente
Cada pessoa para quem foi emitido um certificado de cliente deve importar o par de chaves nas máquinas, será utilizado para comunicar com o serviço:

* Faça duplo clique na. Ficheiro PFX no Explorador do Windows
* Importar o certificado em pessoal armazenar com, pelo menos, esta opção:
  * Incluir todas as propriedades expandidas verificadas

## <a name="copy-client-certificate-thumbprints"></a>Copie os thumbprints de certificado de cliente
Cada pessoa para quem foi emitido um certificado de cliente tem de seguir estes passos para obter o thumbprint do respetivo certificado, o que será adicionado ao arquivo de configuração de serviço:

* Executar certmgr.exe
* Selecione o separador pessoa
* Clique duas vezes o certificado de cliente a ser utilizado para autenticação
* Na caixa de diálogo a certificado que se abre, selecione o separador de detalhes
* Certifique-se de que mostrar está exibindo todas
* Selecione o campo com o Thumbprint do nome na lista
* Copie o valor do impressão digital
  * Eliminar os caracteres de Unicode não visível na frente o dígito primeiro
  * Eliminar todos os espaços

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Configurar clientes de permitido no ficheiro de configuração do serviço
Atualize o valor da definição seguinte no ficheiro de configuração do serviço com uma lista separada por vírgulas de thumbprints dos certificados de cliente permitidos para aceder ao serviço:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Configurar a verificação de revogação de certificado de cliente
Não verifica se a configuração padrão com a autoridade de certificação para o estado de revogação de certificado de cliente. Para ativar as verificações, se a autoridade de certificação que emitiu o cliente certificados oferece suporte a essas verificações, altere a definição seguinte com um dos valores definidos na enumeração X509RevocationMode:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Criar o ficheiro PFX para certificados de encriptação autoassinado
Para um certificado de encriptação, execute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizando:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Introduza a palavra-passe e, em seguida, exportar o certificado com estas opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades expandidas
* Terá da palavra-passe ao carregar o certificado para o serviço em nuvem.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportar o certificado de encriptação do arquivo de certificados
* Encontrar o certificado
* Clique em ações -> todas as tarefas -> exportação...
* Exportar o certificado para um. Ficheiro PFX com estas opções: 
  * Sim, exportar a chave privada
  * Incluir todos os certificados no caminho de certificação se possível 
* Exportar todas as propriedades expandidas

## <a name="upload-encryption-certificate-to-cloud-service"></a>Carregar o certificado de encriptação para o serviço em nuvem
Carregamento de certificados com o existente ou gerado. Ficheiro PFX com o par de chaves de encriptação:

* Introduza a palavra-passe a proteger as informações da chave privadas

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Atualizar o certificado de encriptação no ficheiro de configuração de serviço
Atualize o valor do thumbprint das seguintes definições no ficheiro de configuração do serviço com o thumbprint do certificado carregado para o serviço em nuvem:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Operações de certificado comuns
* Configurar o certificado SSL
* Configurar certificados de cliente

## <a name="find-certificate"></a>Encontrar o certificado
Siga estes passos.

1. Run mmc.exe.
2. Ficheiro -> Adicionar/Remover Snap-in...
3. Selecione **certificados**.
4. Clique em **Adicionar**.
5. Escolha a localização do arquivo de certificados.
6. Clique em **Concluir**.
7. Clique em **OK**.
8. Expanda **certificados**.
9. Expanda o nó do arquivo de certificados.
10. Expanda o nó filho do certificado.
11. Selecione um certificado na lista.

## <a name="export-certificate"></a>Exportar o certificado
Na **Assistente de exportação de certificado**:

1. Clique em **Seguinte**.
2. Selecione **Sim**, em seguida, **exportar a chave privada**.
3. Clique em **Seguinte**.
4. Selecione o formato de ficheiro de saída desejada.
5. Verifique as opções pretendidas.
6. Verifique **palavra-passe**.
7. Introduza uma palavra-passe forte e confirme-lo.
8. Clique em **Seguinte**.
9. Escreva ou procure um nome de ficheiro onde pretende armazenar o certificado (utilizar um. Extensão PFX).
10. Clique em **Seguinte**.
11. Clique em **Concluir**.
12. Clique em **OK**.

## <a name="import-certificate"></a>Importar certificado
No Assistente para importar certificado:

1. Selecione a localização do arquivo.
   
   * Selecione **utilizador atual** se apenas processos em execução no utilizador atual terão acesso ao serviço
   * Selecione **computador Local** se outros processos neste computador terão acesso ao serviço
2. Clique em **Seguinte**.
3. Se importar de um ficheiro, confirme o caminho do ficheiro.
4. Se importar um. Ficheiro PFX:
   1. Introduza a palavra-passe proteger a chave privada
   2. Selecione as opções de importação
5. Selecionar certificados de "Place" no seguinte arquivo
6. Clique em **Browse** (Procurar).
7. Selecione o arquivo desejado.
8. Clique em **Concluir**.
   
   * Se o arquivo de autoridade de certificação de raiz fidedigna foi escolhido, clique em **Sim**.
9. Clique em **OK** em todas as janelas de caixa de diálogo.

## <a name="upload-certificate"></a>Carregar certificado
No [portal do Azure](https://portal.azure.com/)

1. Selecione **serviços Cloud**.
2. Selecione o serviço em nuvem.
3. No menu superior, clique em **certificados**.
4. Na barra inferior, clique em **carregar**.
5. Selecione o ficheiro de certificado.
6. Se se trata de um. PFX do ficheiro, introduza a palavra-passe para a chave privada.
7. Depois de concluído, copie o thumbprint do certificado da nova entrada na lista.

## <a name="other-security-considerations"></a>Outras considerações de segurança
As definições de SSL descritas neste documento para encriptar a comunicação entre o serviço e seus clientes quando é utilizado o ponto final HTTPS. Isso é importante desde as credenciais de acesso de base de dados e potencialmente outras informações confidenciais contidas na comunicação. No entanto, observe que o serviço de persiste estado interno, incluindo as credenciais, no respetivas tabelas internas na base de dados SQL do Microsoft Azure fornecidos para armazenamento de metadados na sua subscrição do Microsoft Azure. Esse banco de dados foi definido como parte da definição seguinte no ficheiro de configuração de serviço (. Ficheiro CSCFG): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

As credenciais armazenadas nesta base de dados são encriptadas. No entanto, como melhor prática, certifique-se de que as funções web e de trabalho das suas implementações de serviço são mantidas atualizadas e seguros como os têm acesso para a base de dados de metadados e o certificado utilizado para encriptação e desencriptação de credenciais armazenadas. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

