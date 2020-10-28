---
title: Configuração de segurança de fusão dividida
description: Configurar certificados x409 para encriptação com o serviço de divisão/fusão para a escala elástica.
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
ms.openlocfilehash: 235efc550fd47d4244a5bf081c75d5e824a8e4b4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793437"
---
# <a name="split-merge-security-configuration"></a>Configuração de segurança de fusão dividida
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Para utilizar o serviço Split/Merge, tem de configurar corretamente a segurança. O serviço faz parte da função de Escala Elástica da Base de Dados Azure SQL. Para obter mais informações, consulte [o Tutorial de Serviço de Split de Escala Elástica e Serviço de Fusão.](elastic-scale-configure-deploy-split-and-merge.md)

## <a name="configuring-certificates"></a>Certificados de configuração

Os certificados são configurados de duas maneiras. 

1. [Para configurar o Certificado TLS/SSL](#to-configure-the-tlsssl-certificate)
2. [Para configurar certificados de cliente](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Para obter certificados

Os certificados podem ser obtidos a partir de Autoridades públicas de Certificados (AC) ou do Serviço de [Certificados](/windows/win32/seccrypto/certificate-services)do Windows . Estes são os métodos preferidos para obter certificados.

Se essas opções não estiverem disponíveis, pode gerar **certificados auto-assinados.**

## <a name="tools-to-generate-certificates"></a>Ferramentas para gerar certificados

* [makecert.exe](/previous-versions/dotnet/netframework-4.0/bfsktky3(v=vs.100))
* [pvk2pfx.exe](/windows-hardware/drivers/devtest/pvk2pfx)

### <a name="to-run-the-tools"></a>Para executar as ferramentas

* A partir de um pedido de comando de desenvolvedor para estúdios visuais, consulte [o pedido de comando do estúdio visual](/dotnet/framework/tools/developer-command-prompt-for-vs) 
  
    Se instalado, vá a:
  
    ```console
    %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
    ```

* Obtenha o WDK do [Windows 8.1: Descarregue kits e ferramentas](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-tlsssl-certificate"></a>Para configurar o certificado TLS/SSL

É necessário um certificado TLS/SSL para encriptar a comunicação e autenticar o servidor. Escolha o mais aplicável dos três cenários abaixo e execute todos os seus passos:

### <a name="create-a-new-self-signed-certificate"></a>Criar um novo certificado auto-assinado

1. [Criar um certificado de Self-Signed](#create-a-self-signed-certificate)
2. [Criar ficheiro PFX para Self-Signed Certificado TLS/SSL](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Carregar certificado TLS/SSL para o Serviço de Cloud](#upload-tlsssl-certificate-to-cloud-service)
4. [Atualização certificado TLS/SSL em ficheiro de configuração de serviço](#update-tlsssl-certificate-in-service-configuration-file)
5. [Autoridade de Certificação TLS/SSL de Importação](#import-tlsssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Utilizar um certificado existente na loja de certificados
1. [Certificado de exportação TLS/SL da Loja de Certificados](#export-tlsssl-certificate-from-certificate-store)
2. [Carregar certificado TLS/SSL para o Serviço de Cloud](#upload-tlsssl-certificate-to-cloud-service)
3. [Atualização certificado TLS/SSL em ficheiro de configuração de serviço](#update-tlsssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Para utilizar um certificado existente num ficheiro PFX
1. [Carregar certificado TLS/SSL para o Serviço de Cloud](#upload-tlsssl-certificate-to-cloud-service)
2. [Atualização certificado TLS/SSL em ficheiro de configuração de serviço](#update-tlsssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Para configurar certificados de cliente
São necessários certificados para clientes para autenticar pedidos ao serviço. Escolha o mais aplicável dos três cenários abaixo e execute todos os seus passos:

### <a name="turn-off-client-certificates"></a>Desligue os certificados de cliente
1. [Desligue a autenticação Certificate-Based cliente](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Emite novos certificados de cliente auto-assinados
1. [Criar uma Autoridade de Certificação Self-Signed](#create-a-self-signed-certification-authority)
2. [Faça upload do certificado CA para o Serviço cloud](#upload-ca-certificate-to-cloud-service)
3. [Atualização certificado CA no ficheiro de configuração de serviço](#update-ca-certificate-in-service-configuration-file)
4. [Emitir Certificados de Cliente](#issue-client-certificates)
5. [Criar ficheiros PFX para Certificados de Cliente](#create-pfx-files-for-client-certificates)
6. [Certificado de Cliente importador](#import-client-certificate)
7. [Impressão digital do certificado do cliente de cópia](#copy-client-certificate-thumbprints)
8. [Configurar clientes permitidos no Ficheiro de Configuração de Serviço](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Utilizar certificados de cliente existentes
1. [Encontre a chave pública ca](#find-ca-public-key)
2. [Faça upload do certificado CA para o Serviço cloud](#upload-ca-certificate-to-cloud-service)
3. [Atualização certificado CA no ficheiro de configuração de serviço](#update-ca-certificate-in-service-configuration-file)
4. [Impressão digital do certificado do cliente de cópia](#copy-client-certificate-thumbprints)
5. [Configurar clientes permitidos no Ficheiro de Configuração de Serviço](#configure-allowed-clients-in-the-service-configuration-file)
6. [Verificação de revogação do certificado de cliente configurada](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Endereços IP permitidos
O acesso aos pontos finais de serviço pode ser restringido a gamas específicas de endereços IP.

## <a name="to-configure-encryption-for-the-store"></a>Para configurar encriptação para a loja
É necessário um certificado para encriptar as credenciais que são armazenadas na loja de metadados. Escolha o mais aplicável dos três cenários abaixo e execute todos os seus passos:

### <a name="use-a-new-self-signed-certificate"></a>Use um novo certificado auto-assinado
1. [Criar um certificado de Self-Signed](#create-a-self-signed-certificate)
2. [Criar ficheiro PFX para Self-Signed Certificado de Encriptação](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Faça upload do certificado de encriptação para o serviço cloud](#upload-encryption-certificate-to-cloud-service)
4. [Certificado de encriptação de atualização no ficheiro de configuração de serviço](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Utilize um certificado existente na loja de certificados
1. [Certificado de encriptação de exportação da Loja de Certificados](#export-encryption-certificate-from-certificate-store)
2. [Faça upload do certificado de encriptação para o serviço cloud](#upload-encryption-certificate-to-cloud-service)
3. [Certificado de encriptação de atualização no ficheiro de configuração de serviço](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Utilize um certificado existente num ficheiro PFX
1. [Faça upload do certificado de encriptação para o serviço cloud](#upload-encryption-certificate-to-cloud-service)
2. [Certificado de encriptação de atualização no ficheiro de configuração de serviço](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>A configuração padrão
A configuração predefinida nega todo o acesso ao ponto final HTTP. Esta é a definição recomendada, uma vez que os pedidos a estes pontos finais podem transportar informações sensíveis como credenciais de base de dados.
A configuração predefinida permite todo o acesso ao ponto final HTTPS. Esta definição pode ser restringida ainda mais.

### <a name="changing-the-configuration"></a>Alterar a Configuração
O grupo de regras de controlo de acesso que se aplicam e ponto final estão configurados na **\<EndpointAcls>** secção do ficheiro de **configuração** de serviço .

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

As regras de um grupo de controlo de acessos estão configuradas numa \<AccessControl name=""> secção do ficheiro de configuração de serviço. 

O formato é explicado na documentação das Listas de Controlo de Acesso à Rede.
Por exemplo, para permitir apenas iPs na faixa 100.100.0.0 a 100.100.255.255 para aceder ao ponto final HTTPS, as regras seriam assim:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Negação da prevenção de serviços
Existem dois mecanismos diferentes apoiados para detetar e prevenir ataques de Negação de Serviço:

* Restringir o número de pedidos simultâneos por anfitrião remoto (desligado por padrão)
* Restringir a taxa de acesso por anfitrião remoto (por defeito)

Estes baseiam-se nas funcionalidades ainda mais documentadas na Dynamic IP Security no IIS. Ao alterar esta configuração, tenha cuidado com os seguintes fatores:

* O comportamento dos proxies e dos dispositivos de tradução de endereços de rede sobre as informações remotas do anfitrião
* Cada pedido a qualquer recurso na função web é considerado (por exemplo, carregar scripts, imagens, etc)

## <a name="restricting-number-of-concurrent-accesses"></a>Restringir o número de acessos simultâneos
As configurações que configuram este comportamento são:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Alterar DynamicIpRestrictionDenyByConcurrentRequests para ser verdadeiro para permitir esta proteção.

## <a name="restricting-rate-of-access"></a>Taxa de acesso restritiva
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

Consulte a documentação para a Dynamic IP Security no IIS para outros valores suportados.

## <a name="operations-for-configuring-service-certificates"></a>Operações de configuração de certificados de serviço
Este tópico é apenas para referência. Siga os passos de configuração descritos em:

* Configure o certificado TLS/SSL
* Configure certificados de cliente

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Executar:

```console
makecert ^
  -n "CN=myservice.cloudapp.net" ^
  -e MM/DD/YYYY ^
  -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
  -a sha256 -len 2048 ^
  -sv MySSL.pvk MySSL.cer
```

Para personalizar:

* -n com a URL de serviço. Wildcards ("CN=*.cloudapp.net") e nomes alternativos ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") são suportados.
* -e com a data de validade do certificado Crie uma palavra-passe forte e especifique-a quando solicitada.

## <a name="create-pfx-file-for-self-signed-tlsssl-certificate"></a>Criar ficheiro PFX para certificado TLS/SSL auto-assinado
Executar:

```console
pvk2pfx -pvk MySSL.pvk -spc MySSL.cer
```

Introduza a palavra-passe e, em seguida, o certificado de exportação com estas opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades estendidas

## <a name="export-tlsssl-certificate-from-certificate-store"></a>Certificado de exportação TLS/SSL da loja de certificados
* Encontrar certificado
* Clique em Ações -> Todas as tarefas -> Exportação...
* Certificado de exportação em um . Ficheiro PFX com estas opções:
  * Sim, exportar a chave privada
  * Incluir todos os certificados na via de certificação, se possível *Exportar todas as propriedades estendidas

## <a name="upload-tlsssl-certificate-to-cloud-service"></a>Carregar certificado TLS/SSL para o serviço na nuvem
Faça o upload do certificado com o existente ou gerado . Ficheiro PFX com o par de chaves TLS:

* Introduza a palavra-passe protegendo as informações chave privadas

## <a name="update-tlsssl-certificate-in-service-configuration-file"></a>Atualização do certificado TLS/SSL no ficheiro de configuração de serviço
Atualize o valor da impressão digital do seguinte quadro de configuração de serviço com a impressão digital do certificado enviado para o serviço de nuvem:

```console
<Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="import-tlsssl-certification-authority"></a>Autoridade de certificação Import TLS/SSL
Siga estes passos em todas as contas/máquinas que comunicarão com o serviço:

* Clique duas vezes no . Arquivo CER no Windows Explorer
* No diálogo do Certificado, clique em Instalar Certificado...
* Certificado de importação na loja De Certificação de Raiz Fidedigna

## <a name="turn-off-client-certificate-based-authentication"></a>Desativar a autenticação baseada em certificados de cliente
Apenas a autenticação baseada em certificados de cliente é suportada e desativada permitirá o acesso público aos pontos finais do serviço, a menos que existam outros mecanismos (por exemplo, Rede Virtual Microsoft Azure).

Altere estas definições para falsas no ficheiro de configuração de serviço para desativar a função:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Em seguida, copie a mesma impressão digital que o certificado TLS/SSL na definição do certificado CA:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Criar uma autoridade de certificação auto-assinada
Execute as seguintes etapas para criar um certificado auto-assinado para atuar como Autoridade de Certificação:

```console
makecert ^
-n "CN=MyCA" ^
-e MM/DD/YYYY ^
 -r -cy authority -h 1 ^
 -a sha256 -len 2048 ^
  -sr localmachine -ss my ^
  MyCA.cer
```

Para personalizá-lo

* -e com a data de validade da certificação

## <a name="find-ca-public-key"></a>Encontre a chave pública da AC
Todos os certificados de cliente devem ter sido emitidos por uma Autoridade de Certificação fidedigna pelo serviço. Encontre a chave pública da Autoridade de Certificação que emitiu os certificados de cliente que vão ser utilizados para autenticação de forma a carregá-lo para o serviço de nuvem.

Se o ficheiro com a chave pública não estiver disponível, exporte-o para a loja de certificados:

* Encontrar certificado
  * Pesquisa de um certificado de cliente emitido pela mesma Autoridade de Certificação
* Clique duas vezes no certificado.
* Selecione o separador Caminho de Certificação no diálogo certificado.
* Clique duas vezes na entrada da AC no caminho.
* Tome nota das propriedades do certificado.
* Feche o diálogo **do Certificado.**
* Encontrar certificado
  * Procure a AC acima.
* Clique em Ações -> Todas as tarefas -> Exportação...
* Certificado de exportação em um . CER com estas opções:
  * **Não, não exporte a chave privada**
  * Inclua todos os certificados na rota de certificação, se possível.
  * Exportar todas as propriedades estendidas.

## <a name="upload-ca-certificate-to-cloud-service"></a>Faça upload do certificado CA para o serviço na nuvem
Faça o upload do certificado com o existente ou gerado . Arquivo CER com a chave pública ca.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Atualizar certificado CA no ficheiro de configuração de serviço
Atualize o valor da impressão digital do seguinte quadro de configuração de serviço com a impressão digital do certificado enviado para o serviço de nuvem:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Atualizar o valor da seguinte definição com a mesma impressão digital:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Emitir certificados de cliente
Cada pessoa autorizada a aceder ao serviço deve ter um certificado de cliente emitido para uso exclusivo e deve escolher a sua própria senha forte para proteger a sua chave privada. 

Os seguintes passos devem ser executados na mesma máquina em que o certificado de CA auto-assinado foi gerado e armazenado:

```console
makecert ^
  -n "CN=My ID" ^
  -e MM/DD/YYYY ^
  -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
  -a sha256 -len 2048 ^
  -in "MyCA" -ir localmachine -is my ^
  -sv MyID.pvk MyID.cer
```

Personalização:

* -n com uma ID para o cliente que será autenticado com este certificado
* -e com a data de validade do certificado
* MyID.pvk e MyID.cer com nomes de ficheiros exclusivos para este certificado de cliente

Este comando solicitará a criação de uma palavra-passe e depois será utilizada uma vez. Use uma senha forte.

## <a name="create-pfx-files-for-client-certificates"></a>Criar ficheiros PFX para certificados de cliente
Para cada certificado de cliente gerado, execute:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Personalização:

```console
MyID.pvk and MyID.cer with the filename for the client certificate
```

Introduza a palavra-passe e, em seguida, o certificado de exportação com estas opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades estendidas
* O indivíduo a quem este certificado está a ser emitido deve escolher a senha de exportação

## <a name="import-client-certificate"></a>Certificado de cliente de importação
Cada indivíduo para quem tenha sido emitido um certificado de cliente deve importar o par-chave nas máquinas que utilizará para comunicar com o serviço:

* Clique duas vezes no . Ficheiro PFX no Windows Explorer
* Certificado de importação para a loja Pessoal com pelo menos esta opção:
  * Incluir todas as propriedades estendidas verificadas

## <a name="copy-client-certificate-thumbprints"></a>Impressão digital do certificado do cliente de cópia
Cada pessoa para quem tenha sido emitido um certificado de cliente deve seguir estas etapas para obter a impressão digital do seu certificado, que será adicionada ao ficheiro de configuração de serviço:

* Executar certmgr.exe
* Selecione o separador Pessoal
* Clique duas vezes no certificado de cliente a ser usado para autenticação
* No diálogo certificado que abre, selecione o separador Detalhes
* Certifique-se de que o Show está a exibir Tudo
* Selecione o campo chamado Thumbprint na lista
* Copiar o valor da impressão digital
  * Eliminar caracteres Unicode não visíveis na frente do primeiro dígito
  * Apagar todos os espaços

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Configurar Clientes permitidos no ficheiro de configuração de serviço
Atualizar o valor da seguinte definição no ficheiro de configuração de serviço com uma lista separada por vírgula das impressões digitais dos certificados do cliente permitiu o acesso ao serviço:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Configurar verificação de revogação do certificado de cliente
A definição por defeito não verifica com a Autoridade de Certificação o estado de revogação do certificado de cliente. Para fazer a verificação, se a Autoridade de Certificação que emitiu os certificados de cliente suportar tais verificações, altere a seguinte definição com um dos valores definidos na Enumeração X509RevocationMode:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Criar ficheiro PFX para certificados de encriptação auto-assinados
Para um certificado de encriptação, execute:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Personalização:

```console
MyID.pvk and MyID.cer with the filename for the encryption certificate
```

Introduza a palavra-passe e, em seguida, o certificado de exportação com estas opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades estendidas
* Necessitará da palavra-passe ao enviar o certificado para o serviço de cloud.

## <a name="export-encryption-certificate-from-certificate-store"></a>Certificado de encriptação de exportação da loja de certificados
* Encontrar certificado
* Clique em Ações -> Todas as tarefas -> Exportação...
* Certificado de exportação em um . Ficheiro PFX com estas opções: 
  * Sim, exportar a chave privada
  * Incluir todos os certificados na rota de certificação, se possível 
* Exportar todas as propriedades estendidas

## <a name="upload-encryption-certificate-to-cloud-service"></a>Faça upload do certificado de encriptação para o serviço na nuvem
Faça o upload do certificado com o existente ou gerado . Ficheiro PFX com o par de chaves de encriptação:

* Introduza a palavra-passe protegendo as informações chave privadas

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Atualizar certificado de encriptação no ficheiro de configuração de serviço
Atualize o valor da impressão digital das seguintes definições no ficheiro de configuração de serviço com a impressão digital do certificado enviado para o serviço de nuvem:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Operações comuns de certificados
* Configure o certificado TLS/SSL
* Configure certificados de cliente

## <a name="find-certificate"></a>Encontrar certificado
Siga estes passos:

1. Corre mmc.exe.
2. Arquivo -> Adicionar/Remover Snap-in...
3. Selecione **Certificados** .
4. Clique em **Adicionar** .
5. Escolha o local da loja de certificados.
6. Clique em **Concluir** .
7. Clique em **OK** .
8. **Certificados de** Expansão .
9. Expandir o nó da loja de certificados.
10. Expandir o nó de criança Certificado.
11. Selecione um certificado na lista.

## <a name="export-certificate"></a>Exportar o certificado
No **certificado assistente de exportação:**

1. Clique em **Seguinte** .
2. Selecione **Sim,** em **seguida, Exporte a chave privada** .
3. Clique em **Seguinte** .
4. Selecione o formato de ficheiro de saída pretendido.
5. Verifique as opções desejadas.
6. Verifique **a palavra-passe.**
7. Introduza uma senha forte e confirme- a.
8. Clique em **Seguinte** .
9. Digite ou navegue num nome de arquivo onde armazenar o certificado (utilize um . Extensão PFX).
10. Clique em **Seguinte** .
11. Clique em **Concluir** .
12. Clique em **OK** .

## <a name="import-certificate"></a>Importar o certificado
No Certificado De Importação Assistente:

1. Selecione a localização da loja.
   
   * Selecione **o Utilizador Atual** se apenas os processos em execução sob o utilizador atual acederem ao serviço
   * Selecione **Máquina Local** se outros processos neste computador acederem ao serviço
2. Clique em **Seguinte** .
3. Se importar de um ficheiro, confirme o caminho do ficheiro.
4. Se importar um . Ficheiro PFX:
   1. Introduza a palavra-passe protegendo a chave privada
   2. Selecione opções de importação
5. Selecione certificados "Place" na seguinte loja
6. Clique em **Browse** (Procurar).
7. Selecione a loja desejada.
8. Clique em **Concluir** .
   
   * Se a loja Da Autoridade de Certificação de Raiz Fidedigna foi escolhida, clique em **Sim** .
9. Clique **em OK** em todas as janelas de diálogo.

## <a name="upload-certificate"></a>Carregar certificado
No [portal Azure](https://portal.azure.com/)

1. Selecione **Serviços cloud** .
2. Selecione o serviço de nuvem.
3. No menu superior, clique em **Certificados.**
4. Na barra inferior, clique em **Upload** .
5. Selecione o ficheiro de certificado.
6. Se for um. Ficheiro PFX, introduza a palavra-passe para a chave privada.
7. Uma vez concluído, copie a impressão digital do certificado da nova entrada na lista.

## <a name="other-security-considerations"></a>Outras considerações de segurança
As definições TLS descritas neste documento encriptam a comunicação entre o serviço e os seus clientes quando o ponto final HTTPS é utilizado. Isto é importante, uma vez que as credenciais para o acesso à base de dados e outras informações potencialmente sensíveis estão contidas na comunicação. Note-se, no entanto, que o serviço persiste no estado interno, incluindo credenciais, nas suas tabelas internas na base de dados da Base de Dados Azure SQL que forneceu para armazenamento de metadados na sua subscrição Microsoft Azure. Esta base de dados foi definida como parte da seguinte definição no seu ficheiro de configuração de serviço (. Ficheiro CSCFG): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

As credenciais armazenadas nesta base de dados estão encriptadas. No entanto, como uma boa prática, certifique-se de que tanto as funções web como os dos trabalhadores das suas implementações de serviço são mantidas atualizadas e seguras, uma vez que ambas têm acesso à base de dados de metadados e ao certificado utilizado para encriptação e desencriptação de credenciais armazenadas. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]