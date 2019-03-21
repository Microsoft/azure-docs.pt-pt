---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: d8e249a788d80108d46a61f316b795ab04e541a2
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203549"
---
## <a name="setting-up-your-ios-application"></a>Configurar a sua aplicação iOS

Esta secção fornece instruções passo a passo para criar um novo projeto para demonstrar como integrar uma aplicação iOS (Swift) com *início de sessão com a Microsoft* que ele possa consultar as APIs da Web que exigem um token.

> Prefere transferir o projeto do XCode este exemplo em vez disso? [Transfira um projeto](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip) e avance para o [passo de configuração](#register-your-application) para configurar o exemplo de código antes de executar.

## <a name="install-carthage-to-download-and-build-msal"></a>Instalar Carthage para transferir e criar MSAL

Gestor de pacotes de Carthage é utilizada durante o período de pré-visualização da MSAL – ele se integra com o XCode, mantendo a capacidade para a Microsoft fazer alterações à biblioteca.

- Transferir e instalar a versão mais recente do Carthage [aqui](https://github.com/Carthage/Carthage/releases "URL de transferência de Carthage").

## <a name="creating-your-application"></a>Criação da sua aplicação

1. Abra o Xcode e selecione **criar um novo projeto do Xcode**.
2. Selecione **iOS > aplicação de vista única** e selecione **próxima**.
3. Dê um nome de produto e selecione **seguinte**.
4. Selecione uma pasta para criar a sua aplicação e clique em *Create*

## <a name="build-the-msal-framework"></a>Criar a estrutura MSAL

Siga as instruções abaixo para extrair e, em seguida, criar a versão mais recente das bibliotecas MSAL usando Carthage:

1. Abra o terminal bash e aceda à pasta de raiz da aplicação.
2. Copiar o abaixo e cole no terminal para criar um ficheiro de 'Cartfile' bash:

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
Copie e cole o abaixo. Este comando obtém as dependências para uma pasta de Carthage/Checkouts, em seguida, baseia-se a biblioteca MSAL:
</li>
</ol>

```bash
carthage update
```

> O processo acima é utilizado para transferir e criar a biblioteca de autenticação da Microsoft (MSAL). A MSAL lida com a aquisição, colocação em cache e atualizar os tokens de utilizador utilizados para aceder a APIs protegidas o v2.0 do Azure Active Directory.

## <a name="add-the-msal-framework-to-your-application"></a>Adicionar a estrutura MSAL à sua aplicação

1. No Xcode, abra a **gerais** separador.
2. Vá para o **estruturas e bibliotecas ligadas** secção e selecione **+**.
3. Selecione **adicionar outro...** .
4. Selecione **Carthage > criar > iOS > MSAL.framework** e, em seguida, selecione **aberto**. Deverá ver `MSAL.framework` adicionado à lista.
5. Vá para o **fases de criação** separador, selecione a **+** ícone e, em seguida, selecione **nova fase Script execute**.
6. Adicione o seguinte conteúdo para o *área do script*:

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Adicione o seguinte ao <code>Input Files</code> ao clicar em <code>+</code>:
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>Criar a IU da sua aplicação

Um ficheiro de Main.storyboard deve ser criado automaticamente como parte do seu modelo de projeto. Siga as instruções abaixo para criar a IU da aplicação:

1.  Controlar + clique `Main.storyboard` para abrir o menu contextual e, em seguida, clique em: `Open As` > `Source Code`
2.  Substitua o `<scenes>` nó com o código a seguir:

```xml
 <scenes>
    <scene sceneID="tne-QT-ifu">
        <objects>
            <viewController id="BYZ-38-t0r" customClass="ViewController" customModule="MSALiOS" customModuleProvider="target" sceneMemberID="viewController">
                <layoutGuides>
                    <viewControllerLayoutGuide type="top" id="y3c-jy-aDJ"/>
                    <viewControllerLayoutGuide type="bottom" id="wfy-db-euE"/>
                </layoutGuides>
                <view key="view" contentMode="scaleToFill" id="8bC-Xf-vdC">
                    <rect key="frame" x="0.0" y="0.0" width="375" height="667"/>
                    <autoresizingMask key="autoresizingMask" widthSizable="YES" heightSizable="YES"/>
                    <subviews>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Microsoft Authentication Library" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="ifd-fu-zjm">
                            <rect key="frame" x="64" y="28" width="246" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Logging" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="98g-dc-BPL">
                            <rect key="frame" x="16" y="277" width="62" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="2rX-Vv-T1i">
                            <rect key="frame" x="87" y="100" width="200" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Call Microsoft Graph API"/>
                            <connections>
                                <action selector="callGraphButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="Kx0-JL-Bv9"/>
                            </connections>
                        </button>
                        <textView clipsSubviews="YES" multipleTouchEnabled="YES" contentMode="scaleToFill" fixedFrame="YES" editable="NO" textAlignment="natural" selectable="NO" translatesAutoresizingMaskIntoConstraints="NO" id="qXW-2z-J7K">
                            <rect key="frame" x="16" y="324" width="343" height="291"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <color key="backgroundColor" white="1" alpha="1" colorSpace="calibratedWhite"/>
                            <accessibility key="accessibilityConfiguration">
                                <accessibilityTraits key="traits" updatesFrequently="YES"/>
                            </accessibility>
                            <fontDescription key="fontDescription" type="system" pointSize="14"/>
                            <textInputTraits key="textInputTraits" autocapitalizationType="sentences"/>
                        </textView>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="9u4-b8-vmX">
                            <rect key="frame" x="137" y="138" width="100" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Sign Out"/>
                            <connections>
                                <action selector="signoutButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="kZT-P8-0Zy"/>
                            </connections>
                        </button>
                    </subviews>
                    <color key="backgroundColor" red="1" green="1" blue="1" alpha="1" colorSpace="custom" customColorSpace="sRGB"/>
                </view>
                <connections>
                    <outlet property="loggingText" destination="qXW-2z-J7K" id="uqO-Yw-AsK"/>
                    <outlet property="signoutButton" destination="9u4-b8-vmX" id="OCh-qk-ldv"/>
                </connections>
            </viewController>
            <placeholder placeholderIdentifier="IBFirstResponder" id="dkx-z0-nzr" sceneMemberID="firstResponder"/>
        </objects>
        <point key="canvasLocation" x="140" y="137.18140929535232"/>
    </scene>
</scenes>
```
