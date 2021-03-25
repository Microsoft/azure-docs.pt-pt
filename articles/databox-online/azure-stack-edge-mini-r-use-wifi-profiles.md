---
title: Utilize perfis de Wi-Fi com dispositivos Azure Stack Edge Mini R
description: Descreve como criar perfis Wi-Fi para dispositivos Azure Stack Edge Mini R em redes empresariais de alta segurança e redes pessoais.
services: databox
author: v-dalc@microsoft.com
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: 90c7c238cef104eae78618e51fa4b284adcc8f42
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050548"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Utilize perfis de Wi-Fi com dispositivos Azure Stack Edge Mini R

Este artigo descreve como utilizar perfis de rede sem fios (Wi-Fi) com os seus dispositivos Azure Stack Edge Mini R.

A forma como prepara o perfil Wi-Fi depende do tipo de rede sem fios:

- Numa Wi-Fi Protected Access 2 (WPA2) - Rede pessoal, como uma rede doméstica ou Wi-Fi hotspot aberto, poderá descarregar e utilizar um perfil sem fios existente com a mesma palavra-passe que utiliza com outros dispositivos.

- Num ambiente empresarial de alta segurança, você acederá ao seu dispositivo sobre uma rede WPA2 - Enterprise. Neste tipo de rede, cada computador cliente terá um perfil Wi-Fi distinto e será autenticado através de certificados. Terá de trabalhar com o administrador de rede para determinar a configuração necessária.

Discutiremos os requisitos de perfil para ambos os tipos de rede mais tarde.

Em qualquer dos casos, é muito importante certificar-se de que o perfil cumpre os requisitos de segurança da sua organização antes de testar ou usar o perfil com o seu dispositivo.

## <a name="about-wi-fi-profiles"></a>Sobre perfis de Wi-Fi

Um perfil Wi-Fi contém o SSID (identificador de conjunto de serviço, ou **nome de rede),** chave de senha e informações de segurança necessárias para ligar o seu dispositivo Azure Stack Edge Mini R a uma rede sem fios.

O exemplo de código a seguir mostra configurações básicas para um perfil a utilizar com uma rede sem fios típica:

* `SSID` é o nome da rede.

* `name` é o nome fácil de utilizar para a ligação Wi-Fi. É este o nome que os utilizadores vão ver quando navegam nas ligações disponíveis no seu dispositivo.

* O perfil está configurado para ligar automaticamente o computador à rede sem fios quando está dentro do alcance da rede ( `connectionMode`  =  `auto` ).

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

Para obter mais informações sobre as definições de perfil Wi-Fi, consulte **o perfil da Enterprise** nas [definições de Add Wi-Fi para dispositivos Windows 10 e dispositivos mais recentes](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile), e consulte o perfil de Wi-Fi da Cisco [configurar](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile).

Para ativar as ligações sem fios num dispositivo Azure Stack Edge Mini R, configurar a porta Wi-Fi do dispositivo e, em seguida, adicionar o perfil de Wi-Fi ao dispositivo. Numa rede empresarial, também irás enviar certificados para o dispositivo. Em seguida, pode ligar-se a uma rede sem fios a partir da UI web local para o dispositivo. Para obter mais informações, consulte [Gerir a conectividade sem fios no seu Azure Stack Edge Mini R](./azure-stack-edge-mini-r-manage-wifi.md).

## <a name="profile-for-wpa2---personal-network"></a>Perfil para WPA2 - Rede pessoal

Numa Wi-Fi Protected Access 2 (WPA2) - Rede pessoal, como uma rede doméstica ou Wi-Fi hotspot aberto, vários dispositivos podem usar o mesmo perfil e a mesma senha. Na sua rede doméstica, o seu telemóvel e portátil utilizam o mesmo perfil e senha sem fios para se ligar à rede.

Por exemplo, um cliente do Windows 10 pode gerar um perfil de tempo de execução para si. Quando entra na rede sem fios, é-lhe solicitado a Wi-Fi palavra-passe e, assim que fornece essa palavra-passe, está ligado. Não é necessário nenhum certificado neste ambiente.

