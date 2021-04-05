---
title: Use os seus próprios certificados com dispositivos pesados Azure Data Box/Azure Data Box
description: Como utilizar os seus próprios certificados para aceder ao armazenamento de UI e Blog local na sua Caixa de Dados ou dispositivo Data Box Heavy.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: 1836ed57305fd7e168961eb81670b56d4ce296cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545178"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>Use os seus próprios certificados com caixas de dados e dispositivos pesados de caixa de dados

Durante o processamento da encomenda, os certificados auto-assinados são gerados para aceder ao armazenamento web local UI e Blob para um dispositivo Data Box ou Data Box Heavy. Se preferir comunicar com o seu dispositivo através de um canal de confiança, pode usar os seus próprios certificados.

O artigo descreve como instalar os seus próprios certificados e como reverter para os certificados predefinidos antes de devolver o seu dispositivo ao datacenter. Apresenta igualmente um resumo dos requisitos dos certificados.

## <a name="about-certificates"></a>Acerca de certificados

Um certificado fornece uma ligação entre uma **chave pública** e uma entidade (como o nome de domínio) que tenha sido **assinada** (verificada) por um terceiro de confiança, como uma autoridade **de certificados**.  Um certificado fornece uma maneira conveniente de distribuir chaves de encriptação pública fidedignas. Desta forma, os certificados garantem que a sua comunicação é fidedigna e está a enviar informações encriptadas para o servidor certo.

Quando o seu dispositivo Data Box está inicialmente configurado, os certificados auto-assinados são automaticamente gerados. Opcionalmente, pode trazer os seus próprios certificados. Há diretrizes que tem de seguir se pretender trazer os seus próprios certificados.

Numa Caixa de Dados ou numa Caixa de Dados Dispositivo pesado, são utilizados dois tipos de certificados de ponto final:

- Certificado de armazenamento blob
- Certificado de UI local

### <a name="certificate-requirements"></a>Requisitos de certificados

Os certificados devem satisfazer os seguintes requisitos:

- O certificado de ponto final tem de estar em `.pfx` formato com uma chave privada que pode ser exportada.
- Pode utilizar um certificado individual para cada ponto final, um certificado de multidomatina para vários pontos finais ou um certificado de ponto final wildcard.
- As propriedades de um certificado de ponto final são semelhantes às propriedades de um certificado SSL típico.
- É necessário um certificado correspondente em formato DER `.cer` (extensão de nome de ficheiro) na máquina do cliente.
- Depois de fazer o upload do certificado de UI local, terá de reiniciar o navegador e limpar a cache. Consulte instruções específicas para o seu navegador.
- Os certificados devem ser alterados se o nome do dispositivo ou o nome de domínio DNS mudarem.
- Utilize o seguinte quadro ao criar certificados de ponto final:

  |Tipo |Nome do sujeito (SN)  |Nome alternativo sujeito (SAN)  |Exemplo do nome do sujeito |
  |---------|---------|---------|---------|
  |Local UI| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Armazenamento de blobs|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |Certificado único multi-SAN|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

Para mais informações, consulte [os requisitos do Certificado.](../../articles/databox-online/azure-stack-edge-gpu-certificate-requirements.md)

## <a name="add-certificates-to-device"></a>Adicionar certificados ao dispositivo

Pode utilizar os seus próprios certificados para aceder à UI web local e para aceder ao armazenamento blob.

> [!IMPORTANT]
> Se o nome do dispositivo ou o domínio DNS forem alterados, devem ser criados novos certificados. Os certificados de cliente e os certificados do dispositivo devem então ser atualizados com o novo nome do dispositivo e o domínio DNS.

Para adicionar o seu próprio certificado ao seu dispositivo, siga estes passos:

