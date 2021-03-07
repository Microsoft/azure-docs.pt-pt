---
title: Use certificados com Azure Stack Edge Pro GPU | Microsoft Docs
description: Descreve a utilização de certificados com o dispositivo GPU Azure Stack Edge Pro, incluindo o porquê de usar, que tipos e como carregar certificados no seu dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4193e58c28b481297df38bca8f18d2ea766ce886
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443136"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Use certificados com dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve os tipos de certificados que podem ser instalados no seu dispositivo Azure Stack Edge Pro. O artigo também inclui os detalhes de cada tipo de certificado, juntamente com o procedimento para instalar e identificar a data de validade. 

## <a name="about-certificates"></a>Acerca de certificados

Um certificado fornece uma ligação entre uma **chave pública** e uma entidade (como o nome de domínio) que tenha sido **assinada** (verificada) por um terceiro de confiança (como uma autoridade de **certificados).**  Um certificado fornece uma maneira conveniente de distribuir chaves de encriptação pública fidedignas. Os certificados garantem assim que a sua comunicação é fidedigna e que está a enviar informações encriptadas para o servidor certo. 

Quando o seu dispositivo Azure Stack Edge Pro está inicialmente configurado, os certificados auto-assinados são automaticamente gerados. Opcionalmente, pode trazer os seus próprios certificados. Há diretrizes que tem de seguir se pretender trazer os seus próprios certificados.

## <a name="types-of-certificates"></a>Tipos de certificado

Os vários tipos de certificados que são utilizados no seu dispositivo Azure Stack Edge Pro são os seguintes: 
- Certificados de assinatura
    - AC de Raiz
    - Intermédio

- Certificados de ponto final
    - Certificado de nó
    - Certificados de UI locais
    - Certificados Azure Resource Manager
    - Certificados de armazenamento blob
    - Certificados de dispositivo ioT
    <!--- WiFi certificates
    - VPN certificates-->

- Certificados de encriptação
    - Certificados de sessão de apoio

Cada um destes certificados é descrito em pormenor nas seguintes secções.

## <a name="signing-chain-certificates"></a>Certificados de cadeia de assinatura

Estes são os certificados para a autoridade que assina os certificados ou a autoridade de certificado de assinatura. 

### <a name="types"></a>Tipos

Estes certificados podem ser certificados de raiz ou certificados intermédios. Os certificados de raiz são sempre auto-assinados (ou assinados por si mesmos). Os certificados intermédios não são auto-assinados e são assinados pela autoridade de assinatura.

### <a name="caveats"></a>Ressalvas

- Os certificados de raiz devem ser a assinatura de certificados em cadeia.
- Os certificados de raiz podem ser carregados no seu dispositivo no seguinte formato: 
    - **DER** – Estes estão disponíveis como extensão de `.cer` ficheiro.
    - **Base-64 codificado** – Estes estão disponíveis como `.cer` extensão de ficheiro.
    - **P7b** – Este formato é utilizado apenas para a assinatura de certificados em cadeia que incluem os certificados raiz e intermédio.
- Os certificados de cadeia de assinatura são sempre carregados antes de carregar quaisquer outros certificados.


## <a name="node-certificates"></a>Certificados de nó

<!--Your Azure Stack Edge Pro device could be a 1-node device or a 4-node device.--> Todos os nós do dispositivo estão constantemente a comunicar uns com os outros e, portanto, precisam de ter uma relação de confiança. Os certificados de nó fornecem uma forma de estabelecer essa confiança. Os certificados de nó também entram em jogo quando estiver a ligar-se ao nó do dispositivo utilizando uma sessão remota do PowerShell em https.

### <a name="caveats"></a>Ressalvas