Neste tipo de rede, poderá exportar um perfil de Wi-Fi do seu portátil e, em seguida, adicioná-lo ao seu dispositivo Azure Stack Edge Mini R. Para obter instruções, consulte [Export a Wi-Fi profile](#export-a-wi-fi-profile), abaixo.

> [!IMPORTANT]
> Antes de criar um perfil Wi-Fi para o seu dispositivo Azure Stack Edge Mini R, contacte o administrador da rede para saber os requisitos de segurança da organização para a rede sem fios. Não deve testar ou utilizar qualquer perfil Wi-Fi no seu dispositivo até saber que a rede sem fios cumpre os requisitos.

## <a name="profiles-for-wpa2---enterprise-network"></a>Perfis para WPA2 - Rede empresarial

Numa rede de acesso protegido sem fios 2 (WPA2) - Rede empresarial, terá de trabalhar com o administrador da rede para obter o perfil e certificado de Wi-Fi necessários para ligar o seu dispositivo Azure Stack Edge Mini R à rede.

Para redes altamente seguras, o dispositivo Azure pode utilizar o Protocolo de Autenticação Extensível Protegido (PEAP) com autenticação extensível Protocol-Transport Segurança da Camada (EAP-TLS). O PEAP com eAP-TLS utiliza a autenticação da máquina: o cliente e o servidor utilizam certificados para verificar as suas identidades entre si.

> [!NOTE]
> * A autenticação do utilizador utilizando o PROTOCOLO DE Autenticação de Aperto de Mão PEAP Microsoft Challenge, versão 2 (PEAP MSCHAPv2) não é suportada em dispositivos Azure Stack Edge Mini R.
> * A autenticação EAP-TLS é necessária para aceder à funcionalidade Azure Stack Edge Mini R. Uma ligação sem fios que configurar utilizando o Ative Directory não funcionará.

O administrador de rede gerará um perfil Wi-Fi único e um certificado de cliente para cada computador. O administrador da rede decide se utiliza um certificado separado para cada dispositivo ou um certificado partilhado.

Se trabalhar em mais de um local físico no local de trabalho, o administrador da rede poderá ter de fornecer mais de um perfil de Wi-Fi e certificado específicos do site para as suas ligações sem fios.

Numa rede empresarial, recomendamos que não altere as definições nos perfis Wi-Fi que o seu administrador de rede fornece. O único ajuste que pode querer fazer é com as definições de ligação automática. Para obter mais informações, consulte [o perfil básico](/mem/intune/configuration/wi-fi-settings-windows#basic-profile) nas definições Wi-Fi para windows 10 e dispositivos mais recentes.

Num ambiente empresarial de alta segurança, poderá utilizar um perfil de rede sem fios existente como modelo:

* Pode descarregar o perfil de rede sem fios corporativo do seu computador de trabalho. Para obter instruções, consulte [Export a Wi-Fi profile](#export-a-wi-fi-profile), abaixo.

* Se outros da sua organização já estiverem ligados aos seus dispositivos Azure Stack Edge Mini R por uma rede sem fios, podem descarregar o perfil Wi-Fi do seu dispositivo. Para obter instruções, consulte [baixar Wi-Fi perfil](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile).

## <a name="export-a-wi-fi-profile"></a>Exportar um perfil de Wi-Fi

Para exportar um perfil para a interface Wi-Fi no seu computador, faça estes passos:

1. Para ver os perfis sem fios no seu computador, no menu **Iniciar,** abra **o pedido de comando** (cmd.exe) e insira este comando:

   `netsh wlan show profiles`

   A saída será semelhante à seguinte:

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

2. Para exportar um perfil, insira o seguinte comando:

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>"`

   Por exemplo, o seguinte comando guarda o perfil ContosoFTINET no formato XML para a pasta Downloads para o utilizador denominado `gusp` .

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>Adicionar certificado, Wi-Fi perfil ao dispositivo

Quando tiver os perfis e certificados Wi-Fi de que necessita, faça estes passos para configurar o seu dispositivo Azure Stack Edge Mini R para ligações sem fios:

1. Para uma rede WPA2 - Enterprise, faça o upload dos certificados necessários para o dispositivo seguindo as orientações nos [certificados upload](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates).

1. Faça o upload do perfil de Wi-Fi para o dispositivo Mini R e, em seguida, conecte-o seguindo as orientações em [Add, ligue-se ao perfil Wi-Fi](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile).

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar a [rede para Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).
- Saiba como [gerir Wi-Fi no seu Azure Stack Edge Mini R](azure-stack-edge-mini-r-manage-wifi.md).