1. Ir para **Gerir**  >  **Certificados.**

   **O nome** mostra o nome do dispositivo. **O domínio DNS** mostra o nome de domínio do servidor DNS.

   A parte inferior do ecrã mostra os certificados atualmente em uso. Para um novo dispositivo, você verá os certificados auto-assinados que foram gerados durante o processamento da encomenda.

   ![Página de certificados para um dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. Se precisar de alterar o domínio **Nome** (nome do dispositivo) ou **DNS** (o domínio do servidor DNS para o dispositivo), faça-o agora, antes de adicionar o certificado. Em seguida, selecione **Aplicar**.

   O certificado deve ser alterado se o nome do dispositivo ou o nome de domínio DNS mudarem.

   ![Aplique um novo nome do dispositivo e domínio DNS para uma Caixa de Dados](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. Para adicionar um certificado, **selecione Adicionar certificado** para abrir o painel de **certificados Add.** Em seguida, selecione o **tipo certificado** - **ou blob armazenamento** ou **uI web local**.

   ![Adicionar painel de certificados na página de Certificados para um dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. Escolha o ficheiro de certificado (em `.pfx` formato) e introduza a palavra-passe que foi definida quando o certificado foi exportado. Em seguida, **selecione Valide & Adicionar**.

   ![Definições para adicionar um certificado de ponto final Blob a uma Caixa de Dados](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   Após a adição do certificado com sucesso, o ecrã **de Certificados** mostra a impressão digital do novo certificado. O estado do certificado é **válido.**

   ![Um novo certificado válido que foi adicionado com sucesso](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. Para ver os dados do certificado, selecione e clique no nome do certificado. O certificado expirará após um ano.

   ![Ver detalhes do certificado para um dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. Se alterou o certificado para o Web UI local, precisa reiniciar o navegador e, em seguida, a UI web local. Este passo é necessário para evitar quaisquer problemas de cache SSL.

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

7. Instale o novo certificado no computador cliente que está a usar para aceder à UI web local. Para obter instruções, consulte [certificados de importação ao cliente,](#import-certificates-to-client)abaixo.


## <a name="import-certificates-to-client"></a>Certificados de importação ao cliente

Depois de adicionar um certificado ao seu dispositivo Data Box, tem de importar o certificado para o computador cliente que utiliza para aceder ao dispositivo. Importará o certificado para a loja Trusted Root Certificate Authority para máquina local.

Para importar um certificado num cliente Windows, siga estes passos:

1. No Explorador de Ficheiros, clique com o botão direito no ficheiro do certificado (com `.cer` formato) e selecione **o certificado de instalação**. Esta ação inicia o Certificado De Importação De Feiticeiro.

    ![Certificado de importação 1](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. Para **localizar a Loja**, selecione Máquina **Local** e, em seguida, selecione **Seguinte**.

    ![Selecione a Máquina Local como a localização da loja no Assistente de Importação de Certificados](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. Selecione **Coloque todos os certificados na loja seguinte,** selecione **Trusted Root Certificate Authority** e, em seguida, selecione **Next**.

   ![Selecione a loja de autoridades de certificação de raiz fidedigna no assistente de importação de certificados](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. Reveja as suas definições e **selecione Terminar**. Uma mensagem dir-lhe-á que a importação foi bem sucedida.

   ![Reveja as definições do certificado e termine o Certificado De Importação](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>Reverter para certificados de incumprimento

Antes de devolver o seu dispositivo ao datacenter Azure, deverá reverter para os certificados originais que foram gerados durante o processamento da encomenda.

Para reverter para os certificados gerados durante o processamento da encomenda, siga estes passos:

1. Vá a **Gerir**  >  **Certificados** e selecione **certificados Reverter**.

   Os certificados revertidos voltam a utilizar os certificados auto-assinados que foram gerados durante o processamento da encomenda. Os seus próprios certificados são removidos do dispositivo.

   ![A opção de certificados reverte em Certificados de Gestão para um dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. Após a reversão do certificado concluída com sucesso, vá para **desligar ou reiniciar**, e selecione **Restart**. Este passo é necessário para evitar quaisquer problemas de cache SSL.

   ![Desligue ou reinicie a UI web local após reverter os certificados num dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   Espere alguns minutos, e depois inscreva-se na internet local de novo.