- O certificado de nó deve ser fornecido em `.pfx` formato com uma chave privada que pode ser exportada. 
- Pode criar e carregar 1 certificado de nó wildcard ou 4 certificados individuais de nó. 
- Um certificado de nó deve ser alterado se o domínio DNS mudar, mas o nome do dispositivo não muda. Se estiver a trazer o seu próprio certificado de nó, não pode alterar o número de série do dispositivo, só pode alterar o nome de domínio.
- Utilize a seguinte tabela para guiá-lo ao criar um certificado de nó.
   
    |Tipo |Nome do sujeito (SN)  |Nome alternativo sujeito (SAN)  |Exemplo do nome do sujeito |
    |---------|---------|---------|---------|
    |Nó|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>Certificados de ponto final

Para quaisquer pontos finais que o dispositivo exponha, é necessário um certificado para comunicação fidedigna. Os certificados de ponto final incluem os necessários ao aceder ao Gestor de Recursos Azure e ao armazenamento de bolhas através das APIs REST. 

Quando você traz um certificado assinado por si mesmo, você também precisa da cadeia de assinatura correspondente do certificado. Para a cadeia de assinaturas, Azure Resource Manager, e os certificados blob no dispositivo, necessitará dos certificados correspondentes na máquina cliente também para autenticar e comunicar com o dispositivo.

### <a name="caveats"></a>Ressalvas

- Os certificados de ponto final têm de estar em `.pfx` formato com uma chave privada. A corrente de assinatura deve ser o formato DER `.cer` (extensão de ficheiro). 
- Quando você traz os seus próprios certificados de ponto final, estes podem ser como certificados individuais ou certificados de pesarina. 
- Se estiver a introduzir a cadeia de assinaturas, o certificado de corrente de assinatura deve ser carregado antes de enviar um certificado de ponto final.
- Estes certificados devem ser alterados se o nome do dispositivo ou os nomes de domínio DNS mudarem.
- Pode ser utilizado um certificado de ponto final wildcard.
- As propriedades dos certificados de ponto final são semelhantes às de um certificado SSL típico. 
- Utilize a seguinte tabela ao criar um certificado de ponto final:

    |Tipo |Nome do sujeito (SN)  |Nome alternativo sujeito (SAN)  |Exemplo do nome do sujeito |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Armazenamento de blobs|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Certificado único multi-SAN para ambos os pontos finais|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>Certificados de UI locais

Pode aceder à UI web local do seu dispositivo através de um browser. Para garantir que esta comunicação é segura, pode fazer o upload do seu próprio certificado. 

### <a name="caveats"></a>Ressalvas

- O certificado de UI local também é carregado em `.pfx` um formato com uma chave privada que pode ser exportada.
- Depois de fazer o upload do certificado de UI local, terá de reiniciar o navegador e limpar a cache. Consulte as instruções específicas para o seu navegador.

    |Tipo |Nome do sujeito (SN)  |Nome alternativo sujeito (SAN)  |Exemplo do nome do sujeito |
    |---------|---------|---------|---------|
    |Local UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>Certificados de dispositivo IoT Edge

O seu dispositivo Azure Stack Edge Pro é também um dispositivo IoT com o cálculo ativado por um dispositivo IoT Edge ligado ao mesmo. Para qualquer comunicação segura entre este dispositivo IoT Edge e os dispositivos a jusante que possam ligar-se ao mesmo, também pode carregar certificados IoT Edge. 

O dispositivo tem certificados auto-assinados que podem ser utilizados se pretender utilizar apenas o cenário computacional com o dispositivo. Se o dispositivo Azure Stack Edge Pro estiver entretanto ligado a dispositivos a jusante, então terá de trazer os seus próprios certificados.

Existem três certificados IoT Edge que precisa de instalar para permitir esta relação de confiança:

- **Autoridade de certificados de raiz ou autoridade de certificados de proprietário**
- **Autoridade de certificados de dispositivo** 
- **Certificado chave do dispositivo**

### <a name="caveats"></a>Ressalvas

- Os certificados IoT Edge são carregados em `.pem` formato. 


