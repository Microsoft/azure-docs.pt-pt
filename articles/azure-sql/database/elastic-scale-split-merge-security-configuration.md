---
title: Configuração de segurança de fusão split
description: Configurar certificados x409 para encriptação com o serviço de divisão/fusão para escala elástica.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: e1fdf219d09148d47759652e97797b569e265fa4
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041911"
---
# <a name="split-merge-security-configuration"></a>Configuração de segurança de fusão split
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Para utilizar o serviço Split/Merge, deve configurar corretamente a segurança. O serviço faz parte da funcionalidade de Escala Elástica da Base de Dados Azure SQL. Para mais informações, consulte elastic [scale split e merge Service Tutorial](elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Configuração de certificados

Os certificados são configurados de duas formas. 

1. [Para configurar o certificado TLS/SSL](#to-configure-the-tlsssl-certificate)
2. [Para configurar certificados de cliente](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Para obter certificados

Os certificados podem ser obtidos a partir de Autoridades públicas de Certificados (AA) ou do Serviço de [Certificados do Windows](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Estes são os métodos preferidos para obter certificados.

Se essas opções não estiverem disponíveis, pode gerar **certificados auto-assinados.**

## <a name="tools-to-generate-certificates"></a>Ferramentas para gerar certificados

* [makecert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Para executar as ferramentas

* A partir de um pedido de comando de desenvolvedor para estúdios visuais, consulte [Visual Studio Command Prompt](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    Se instalado, vá a:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Obtenha o WDK a partir do [Windows 8.1: Descarregue kits e ferramentas](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-tlsssl-certificate"></a>Para configurar o certificado TLS/SSL

É necessário um certificado TLS/SSL para encriptar a comunicação e autenticar o servidor. Escolha o mais aplicável dos três cenários abaixo e execute todos os seus passos:

### <a name="create-a-new-self-signed-certificate"></a>Criar um novo certificado auto-assinado

1. [Criar um Certificado Auto-Assinado](#create-a-self-signed-certificate)
2. [Criar ficheiro PFX para certificado TLS/SSL auto-assinado](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Carregue o Certificado TLS/SSL para o serviço cloud](#upload-tlsssl-certificate-to-cloud-service)
4. [Certificado TLS/SSL atualizado no ficheiro de configuração do serviço](#update-tlsssl-certificate-in-service-configuration-file)
5. [Autoridade de Certificação TLS/SSL](#import-tlsssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Utilizar um certificado existente a partir da loja de certificados
1. [Certificado TLS/SSL de exportação da Loja de Certificados](#export-tlsssl-certificate-from-certificate-store)
2. [Carregue o Certificado TLS/SSL para o serviço cloud](#upload-tlsssl-certificate-to-cloud-service)
3. [Certificado TLS/SSL atualizado no ficheiro de configuração do serviço](#update-tlsssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Para utilizar um certificado existente num ficheiro PFX
1. [Carregue o Certificado TLS/SSL para o serviço cloud](#upload-tlsssl-certificate-to-cloud-service)
2. [Certificado TLS/SSL atualizado no ficheiro de configuração do serviço](#update-tlsssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Para configurar os certificados de cliente
Os certificados de cliente são necessários para autenticar pedidos ao serviço. Escolha o mais aplicável dos três cenários abaixo e execute todos os seus passos:

### <a name="turn-off-client-certificates"></a>Desligue os certificados de cliente
1. [Desativar a autenticação baseada em certificado de cliente](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Emitir novos certificados de cliente auto-assinados
1. [Criar uma Autoridade de Certificação Auto-Assinada](#create-a-self-signed-certification-authority)
2. [Enviar certificado CA para serviço de nuvem](#upload-ca-certificate-to-cloud-service)
3. [Atualizar certificado CA no ficheiro de configuração do serviço](#update-ca-certificate-in-service-configuration-file)
4. [Emitir Certificados de Cliente](#issue-client-certificates)
5. [Criar ficheiros PFX para Certificados de Cliente](#create-pfx-files-for-client-certificates)
6. [Certificado de Cliente de Importação](#import-client-certificate)
7. [Cópia De Impressão Digital do Certificado do Cliente](#copy-client-certificate-thumbprints)
8. [Configurar clientes permitidos no ficheiro de configuração do serviço](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Utilize certificados de cliente existentes
1. [Encontre a chave pública ca](#find-ca-public-key)
2. [Enviar certificado CA para serviço de nuvem](#upload-ca-certificate-to-cloud-service)
3. [Atualizar certificado CA no ficheiro de configuração do serviço](#update-ca-certificate-in-service-configuration-file)
4. [Cópia De Impressão Digital do Certificado do Cliente](#copy-client-certificate-thumbprints)
5. [Configurar clientes permitidos no ficheiro de configuração do serviço](#configure-allowed-clients-in-the-service-configuration-file)
6. [Configure Verificação de Revogação do Certificado de Cliente](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Endereços IP permitidos
O acesso aos pontos finais do serviço pode ser restrito a gamas específicas de endereços IP.

## <a name="to-configure-encryption-for-the-store"></a>Para configurar a encriptação para a loja
É necessário um certificado para encriptar as credenciais armazenadas na loja de metadados. Escolha o mais aplicável dos três cenários abaixo e execute todos os seus passos:

### <a name="use-a-new-self-signed-certificate"></a>Use um novo certificado auto-assinado
1. [Criar um Certificado Auto-Assinado](#create-a-self-signed-certificate)
2. [Criar ficheiro PFX para certificado de encriptação auto-assinado](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Enviar certificado de encriptação para o serviço cloud](#upload-encryption-certificate-to-cloud-service)
4. [Certificado de encriptação de atualização no ficheiro de configuração do serviço](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Utilize um certificado existente na loja de certificados
1. [Certificado de encriptação de exportação da loja de certificados](#export-encryption-certificate-from-certificate-store)
2. [Enviar certificado de encriptação para o serviço cloud](#upload-encryption-certificate-to-cloud-service)
3. [Certificado de encriptação de atualização no ficheiro de configuração do serviço](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Use um certificado existente num ficheiro PFX
1. [Enviar certificado de encriptação para o serviço cloud](#upload-encryption-certificate-to-cloud-service)
2. [Certificado de encriptação de atualização no ficheiro de configuração do serviço](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>A configuração padrão
A configuração predefinida nega todo o acesso ao ponto final http. Esta é a definição recomendada, uma vez que os pedidos a estes pontos finais podem transportar informações sensíveis, como credenciais de base de dados.
A configuração predefinida permite todo o acesso ao ponto final HTTPS. Esta definição pode ser restringida ainda mais.

### <a name="changing-the-configuration"></a>Alterar a Configuração
O grupo de regras de controlo de acesso aplicáveis e endpoint estão configurados na secção do ficheiro de **\<EndpointAcls>** **configuração**do serviço .

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

As regras de um grupo de controlo de acesso estão configuradas numa \<AccessControl name=""> secção do ficheiro de configuração do serviço. 

O formato é explicado na documentação das Listas de Controlo de Acesso de Rede.
Por exemplo, para permitir que apenas os IPs na gama 100.100.0.0 a 100.100.255.255 para aceder ao ponto final https, as regras seriam assim:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Negação da prevenção do serviço
Existem dois mecanismos diferentes suportados para detetar e prevenir ataques de Negação de Serviço:

* Restringir o número de pedidos simultâneos por hospedeiro remoto (desligado por defeito)
* Restringir a taxa de acesso por hospedeiro remoto (por defeito)

Estes baseiam-se nas funcionalidades mais documentadas na Dynamic IP Security no IIS. Ao alterar esta configuração, tenha cuidado com os seguintes fatores:

* O comportamento dos proxies e dispositivos de tradução de endereços de rede através da informação remota do anfitrião
* Cada pedido a qualquer recurso na função web é considerado (por exemplo, carregar scripts, imagens, etc)

## <a name="restricting-number-of-concurrent-accesses"></a>Restringir o número de acessos simultâneos
As configurações que configuram este comportamento são:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Alterar DynamicIpRestrictionDenyByConcurrentRequests true to enable this protection.

## <a name="restricting-rate-of-access"></a>Taxa de acesso restrito
As configurações que configuram este comportamento são:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Configurar a resposta a um pedido negado
A seguinte definição configura a resposta a um pedido negado:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Consulte a documentação para A Segurança DINÂMICA do IP no IIS para outros valores suportados.

## <a name="operations-for-configuring-service-certificates"></a>Operações para configurar certificados de serviço
Este tema é apenas para referência. Siga os passos de configuração descritos em:

* Configure o certificado TLS/SSL
* Configurar certificados de cliente

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Executar:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Para personalizar:

* -n com a URL de serviço. Wildcards ("CN=*.cloudapp.net") e nomes alternativos ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") são suportados.
* -e com a data de validade do certificado Crie uma senha forte e especifique-a quando solicitada.

## <a name="create-pfx-file-for-self-signed-tlsssl-certificate"></a>Criar ficheiro PFX para certificado TLS/SSL auto-assinado
Executar:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Introduza a palavra-passe e, em seguida, exporte certificado com estas opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades estendidas

## <a name="export-tlsssl-certificate-from-certificate-store"></a>Certificado TLS/SSL de exportação de certificados
* Encontrar certificado
* Clique em Ações -> Todas as tarefas -> Exportação...
* Certificado de exportação em . Ficheiro PFX com estas opções:
  * Sim, exportar a chave privada
  * Incluir todos os certificados na via de certificação, se possível *Exportar todas as propriedades estendidas

## <a name="upload-tlsssl-certificate-to-cloud-service"></a>Carregue o certificado TLS/SSL para o serviço na nuvem
Faça upload do certificado com o existente ou gerado . Ficheiro PFX com o par de chaves TLS:

* Introduza a palavra-passe protegendo as informações de chave privadas

## <a name="update-tlsssl-certificate-in-service-configuration-file"></a>Atualizar certificado TLS/SSL no ficheiro de configuração do serviço
Atualize o valor de impressão digital da seguinte definição no ficheiro de configuração do serviço com a impressão digital do certificado enviado para o serviço em nuvem:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-tlsssl-certification-authority"></a>Autoridade de certificação TLS/SSL
Siga estes passos em todas as contas/máquinas que comunicarão com o serviço:

* Clique duas vezes no . Arquivo CER no Windows Explorer
* No diálogo do Certificado, clique em Certificado de Instalação...
* Certificado de importação na loja de autoridades de certificação de raiz fidedigna

## <a name="turn-off-client-certificate-based-authentication"></a>Desligue a autenticação baseada em certificado de cliente
Apenas a autenticação baseada em certificados de cliente é suportada e incapacitante permitirá o acesso público aos pontos finais do serviço, a menos que outros mecanismos estejam em vigor (por exemplo, Rede Virtual Microsoft Azure).

Altere estas definições para falsas no ficheiro de configuração do serviço para desativar a funcionalidade:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Em seguida, copie a mesma impressão digital que o certificado TLS/SSL na definição do certificado CA:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Criar uma autoridade de certificação auto-assinada
Executar os seguintes passos para criar um certificado auto-assinado para atuar como autoridade de certificação:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Para personalizá-lo

* -e com a data de validade da certificação

## <a name="find-ca-public-key"></a>Encontre a chave pública ca
Todos os certificados de cliente devem ter sido emitidos por uma Autoridade de Certificação confiada pelo serviço. Encontre a chave pública da Autoridade de Certificação que emitiu os certificados de cliente que vão ser utilizados para autenticação de forma a carregá-lo para o serviço de cloud.

Se o ficheiro com a chave pública não estiver disponível, exporte-o do certificado:

* Encontrar certificado
  * Procurar um certificado de cliente emitido pela mesma Autoridade de Certificação
* Clique duas vezes no certificado.
* Selecione o separador Caminho de Certificação no diálogo do Certificado.
* Clique duas vezes na entrada ca no caminho.
* Tome notas das propriedades do certificado.
* Feche o diálogo do **Certificado.**
* Encontrar certificado
  * Procura pela AC acima referida.
* Clique em Ações -> Todas as tarefas -> Exportação...
* Certificado de exportação em . CER com estas opções:
  * **Não, não exporte a chave privada**
  * Inclua todos os certificados no caminho de certificação, se possível.
  * Exportar todas as propriedades estendidas.

## <a name="upload-ca-certificate-to-cloud-service"></a>Carregue o certificado CA para o serviço de nuvem
Faça upload do certificado com o existente ou gerado . Arquivo cer com a chave pública da AC.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Atualizar certificado CA no ficheiro de configuração do serviço
Atualize o valor de impressão digital da seguinte definição no ficheiro de configuração do serviço com a impressão digital do certificado enviado para o serviço em nuvem:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Atualize o valor da seguinte definição com a mesma impressão digital:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Emitir certificados de cliente
Cada indivíduo autorizado a aceder ao serviço deve ter um certificado de cliente emitido para uso exclusivo e deve escolher a sua própria senha forte para proteger a sua chave privada. 

Os seguintes passos devem ser executados na mesma máquina onde o certificado de CA auto-assinado foi gerado e armazenado:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personalização:

* -n com identificação para o cliente que será autenticado com este certificado
* -e com a data de validade do certificado
* MyID.pvk e MyID.cer com nomes de ficheiros únicos para este certificado de cliente

Este comando solicitará a criação de uma palavra-passe e depois utilizada uma vez. Use uma senha forte.

## <a name="create-pfx-files-for-client-certificates"></a>Criar ficheiros PFX para certificados de cliente
Para cada certificado de cliente gerado, execute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalização:

    MyID.pvk and MyID.cer with the filename for the client certificate

Introduza a palavra-passe e, em seguida, exporte certificado com estas opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades estendidas
* O indivíduo a quem este certificado está a ser emitido deve escolher a senha de exportação

## <a name="import-client-certificate"></a>Certificado de cliente de importação
Cada indivíduo para quem tenha sido emitido um certificado de cliente deve importar o par chave nas máquinas que utilizarão para comunicar com o serviço:

* Clique duas vezes no . Ficheiro PFX no Windows Explorer
* Certificado de importação na loja pessoal com pelo menos esta opção:
  * Incluir todas as propriedades estendidas verificadas

## <a name="copy-client-certificate-thumbprints"></a>Copiar impressões digitais de certificado de cliente
Cada indivíduo para quem tenha sido emitido um certificado de cliente deve seguir estes passos para obter a impressão digital do seu certificado, que será adicionado ao ficheiro de configuração do serviço:

* Executar certmgr.exe
* Selecione o separador Pessoal
* Clique duas vezes no certificado de cliente a utilizar para autenticação
* No diálogo certificado que abre, selecione o separador Detalhes
* Certifique-se de que show está exibindo All
* Selecione o campo chamado Thumbprint na lista
* Copiar o valor da impressão digital
  * Eliminar caracteres Unicode não visíveis na frente do primeiro dígito
  * Eliminar todos os espaços

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Configurar clientes permitidos no ficheiro de configuração do serviço
Atualizar o valor da seguinte definição no ficheiro de configuração do serviço com uma lista separada das impressões digitais dos certificados de cliente permitiu o acesso ao serviço:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Configure verificação de revogação do certificado de cliente
A definição predefinida não é verificada com a Autoridade de Certificação para o estatuto de revogação do certificado de cliente. Para ativar os controlos, se a Autoridade de Certificação que emitiu os certificados de cliente suportar tais verificações, altere a seguinte definição com um dos valores definidos na Enumeração X509RevogaçãoMode:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Criar ficheiro PFX para certificados de encriptação auto-assinados
Para um certificado de encriptação, execute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalização:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Introduza a palavra-passe e, em seguida, exporte certificado com estas opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades estendidas
* Necessitará da senha ao enviar o certificado para o serviço de nuvem.

## <a name="export-encryption-certificate-from-certificate-store"></a>Certificado de encriptação de exportação de loja de certificados
* Encontrar certificado
* Clique em Ações -> Todas as tarefas -> Exportação...
* Certificado de exportação em . Ficheiro PFX com estas opções: 
  * Sim, exportar a chave privada
  * Inclua todos os certificados na via de certificação, se possível 
* Exportar todas as propriedades estendidas

## <a name="upload-encryption-certificate-to-cloud-service"></a>Faça upload de certificado de encriptação para o serviço na nuvem
Faça upload do certificado com o existente ou gerado . Ficheiro PFX com o par de chaves de encriptação:

* Introduza a palavra-passe protegendo as informações de chave privadas

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Atualizar certificado de encriptação no ficheiro de configuração do serviço
Atualize o valor de impressão digital das seguintes definições no ficheiro de configuração do serviço com a impressão digital do certificado enviado para o serviço em nuvem:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Operações comuns de certificados
* Configure o certificado TLS/SSL
* Configurar certificados de cliente

## <a name="find-certificate"></a>Encontrar certificado
Siga estes passos.

1. Corra mmc.exe.
2. Ficheiro -> Adicionar/Remover Snap-in...
3. Selecione **Certificados**.
4. Clique em **Adicionar**.
5. Escolha a localização da loja de certificados.
6. Clique em **Concluir**.
7. Clique em **OK**.
8. Expandir **Certificados**.
9. Expanda o nó da loja de certificados.
10. Expandir o nó de criança certificado.
11. Selecione um certificado na lista.

## <a name="export-certificate"></a>Exportar o certificado
No **Certificado De Saque De Exportação:**

1. Clique em **Seguinte**.
2. Selecione **Sim,** em **seguida, exportar a chave privada**.
3. Clique em **Seguinte**.
4. Selecione o formato de ficheiro de saída pretendido.
5. Verifique as opções desejadas.
6. Verifique **a palavra-passe**.
7. Introduza uma senha forte e confirme-a.
8. Clique em **Seguinte**.
9. Digite ou navegue num nome de ficheiro onde guardar o certificado (use a . Extensão PFX).
10. Clique em **Seguinte**.
11. Clique em **Concluir**.
12. Clique em **OK**.

## <a name="import-certificate"></a>Importar o certificado
No Certificado De Import Wizard:

1. Selecione a localização da loja.
   
   * Selecione **Utilizador Atual** se apenas os processos em execução sob o utilizador atual acederão ao serviço
   * Selecione **Máquina Local** se outros processos neste computador acederão ao serviço
2. Clique em **Seguinte**.
3. Se importar de um ficheiro, confirme o caminho do ficheiro.
4. Se importar um . Ficheiro PFX:
   1. Introduza a palavra-passe protegendo a chave privada
   2. Selecione opções de importação
5. Selecione certificados "Lugar" na seguinte loja
6. Clique em **Browse** (Procurar).
7. Selecione a loja desejada.
8. Clique em **Concluir**.
   
   * Se a loja Trust Root Certification Authority foi escolhida, clique **em Sim**.
9. Clique **ok** em todas as janelas de diálogo.

## <a name="upload-certificate"></a>Carregar certificado
No [portal do Azure](https://portal.azure.com/)

1. Selecione **Serviços cloud**.
2. Selecione o serviço de nuvem.
3. No menu superior, clique em **Certificados**.
4. Na barra inferior, clique em **Carregar**.
5. Selecione o ficheiro de certificado.
6. Se for um. Ficheiro PFX, introduza a palavra-passe para a chave privada.
7. Uma vez concluída, copie a impressão digital do certificado a partir da nova entrada na lista.

## <a name="other-security-considerations"></a>Outras considerações de segurança
As definições tLS descritas neste documento encriptam a comunicação entre o serviço e os seus clientes quando o ponto final HTTPS é utilizado. Isto é importante, uma vez que as credenciais para o acesso à base de dados e potencialmente outras informações sensíveis estão contidas na comunicação. Note-se, no entanto, que o serviço persiste no estado interno, incluindo credenciais, nas suas tabelas internas na base de dados do Azure SQL Database que forneceu para armazenamento de metadados na sua subscrição do Microsoft Azure. Essa base de dados foi definida como parte da seguinte definição no ficheiro de configuração do seu serviço (. Arquivo CSCFG): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

As credenciais armazenadas nesta base de dados estão encriptadas. No entanto, como uma boa prática, certifique-se de que as funções web e dos trabalhadores das suas implementações de serviço são mantidas atualizadas e seguras, uma vez que ambos têm acesso à base de dados de metadados e ao certificado utilizado para encriptação e desencriptação de credenciais armazenadas. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

