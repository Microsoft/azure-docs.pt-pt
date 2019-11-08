---
title: Configuração de segurança de divisão/mesclagem
description: Configure certificados do X409 para criptografia com o serviço de divisão/mesclagem para escala elástica.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: a916645f153f73a98e7fc5d4046bdf557e8acf2b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823518"
---
# <a name="split-merge-security-configuration"></a>Configuração de segurança de divisão/mesclagem

Para usar o serviço de divisão/mesclagem, você deve configurar corretamente a segurança. O serviço faz parte do recurso de escala elástica do Banco de Dados SQL do Microsoft Azure. Para obter mais informações, consulte [tutorial de divisão de escala elástica e serviço de mesclagem](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Configurando certificados

Os certificados são configurados de duas maneiras. 

1. [Para configurar o certificado SSL](#to-configure-the-ssl-certificate)
2. [Para configurar certificados de cliente](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Para obter certificados

Os certificados podem ser obtidos de CAs (autoridades de certificação) públicas ou do [serviço de certificado do Windows](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Esses são os métodos preferenciais para obter certificados.

Se essas opções não estiverem disponíveis, você poderá gerar **certificados autoassinados**.

## <a name="tools-to-generate-certificates"></a>Ferramentas para gerar certificados

* [MakeCert. exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [Pvk2pfx. exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Para executar as ferramentas

* De um Prompt de Comando do Desenvolvedor para Visual estúdios, consulte [prompt de comando do Visual Studio](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    Se estiver instalado, vá para:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Obtenha o WDK de [Windows 8.1: Baixe kits e ferramentas](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Para configurar o certificado SSL

Um certificado SSL é necessário para criptografar a comunicação e autenticar o servidor. Escolha o mais aplicável aos três cenários abaixo e execute todas as suas etapas:

### <a name="create-a-new-self-signed-certificate"></a>Criar um novo certificado autoassinado

1. [Criar um certificado autoassinado](#create-a-self-signed-certificate)
2. [Criar arquivo PFX para certificado SSL autoassinado](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Carregar o certificado SSL para o serviço de nuvem](#upload-ssl-certificate-to-cloud-service)
4. [Atualizar o certificado SSL no arquivo de configuração de serviço](#update-ssl-certificate-in-service-configuration-file)
5. [Importar autoridade de certificação SSL](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Para usar um certificado existente do repositório de certificados
1. [Exportar certificado SSL do repositório de certificados](#export-ssl-certificate-from-certificate-store)
2. [Carregar o certificado SSL para o serviço de nuvem](#upload-ssl-certificate-to-cloud-service)
3. [Atualizar o certificado SSL no arquivo de configuração de serviço](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Para usar um certificado existente em um arquivo PFX
1. [Carregar o certificado SSL para o serviço de nuvem](#upload-ssl-certificate-to-cloud-service)
2. [Atualizar o certificado SSL no arquivo de configuração de serviço](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Para configurar certificados de cliente
Certificados de cliente são necessários para autenticar solicitações para o serviço. Escolha o mais aplicável aos três cenários abaixo e execute todas as suas etapas:

### <a name="turn-off-client-certificates"></a>Desativar certificados do cliente
1. [Desligar a autenticação baseada em certificado do cliente](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Emitir novos certificados de cliente autoassinados
1. [Criar uma autoridade de certificação autoassinada](#create-a-self-signed-certification-authority)
2. [Carregar certificado de autoridade de certificação no serviço de nuvem](#upload-ca-certificate-to-cloud-service)
3. [Atualizar certificado de autoridade de certificação no arquivo de configuração de serviço](#update-ca-certificate-in-service-configuration-file)
4. [Emitir certificados de cliente](#issue-client-certificates)
5. [Criar arquivos PFX para certificados de cliente](#create-pfx-files-for-client-certificates)
6. [Importar certificado do cliente](#import-client-certificate)
7. [Copiar impressões digitais de certificado de cliente](#copy-client-certificate-thumbprints)
8. [Configurar clientes permitidos no arquivo de configuração de serviço](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Usar certificados de cliente existentes
1. [Localizar chave pública de CA](#find-ca-public-key)
2. [Carregar certificado de autoridade de certificação no serviço de nuvem](#upload-ca-certificate-to-cloud-service)
3. [Atualizar certificado de autoridade de certificação no arquivo de configuração de serviço](#update-ca-certificate-in-service-configuration-file)
4. [Copiar impressões digitais de certificado de cliente](#copy-client-certificate-thumbprints)
5. [Configurar clientes permitidos no arquivo de configuração de serviço](#configure-allowed-clients-in-the-service-configuration-file)
6. [Configurar verificação de revogação de certificado de cliente](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Endereços IP permitidos
O acesso aos pontos de extremidade de serviço pode ser restrito a intervalos específicos de endereços IP.

## <a name="to-configure-encryption-for-the-store"></a>Para configurar a criptografia para a loja
Um certificado é necessário para criptografar as credenciais armazenadas no repositório de metadados. Escolha o mais aplicável aos três cenários abaixo e execute todas as suas etapas:

### <a name="use-a-new-self-signed-certificate"></a>Usar um novo certificado autoassinado
1. [Criar um certificado autoassinado](#create-a-self-signed-certificate)
2. [Criar arquivo PFX para certificado de criptografia autoassinado](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Carregar o certificado de criptografia no serviço de nuvem](#upload-encryption-certificate-to-cloud-service)
4. [Atualizar certificado de criptografia no arquivo de configuração de serviço](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Usar um certificado existente do repositório de certificados
1. [Exportar certificado de criptografia do repositório de certificados](#export-encryption-certificate-from-certificate-store)
2. [Carregar o certificado de criptografia no serviço de nuvem](#upload-encryption-certificate-to-cloud-service)
3. [Atualizar certificado de criptografia no arquivo de configuração de serviço](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Usar um certificado existente em um arquivo PFX
1. [Carregar o certificado de criptografia no serviço de nuvem](#upload-encryption-certificate-to-cloud-service)
2. [Atualizar certificado de criptografia no arquivo de configuração de serviço](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>A configuração padrão
A configuração padrão nega todo o acesso ao ponto de extremidade HTTP. Essa é a configuração recomendada, pois as solicitações para esses pontos de extremidade podem transportar informações confidenciais, como credenciais de banco de dados.
A configuração padrão permite todo o acesso ao ponto de extremidade HTTPS. Essa configuração pode ser mais restrita.

### <a name="changing-the-configuration"></a>Alterando a configuração
O grupo de regras de controle de acesso que se aplicam ao e ao ponto de extremidade são configurados na seção **\<EndpointAcls >** no **arquivo de configuração de serviço**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

As regras em um grupo de controle de acesso são configuradas em uma seção \<AccessControl Name = "" > do arquivo de configuração de serviço. 

O formato é explicado na documentação das listas de controle de acesso à rede.
Por exemplo, para permitir que somente IPs no intervalo 100.100.0.0 a 100.100.255.255 acessem o ponto de extremidade HTTPS, as regras teriam a seguinte aparência:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Prevenção de negação de serviço
Há dois mecanismos diferentes com suporte para detectar e impedir ataques de negação de serviço:

* Restringir o número de solicitações simultâneas por host remoto (desativado por padrão)
* Restringir a taxa de acesso por host remoto (ativado por padrão)

Elas se baseiam nos recursos documentados em segurança de IP dinâmico no IIS. Ao alterar essa configuração, lembre-se dos seguintes fatores:

* O comportamento de proxies e dispositivos de conversão de endereços de rede nas informações do host remoto
* Cada solicitação para qualquer recurso na função Web é considerada (por exemplo, carregamento de scripts, imagens etc.)

## <a name="restricting-number-of-concurrent-accesses"></a>Restringindo o número de acessos simultâneos
As configurações que configuram esse comportamento são:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Altere DynamicIpRestrictionDenyByConcurrentRequests para true para habilitar essa proteção.

## <a name="restricting-rate-of-access"></a>Restringindo a taxa de acesso
As configurações que configuram esse comportamento são:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Configurando a resposta para uma solicitação negada
A configuração a seguir configura a resposta a uma solicitação negada:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Consulte a documentação da segurança de IP dinâmico no IIS para obter outros valores com suporte.

## <a name="operations-for-configuring-service-certificates"></a>Operações para configurar certificados de serviço
Este tópico é apenas para referência. Siga as etapas de configuração descritas em:

* Configurar o certificado SSL
* Configurar certificados de cliente

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Executados

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Para personalizar:

* -n com a URL do serviço. Há suporte para caracteres curinga ("CN = *. cloudapp. net") e nomes alternativos ("CN = myservice1. cloudapp. net, CN = myservice2. cloudapp. net").
* -e com a data de expiração do certificado, crie uma senha forte e especifique-a quando solicitado.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Criar arquivo PFX para certificado SSL autoassinado
Executados

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Insira a senha e, em seguida, exporte o certificado com estas opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades estendidas

## <a name="export-ssl-certificate-from-certificate-store"></a>Exportar certificado SSL do repositório de certificados
* Localizar certificado
* Clique em ações-> todas as tarefas-> Exportar...
* Exporte o certificado para um. Arquivo PFX com estas opções:
  * Sim, exportar a chave privada
  * Incluir todos os certificados no caminho de certificação, se possível * exportar todas as propriedades estendidas

## <a name="upload-ssl-certificate-to-cloud-service"></a>Carregar o certificado SSL para o serviço de nuvem
Carregue o certificado com o existente ou gerado. Arquivo PFX com o par de chaves SSL:

* Insira a senha que protege as informações de chave privada

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Atualizar o certificado SSL no arquivo de configuração de serviço
Atualize o valor de impressão digital da seguinte configuração no arquivo de configuração de serviço com a impressão digital do certificado carregado para o serviço de nuvem:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Importar autoridade de certificação SSL
Siga estas etapas em todas as contas/computadores que se comunicarão com o serviço:

* Clique duas vezes em. Arquivo CER no Windows Explorer
* Na caixa de diálogo certificado, clique em instalar certificado...
* Importar o certificado para o repositório de autoridades de certificação raiz confiáveis

## <a name="turn-off-client-certificate-based-authentication"></a>Desligar a autenticação baseada em certificado do cliente
Somente a autenticação baseada em certificado do cliente tem suporte e sua desabilitação permitirá o acesso público aos pontos de extremidade de serviço, a menos que outros mecanismos estejam em vigor (por exemplo, Rede Virtual do Microsoft Azure).

Altere essas configurações para falso no arquivo de configuração de serviço para desativar o recurso:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Em seguida, copie a mesma impressão digital como o certificado SSL na configuração do certificado de autoridade de certificação:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Criar uma autoridade de certificação autoassinada
Execute as seguintes etapas para criar um certificado autoassinado para atuar como uma autoridade de certificação:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Para personalizá-lo

* -e com a data de validade da certificação

## <a name="find-ca-public-key"></a>Localizar chave pública de CA
Todos os certificados de cliente devem ter sido emitidos por uma autoridade de certificação confiável pelo serviço. Localize a chave pública para a autoridade de certificação que emitiu os certificados de cliente que serão usados para autenticação a fim de carregá-los no serviço de nuvem.

Se o arquivo com a chave pública não estiver disponível, exporte-o do repositório de certificados:

* Localizar certificado
  * Pesquisar um certificado de cliente emitido pela mesma autoridade de certificação
* Clique duas vezes no certificado.
* Selecione a guia caminho de certificação na caixa de diálogo certificado.
* Clique duas vezes na entrada de autoridade de certificação no caminho.
* Faça anotações das propriedades do certificado.
* Feche a caixa de diálogo **certificado** .
* Localizar certificado
  * Pesquise pela AC anotada acima.
* Clique em ações-> todas as tarefas-> Exportar...
* Exporte o certificado para um. CER com estas opções:
  * **Não, não exportar a chave privada**
  * Inclua todos os certificados no caminho de certificação, se possível.
  * Exportar todas as propriedades estendidas.

## <a name="upload-ca-certificate-to-cloud-service"></a>Carregar certificado de autoridade de certificação no serviço de nuvem
Carregue o certificado com o existente ou gerado. Arquivo CER com a chave pública de CA.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Atualizar certificado de autoridade de certificação no arquivo de configuração de serviço
Atualize o valor de impressão digital da seguinte configuração no arquivo de configuração de serviço com a impressão digital do certificado carregado para o serviço de nuvem:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Atualize o valor da seguinte configuração com a mesma impressão digital:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Emitir certificados de cliente
Cada indivíduo autorizado a acessar o serviço deve ter um certificado de cliente emitido para seu uso exclusivo e deve escolher sua própria senha forte para proteger sua chave privada. 

As etapas a seguir devem ser executadas no mesmo computador em que o certificado de autoridade de certificação autoassinado foi gerado e armazenado:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personalizando

* -n com uma ID para o cliente que será autenticado com esse certificado
* -e com a data de expiração do certificado
* MyID. pvk e MyID. cer com nomes de e exclusivos para este certificado de cliente

Esse comando solicitará uma senha a ser criada e usada uma vez. Use uma senha forte.

## <a name="create-pfx-files-for-client-certificates"></a>Criar arquivos PFX para certificados de cliente
Para cada certificado de cliente gerado, execute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizando

    MyID.pvk and MyID.cer with the filename for the client certificate

Insira a senha e, em seguida, exporte o certificado com estas opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades estendidas
* O indivíduo para quem este certificado está sendo emitido deve escolher a senha de exportação

## <a name="import-client-certificate"></a>Importar certificado do cliente
Cada indivíduo para o qual um certificado de cliente foi emitido deve importar o par de chaves nos computadores que eles usarão para se comunicar com o serviço:

* Clique duas vezes em. Arquivo PFX no Windows Explorer
* Importe o certificado para o repositório pessoal com pelo menos esta opção:
  * Incluir todas as propriedades estendidas marcadas

## <a name="copy-client-certificate-thumbprints"></a>Copiar impressões digitais de certificado de cliente
Cada indivíduo para o qual um certificado de cliente foi emitido deve seguir estas etapas para obter a impressão digital de seu certificado, que será adicionado ao arquivo de configuração de serviço:

* Executar certmgr. exe
* Selecione a guia pessoal
* Clique duas vezes no certificado do cliente a ser usado para autenticação
* Na caixa de diálogo certificado que é aberta, selecione a guia detalhes
* Verifique se a exibição mostrar está sendo exibida
* Selecione o campo chamado impressão digital na lista
* Copiar o valor da impressão digital
  * Excluir caracteres Unicode não visíveis na frente do primeiro dígito
  * Excluir todos os espaços

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Configurar clientes permitidos no arquivo de configuração de serviço
Atualize o valor da configuração a seguir no arquivo de configuração de serviço com uma lista separada por vírgulas das impressões digitais dos certificados de cliente com permissão de acesso ao serviço:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Configurar verificação de revogação de certificado de cliente
A configuração padrão não verifica com a autoridade de certificação para o status de revogação do certificado de cliente. Para ativar as verificações, se a autoridade de certificação que emitiu os certificados de cliente oferecer suporte a tais verificações, altere a seguinte configuração com um dos valores definidos na Enumeração X509RevocationMode:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Criar arquivo PFX para certificados de criptografia autoassinados
Para um certificado de criptografia, execute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizando

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Insira a senha e, em seguida, exporte o certificado com estas opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades estendidas
* Você precisará da senha ao carregar o certificado para o serviço de nuvem.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportar certificado de criptografia do repositório de certificados
* Localizar certificado
* Clique em ações-> todas as tarefas-> Exportar...
* Exporte o certificado para um. Arquivo PFX com estas opções: 
  * Sim, exportar a chave privada
  * Incluir todos os certificados no caminho de certificação, se possível 
* Exportar todas as propriedades estendidas

## <a name="upload-encryption-certificate-to-cloud-service"></a>Carregar o certificado de criptografia no serviço de nuvem
Carregue o certificado com o existente ou gerado. Arquivo PFX com o par de chaves de criptografia:

* Insira a senha que protege as informações de chave privada

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Atualizar certificado de criptografia no arquivo de configuração de serviço
Atualize o valor de impressão digital das seguintes configurações no arquivo de configuração de serviço com a impressão digital do certificado carregado para o serviço de nuvem:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Operações comuns de certificado
* Configurar o certificado SSL
* Configurar certificados de cliente

## <a name="find-certificate"></a>Localizar certificado
Siga estes passos.

1. Execute MMC. exe.
2. Arquivo-> Adicionar/remover snap-in...
3. Selecione **certificados**.
4. Clique em **Adicionar**.
5. Escolha o local do repositório de certificados.
6. Clique em **Concluir**.
7. Clique em **OK**.
8. Expanda **certificados**.
9. Expanda o nó repositório de certificados.
10. Expanda o nó filho do certificado.
11. Selecione um certificado na lista.

## <a name="export-certificate"></a>Exportar o certificado
No **Assistente para exportação de certificados**:

1. Clique em **Seguinte**.
2. Selecione **Sim**e **exportar a chave privada**.
3. Clique em **Seguinte**.
4. Selecione o formato de arquivo de saída desejado.
5. Verifique as opções desejadas.
6. Verifique a **senha**.
7. Insira uma senha forte e confirme-a.
8. Clique em **Seguinte**.
9. Digite ou procure um nome de arquivo onde armazenar o certificado (use um. Extensão PFX).
10. Clique em **Seguinte**.
11. Clique em **Concluir**.
12. Clique em **OK**.

## <a name="import-certificate"></a>Importar certificado
No Assistente para importação de certificados:

1. Selecione o local do repositório.
   
   * Selecione o **usuário atual** se apenas os processos em execução no usuário atual acessarão o serviço
   * Selecione **computador local** se outros processos neste computador acessarão o serviço
2. Clique em **Seguinte**.
3. Se estiver importando de um arquivo, confirme o caminho do arquivo.
4. Se estiver importando um. Arquivo PFX:
   1. Insira a senha que protege a chave privada
   2. Selecionar opções de importação
5. Selecione os certificados "local" no repositório a seguir
6. Clique em **Browse** (Procurar).
7. Selecione o repositório desejado.
8. Clique em **Concluir**.
   
   * Se o repositório da autoridade de certificação raiz confiável tiver sido escolhido, clique em **Sim**.
9. Clique em **OK** em todas as janelas de diálogo.

## <a name="upload-certificate"></a>Carregar certificado
No [portal do Azure](https://portal.azure.com/)

1. Selecione **serviços de nuvem**.
2. Selecione o serviço de nuvem.
3. No menu superior, clique em **certificados**.
4. Na barra inferior, clique em **carregar**.
5. Selecione o arquivo de certificado.
6. Se for um. Arquivo PFX, insira a senha para a chave privada.
7. Depois de concluído, copie a impressão digital do certificado da nova entrada na lista.

## <a name="other-security-considerations"></a>Outras considerações de segurança
As configurações de SSL descritas neste documento criptografam a comunicação entre o serviço e seus clientes quando o ponto de extremidade HTTPS é usado. Isso é importante, pois as credenciais para acesso ao banco de dados e potencialmente outras informações confidenciais estão contidas na comunicação. No entanto, observe que o serviço persiste o status interno, incluindo as credenciais, em suas tabelas internas no banco de dados do Microsoft Azure SQL que você forneceu para armazenamento de metadados na sua assinatura do Microsoft Azure. Esse banco de dados foi definido como parte da configuração a seguir no arquivo de configuração de serviço (. Arquivo CSCFG): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

As credenciais armazenadas neste banco de dados são criptografadas. No entanto, como prática recomendada, certifique-se de que as funções Web e de trabalho de suas implantações de serviço sejam mantidas atualizadas e protegidas, pois ambas têm acesso ao banco de dados de metadados e ao certificado usado para criptografia e descriptografia de credenciais armazenadas. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