Para obter mais informações sobre os certificados IoT Edge, consulte os [dados do certificado Azure IoT Edge](../iot-edge/iot-edge-certs.md#iot-edge-certificates).

## <a name="support-session-certificates"></a>Certificados de sessão de apoio

Se o seu dispositivo Azure Stack Edge Pro estiver a ter algum problema, então para resolver esses problemas, poderá ser aberta uma sessão remota de Suporte powerShell no dispositivo. Para ativar uma comunicação segura e encriptada durante esta sessão de Suporte, pode fazer o upload de um certificado.

### <a name="caveats"></a>Ressalvas

- Certifique-se de que o certificado correspondente `.pfx` com chave privada está instalado na máquina do cliente utilizando a ferramenta de desencriptação.
- Verifique se o campo **de utilização** chave do certificado não é **a Assinatura de Certificados.** Para verificar isto, clique com o certificado à direita, escolha **Abrir** e no separador **Detalhes,** encontre **o Uso da Chave**. 


### <a name="caveats"></a>Ressalvas

- O certificado de sessão de apoio deve ser fornecido como formato DER com uma `.cer` extensão.


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge Pro device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>Criar certificados (opcional)

A secção seguinte descreve o procedimento para criar certificados de cadeia de assinatura e ponto final.

### <a name="certificate-workflow"></a>Fluxo de trabalho de certificado

Terá uma forma definida de criar os certificados para os dispositivos que operam no seu ambiente. Pode utilizar os certificados que lhe são fornecidos pelo seu administrador de TI. 

**Apenas para fins de desenvolvimento ou teste, também pode utilizar o Windows PowerShell para criar certificados no seu sistema local.** Ao criar os certificados para o cliente, siga estas orientações:

1. Pode criar qualquer um dos seguintes tipos de certificados:

    - Crie um único certificado válido para utilização com um único nome de domínio totalmente qualificado (FQDN). Por exemplo, *mydomain.com.*
    - Crie um certificado wildcard para garantir o nome principal de domínio e vários sub domínios também. Por exemplo, **.mydomain.com*.
    - Crie um certificado de nome alternativo (SAN) que cubra vários nomes de domínio num único certificado. 

2. Se você está trazendo o seu próprio certificado, você precisará de um certificado de raiz para a cadeia de assinaturas. Consulte os passos para [criar certificados de cadeia de assinaturas.](#create-signing-chain-certificate)

3. Em seguida, pode criar os certificados de ponto final para a UI local do aparelho, blob e Azure Resource Manager. Pode criar 3 certificados separados para o aparelho, blob e Azure Resource Manager, ou pode criar um certificado para todos os 3 pontos finais. Para obter etapas detalhadas, consulte [Criar certificados de assinatura e ponto final.](#create-signed-endpoint-certificates)

4. Quer esteja a criar 3 certificados separados ou um certificado, especifique os nomes de assunto (SN) e submineje nomes alternativos (SAN) de acordo com as orientações fornecidas para cada tipo de certificado. 

### <a name="create-signing-chain-certificate"></a>Criar certificado de cadeia de assinatura

Crie estes certificados através do Windows PowerShell em funcionamento no modo de administrador. **Os certificados criados desta forma devem ser utilizados apenas para fins de desenvolvimento ou de ensaio.**

O certificado de cadeia de assinaturas só precisa de ser criado uma vez. Os outros certificados de ponto final referem-se a este certificado para a assinatura.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>Criar certificados de ponto final assinados

Crie estes certificados através do Windows PowerShell em funcionamento no modo de administrador.

Nestes exemplos, os certificados de pontos finais são criados para um dispositivo com:
    - Nome do dispositivo: `DBE-HWDC1T2`
    - Domínio DNS: `microsoftdatabox.com`

Substitua-o pelo nome e domínio DNS pelo seu dispositivo para criar certificados para o seu dispositivo.
 
**Certificado de ponto final blob**

Crie um certificado para o ponto final blob na sua loja pessoal.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certificado de ponto final do Azure Resource Manager**

Crie um certificado para os pontos finais do Azure Resource Manager na sua loja pessoal.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certificado de UI web local do dispositivo**

Crie um certificado para a UI web local do dispositivo na sua loja pessoal.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certificado multi-SAN único para todos os pontos finais**

Crie um único certificado para todos os pontos finais da sua loja pessoal.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

Uma vez criados os certificados, o próximo passo é carregar os certificados no seu dispositivo Azure Stack Edge Pro


## <a name="upload-certificates"></a>Certificados de upload 

Os certificados que criou para o seu dispositivo por padrão residem na **loja Personal** do seu cliente. Estes certificados precisam de ser exportados no seu cliente para ficheiros de formato apropriados que podem ser depois enviados para o seu dispositivo.

1. O certificado de raiz deve ser exportado como formato DER com `.cer` extensão. Para etapas detalhadas, consulte [os certificados de exportação como formato DER](#export-certificates-as-der-format).
2. Os certificados de ponto final devem ser exportados como *ficheiros .pfx* com chaves privadas. Para etapas detalhadas, consulte [os certificados de exportação como ficheiro *.pfx* com chaves privadas](#export-certificates-as-pfx-format-with-private-key). 
3. Os certificados raiz e ponto final são então carregados no dispositivo utilizando a opção **de certificado + Adicionar** na página certificados na UI web local. 

    1. Faça o upload dos certificados de raiz primeiro. Na UI web local, aceda aos **Certificados > + Adicionar certificado**.

        ![Adicionar certificado de cadeia de assinatura 1](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. Em seguida, carre faça o upload dos certificados de ponto final. 

        ![Adicionar certificado de cadeia de assinatura 2](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        Escolha os ficheiros de certificado em formato *.pfx* e introduza a palavra-passe que forneceu quando exportou o certificado. O certificado Azure Resource Manager pode demorar alguns minutos a candidatar-se.

        Se a cadeia de assinaturas não for atualizada primeiro e tentar carregar os certificados de ponto final, então terá um erro.

        ![Aplicar erro de certificado](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        Volte e carre fique com o certificado da cadeia de assinaturas e, em seguida, carre fique e aplique os certificados de ponto final.

> [!IMPORTANT]
> Se o nome do dispositivo ou o domínio DNS forem alterados, devem ser criados novos certificados. Os certificados de cliente e os certificados do dispositivo devem então ser atualizados com o novo nome do dispositivo e o domínio DNS. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>Certificados de importação no cliente que aceda ao dispositivo

Os certificados que criou e carregou para o seu dispositivo devem ser importados no seu cliente Windows (acedendo ao dispositivo) na loja de certificados apropriada.

1. O certificado de raiz que exportou como DER deve agora ser importado nas Autoridades de **Certificados de Raiz Fidedignas** no seu sistema de clientes. Para obter etapas detalhadas, consulte [os certificados de importação na loja Trusted Root Certificate Authorities](#import-certificates-as-der-format).

2. Os certificados de ponto final que exportou como `.pfx` deve ser exportado como DER com `.cer` extensão. Isto `.cer` é então importado na **loja de certificados pessoais** no seu sistema. Para obter etapas detalhadas, consulte [os certificados de importação na loja de certificados pessoais.](#import-certificates-as-der-format)

### <a name="import-certificates-as-der-format"></a>Certificados de importação como formato DER

Para importar certificados num cliente Windows, tome as seguintes medidas:

1. Clique com o botão direito no ficheiro e selecione **o certificado de instalação.** Esta ação inicia o Certificado De Importação De Feiticeiro.

    ![Certificado de importação 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. Para **localizar a Loja**, selecione Máquina **Local** e, em seguida, selecione **Seguinte**.

    ![Certificado de importação 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. Selecione **Coloque todos os certificados na loja seguinte** e, em seguida, selecione **Procurar**. 

    - Para importar para loja pessoal, navegue na loja Pessoal do seu anfitrião remoto e, em seguida, selecione **Next**.

        ![Certificado de importação 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - Para importar em loja de confiança, navegue para a Autoridade de Certificados de Raiz Fidedigna e, em seguida, selecione **Next**.

        ![Certificado de importação 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. Selecione **Concluir**. Aparece uma mensagem no sentido de que a importação foi bem sucedida.

### <a name="export-certificates-as-pfx-format-with-private-key"></a>Certificados de exportação como formato .pfx com chave privada

Tome as seguintes medidas para exportar um certificado SSL com chave privada numa máquina Windows. 

> [!IMPORTANT]
> Execute estes passos na mesma máquina que usou para criar o certificado. 

1. Executar *certlm.msc* para lançar a loja de certificados de máquina local.

1. Clique duas **vezes** na pasta Pessoal e, em seguida, nos **Certificados**.

    ![Certificado de exportação 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. Clique com o botão direito no certificado que gostaria de fazer e escolher **Todas as tarefas > Exportar...**

    ![Certificado de exportação 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. Siga o Assistente de Exportação de Certificados para fazer o back up do seu certificado num ficheiro .pfx.

    ![Certificado de exportação 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. Escolha **Sim, exporte a chave privada.**

    ![Certificado de exportação 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. Escolha **Inclua todos os certificados na rota do certificado, se possível,** **Exporte todas as propriedades estendidas** e **permita a privacidade do certificado.** 

    > [!IMPORTANT]
    > NÃO selecione a **opção 'Eliminar Chave Privada' se a exportação for bem sucedida**.

    ![Certificado de exportação 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. Introduza uma palavra-passe que se lembrará. Confirme a palavra-passe. A palavra-passe protege a chave privada.

    ![Certificado de exportação 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. Opte por guardar o ficheiro num local definido.

    ![Certificado de exportação 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. Selecione **Concluir**.

    ![Certificado de exportação 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. Recebes uma mensagem A exportação foi bem sucedida. Selecione **OK**.

    ![Certificado de exportação 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

A cópia de segurança do ficheiro .pfx está agora guardada no local selecionado e está pronta para ser movida ou armazenada para a sua manutenção segura.


### <a name="export-certificates-as-der-format"></a>Certificados de exportação como formato DER

1. Executar *certlm.msc* para lançar a loja de certificados de máquina local.

1. Na loja de certificados Pessoal, selecione o certificado raiz. Clique à direita e selecione **Todas as Tarefas > Exportar...**

    ![Certificado de exportação DER 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. O assistente de certificado abre. Selecione o formato como **DER codificado binário X.509 (.cer)**. Selecione **Seguinte**.

    ![Certificado de exportação DER 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. Navegue e selecione o local onde pretende exportar o ficheiro de formato .cer.

    ![Certificado de exportação DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. Selecione **Concluir**.

    ![Certificado de exportação DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>Algoritmos de certificado suportados

 Apenas os certificados Rivest-Shamir-Adleman (RSA) são suportados com o seu dispositivo Azure Stack Edge Pro. Se forem utilizados certificados de algoritmo de assinatura digital de curva elíptica (ECDSA), então o comportamento do dispositivo é indeterminado.

 Os certificados que contêm uma chave pública RSA são referidos como certificados RSA. Os certificados que contêm uma chave pública criptográfica de curva elíptica (ECC) são referidos como certificados ECDSA (Algoritmo de Assinatura Digital curva elíptica). 


## <a name="view-certificate-expiry"></a>Ver expiração do certificado

Se trouxer os seus próprios certificados, os certificados expirarão normalmente em 1 ano ou 6 meses. Para ver a data de validade no seu certificado, aceda à página **certificados** na UI web local do seu dispositivo. Se selecionar um certificado específico, pode visualizar a data de validade no seu certificado.

<!--## Rotate certificates

Rotation of certificates is not implemented in this release. You are also not notified of the pending expiration date on your certificate. 

View the certificate expiration date on the **Certificates** page in the local web UI of your device. Once the certificate expiration is approaching, create and upload new certificates as per the detailed instructions in [Create and upload certificates]().-->

## <a name="next-steps"></a>Passos seguintes

[Implemente o seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)