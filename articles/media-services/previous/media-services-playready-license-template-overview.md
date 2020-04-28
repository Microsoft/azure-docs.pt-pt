---
title: Visão geral do modelo de licença PlayReady de serviços de mídia
description: Este tópico dá uma visão geral de um modelo de licença PlayReady que é usado para configurar licenças PlayReady.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: fddce5d0-1278-478f-ae05-9b985c748731
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: fb0630f3f7a4acebcfe5dcad343030c9d1184263
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74976405"
---
# <a name="media-services-playready-license-template-overview"></a>Visão geral do modelo de licença PlayReady de serviços de mídia
A Azure Media Services oferece agora um serviço para a entrega de licenças PlayReady. Quando o jogador (por exemplo, Silverlight) tenta reproduzir o seu conteúdo protegido pela PlayReady, um pedido é enviado para o serviço de entrega de licença para obter uma licença. Se o serviço de licença aprovar o pedido, emite a licença que é enviada ao cliente e é usada para desencriptar e reproduzir o conteúdo especificado.

A Media Services também fornece APIs que pode usar para configurar as suas licenças PlayReady. As licenças contêm os direitos e restrições que pretende que o tempo de execução da gestão de direitos digitais playReady (DRM) aplique quando um utilizador tenta reproduzir conteúdo protegido.
Aqui estão alguns exemplos de restrições de licença PlayReady que pode especificar:

* A data e a hora a partir da qual a licença é válida.
* O valor DateTime quando a licença expirar. 
* Para que a licença seja guardada em armazenamento persistente no cliente. As licenças persistentes são normalmente usadas para permitir a reprodução offline do conteúdo.
* O nível de segurança mínimo que um jogador deve ter de reproduzir o seu conteúdo. 
* O nível de proteção de saída para os controlos de saída para o conteúdo áudio\vídeo. 
* Para mais informações, consulte a secção "Controlos de Saída" (3.5) no documento [PlayReady Compliance Rules.](https://www.microsoft.com/playready/licensing/compliance/)

> [!NOTE]
> Atualmente, só é possível configurar a playRight da licença PlayReady. Este direito é necessário. O PlayRight dá ao cliente a capacidade de reproduzir o conteúdo. Também pode utilizar o PlayRight para configurar restrições específicas à reprodução. Para mais informações, consulte [PlayReadyPlayRight](media-services-playready-license-template-overview.md#PlayReadyPlayRight).
> 
> 

Para configurar as licenças PlayReady utilizando os Serviços de Media, tem de configurar o modelo de licença PlayReady dos Serviços de Media Services. O modelo é definido em XML.

O exemplo seguinte mostra o modelo mais simples (e mais comum) que configura uma licença de streaming básica. Com esta licença, os seus clientes podem reproduzir o seu conteúdo protegido pela PlayReady.

    <?xml version="1.0" encoding="utf-8"?>
    <PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" 
                                      xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <PlayRight />
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>

O XML está em conformidade com o modelo de licença PlayReady XML schema definido na secção "Modelo de licença PlayReady XML schema".

A Media Services também define um conjunto de classes .NET que podem ser usadas para serializar e desserializar de e para o XML. Para obter uma descrição das classes principais, consulte as [classes Media Services .NET](media-services-playready-license-template-overview.md#classes) que são usadas para configurar modelos de licença.

Para um exemplo de ponta a ponta que utiliza classes .NET para configurar o modelo de licença PlayReady, consulte [A encriptação dinâmica Use PlayReady e o serviço](media-services-protect-with-playready-widevine.md)de entrega de licenças .

## <a name="media-services-net-classes-that-are-used-to-configure-license-templates"></a><a id="classes"></a>Media Services .NET classes que são usadas para configurar modelos de licença
As seguintes aulas são as classes principais .NET que são usadas para configurar os modelos de licença PlayReady dos Media Services. Estas classes mapeiam para os tipos definidos no modelo de [licença PlayReady XML schema](media-services-playready-license-template-overview.md#schema).

A classe [MediaServicesLicenseTemplateSerializer](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.mediaserviceslicensetemplateserializer.aspx) é usada para serializar e desserializar de e para o modelo de licença de Media Services XML.

### <a name="playreadylicenseresponsetemplate"></a>Modelo de resposta playreadylicense
[Modelo de resposta playReadyLicense](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicenseresponsetemplate.aspx): Esta classe representa o modelo para a resposta enviada de volta para o utilizador. Contém um campo para uma cadeia de dados personalizada entre o servidor de licença e a aplicação (que pode ser útil para a lógica de aplicação personalizada). Também contém uma lista de um ou mais modelos de licença.

Como a classe "de alto nível" na hierarquia do modelo, o modelo de resposta inclui uma lista de modelos de licença. Os modelos de licença incluem (direta ou indiretamente) todas as outras classes que compõem os dados do modelo a serem serializados.

### <a name="playreadylicensetemplate"></a>Modelo de playreadylicense
[PlayReadyLicenseTemplate](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicensetemplate.aspx): Esta classe representa um modelo de licença que é usado para criar licenças PlayReady para serem devolvidas aos utilizadores. Contém os dados da chave de conteúdo da licença. Também inclui quaisquer direitos ou restrições que o tempo de funcionação do REM PlayReady deve impor quando a chave de conteúdo é utilizada.

### <a name="playreadyplayright"></a><a id="PlayReadyPlayRight"></a>PlayReadyPlayRight
[PlayReadyPlayRight](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadyplayright.aspx): Esta classe representa o PlayRight de uma licença PlayReady. Concede ao utilizador a capacidade de reproduzir os conteúdos sujeitos a quaisquer restrições configuradas na licença e no próprio PlayRight (para uma política específica de reprodução). Grande parte da política de um PlayRight diz respeito a restrições de saída que controlam os tipos de saídas que o conteúdo pode ser reproduzido. Inclui também quaisquer restrições que devem ser aplicadas quando uma determinada saída é utilizada. Por exemplo, se o DigitalVideoOnlyContentRestriction estiver ativado, o tempo de execução do DRM apenas permite que o vídeo seja exibido sobre as saídas digitais. (As saídas de vídeo analógicas não estão autorizadas a passar o conteúdo.)

> [!IMPORTANT]
> Este tipo de restrições pode ser poderosa, mas também pode afetar a experiência do consumidor. Se as proteções de saída forem demasiado restritivas, o conteúdo pode ser irrepreensível em alguns clientes. Para mais informações, consulte as Regras de [Conformidade PlayReady](https://www.microsoft.com/playready/licensing/compliance/).
> 
> 

Para um exemplo dos níveis de proteção que silverlight suporta, consulte o [suporte silverlight para proteções](https://go.microsoft.com/fwlink/?LinkId=617318)de saída .

## <a name="playready-license-template-xml-schema"></a><a id="schema"></a>Esquema de modelo de licença PlayReady XML
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
      <xs:complexType name="AgcAndColorStripeRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
      <xs:simpleType name="ContentType">
        <xs:restriction base="xs:string">
          <xs:enumeration value="Unspecified" />
          <xs:enumeration value="UltravioletDownload" />
          <xs:enumeration value="UltravioletStreaming" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
      <xs:complexType name="ExplicitAnalogTelevisionRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
      <xs:complexType name="PlayReadyContentKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
      <xs:complexType name="ContentEncryptionKeyFromHeader">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:PlayReadyContentKey">
            <xs:sequence />
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
      <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:PlayReadyContentKey">
            <xs:sequence>
              <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
      <xs:complexType name="PlayReadyLicenseResponseTemplate">
        <xs:sequence>
          <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
          <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
      <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
      <xs:complexType name="PlayReadyLicenseTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
          <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
          <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
          <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
      <xs:simpleType name="PlayReadyLicenseType">
        <xs:restriction base="xs:string">
          <xs:enumeration value="Nonpersistent" />
          <xs:enumeration value="Persistent" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
      <xs:complexType name="PlayReadyPlayRight">
        <xs:sequence>
          <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
      <xs:simpleType name="UnknownOutputPassingOption">
        <xs:restriction base="xs:string">
          <xs:enumeration value="NotAllowed" />
          <xs:enumeration value="Allowed" />
          <xs:enumeration value="AllowedWithVideoConstriction" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
      <xs:complexType name="ScmsRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
    </xs:schema>


## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

