---
title: Como gerar e transferir chaves protegidas por HSM para Azure Key Vault Azure Key Vault | Microsoft Docs
description: Use este artigo para ajudá-lo a planejar, gerar e transferir suas próprias chaves protegidas por HSM para usar com o Azure Key Vault. Também conhecido como BYOK ou traga sua própria chave.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 3cd8cd0b72f1b3ccea557ce0e12394081329dc5b
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883298"
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Como gerar e transferir chaves protegidas por HSM para Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para garantia adicional, ao usar Azure Key Vault, você pode importar ou gerar chaves em módulos de segurança de hardware (HSMs) que nunca deixam o limite do HSM. Esse cenário é geralmente chamado de *traga sua própria chave*, ou BYOK. Os HSMs têm a certificação FIPS 140-2 de nível 2 validada. Azure Key Vault usa a família de HSMs nCipher nShield para proteger suas chaves.

Use as informações neste tópico para ajudá-lo a planejar, gerar e transferir suas próprias chaves protegidas por HSM para usar com o Azure Key Vault.

Essa funcionalidade não está disponível para o Azure China.

> [!NOTE]
> Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](key-vault-whatis.md)  
> Para obter um tutorial de introdução, que inclui a criação de um cofre de chaves para chaves protegidas por HSM, consulte [o que é Azure Key Vault?](key-vault-overview.md).

Mais informações sobre como gerar e transferir uma chave protegida por HSM pela Internet:

* Você gera a chave de uma estação de trabalho offline, o que reduz a superfície de ataque.
* A chave é criptografada com uma chave de troca de chaves (KEK), que permanece criptografada até ser transferida para o Azure Key Vault HSMs. Somente a versão criptografada da sua chave deixa a estação de trabalho original.
* O conjunto de ferramentas define as propriedades em sua chave de locatário que associa sua chave ao mundo de segurança Azure Key Vault. Assim, depois que os HSMs Azure Key Vault recebem e descriptografam sua chave, somente esses HSMs podem usá-lo. Sua chave não pode ser exportada. Essa associação é imposta pelos HSMs nCipher.
* A chave de troca de chave (KEK) que é usada para criptografar sua chave é gerada dentro do Azure Key Vault HSMs e não é exportável. Os HSMs impõem que não pode haver nenhuma versão clara do KEK fora dos HSMs. Além disso, o conjunto de ferramentas inclui o atestado de nCipher que o KEK não é exportável e foi gerado dentro de um HSM autêntico que foi fabricado pelo nCipher.
* O conjunto de ferramentas inclui atestado de nCipher que o mundo de segurança de Azure Key Vault também foi gerado em um HSM autêntico fabricado pelo nCipher. Esse atestado comprova que a Microsoft está usando hardware genuíno.
* A Microsoft usa KEKs separadas e separa os mundos de segurança em cada região geográfica. Essa separação garante que a chave possa ser usada somente em data centers na região em que você a criptografou. Por exemplo, uma chave de um cliente Europeu não pode ser usada em data centers na América do Norte ou na Ásia.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Mais informações sobre HSMs nCipher e serviços da Microsoft

a nCipher Security é um provedor global líder de criptografia de dados e soluções de segurança cibernéticos para os setores de serviços financeiros, alta tecnologia, manufatura, governo e tecnologia. Com um histórico de 40 anos de proteção de informações corporativas e governamentais, as soluções de criptografia de segurança do nCipher são usadas por quatro das cinco maiores empresas de energia e aeroespacial. Suas soluções também são usadas por 22 países/regiões OTAN e protegem mais de 80 por cento de transações de pagamento mundiais.

A Microsoft colaborau com a nCipher Security para aprimorar o estado da arte para HSMs. Esses aprimoramentos permitem que você obtenha os benefícios típicos dos serviços hospedados sem ceder o controle sobre suas chaves. Especificamente, esses aprimoramentos permitem que a Microsoft gerencie os HSMs para que você não precise fazê-lo. Como um serviço de nuvem, o Azure Key Vault é dimensionado em breve aviso para atender aos picos de uso da sua organização. Ao mesmo tempo, sua chave é protegida dentro dos HSMs da Microsoft: Você mantém o controle sobre o ciclo de vida da chave porque gera a chave e a transfere para os HSMs da Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementando traga sua própria chave (BYOK) para Azure Key Vault

Use as seguintes informações e procedimentos se você gerar sua própria chave protegida por HSM e, em seguida, transferi-la para Azure Key Vault — o cenário traga sua própria chave (BYOK).

## <a name="prerequisites-for-byok"></a>Pré-requisitos para BYOK

Consulte a tabela a seguir para obter uma lista de pré-requisitos para BYOK (traga sua própria chave) para Azure Key Vault.

| Requisito | Mais informações |
| --- | --- |
| Uma assinatura do Azure |Para criar um Azure Key Vault, você precisa de uma assinatura do Azure: [Inscreva-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) |
| A camada de serviço do Azure Key Vault Premium para dar suporte a chaves protegidas por HSM |Para obter mais informações sobre as camadas de serviço e os recursos para Azure Key Vault, consulte o site de [preços de Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) . |
| nCipher nShield HSMs, cartões inteligentes e software de suporte |Você deve ter acesso a um módulo de segurança de hardware nCipher e o conhecimento operacional básico de nCipher nShield HSMs. Consulte [módulo de segurança de hardware do NCipher nShield](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) para obter a lista de modelos compatíveis ou para comprar um HSM se você não tiver um. |
| O hardware e o software a seguir:<ol><li>Uma estação de trabalho x64 offline com um sistema operacional Windows mínimo do Windows 7 e do nCipher nShield software que seja pelo menos a versão 11,50.<br/><br/>Se essa estação de trabalho executar o Windows 7, você deverá [instalar o Microsoft .NET Framework 4,5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Uma estação de trabalho que está conectada à Internet e tem um sistema operacional Windows mínimo do Windows 7 e [Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) **versão mínima do 1.1.0** instalada.</li><li>Uma unidade USB ou outro dispositivo de armazenamento portátil que tenha pelo menos 16 MB de espaço livre.</li></ol> |Por motivos de segurança, recomendamos que a primeira estação de trabalho não esteja conectada a uma rede. No entanto, essa recomendação não é imposta programaticamente.<br/><br/>Nas instruções a seguir, essa estação de trabalho é conhecida como a estação de trabalho desconectada.</p></blockquote><br/>Além disso, se sua chave de locatário for para uma rede de produção, recomendamos que você use uma segunda estação de trabalho separada para baixar o conjunto de ferramentas e carregar a chave de locatário. Mas, para fins de teste, você pode usar a mesma estação de trabalho que a primeira.<br/><br/>Nas instruções a seguir, essa segunda estação de trabalho é chamada de estação de trabalho conectada à Internet.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Gerar e transferir sua chave para Azure Key Vault HSM

Você usará as cinco etapas a seguir para gerar e transferir sua chave para um Azure Key Vault HSM:

* [Passo 1: Prepare sua estação de trabalho conectada à Internet](#step-1-prepare-your-internet-connected-workstation)
* [Passo 2: Preparar sua estação de trabalho desconectada](#step-2-prepare-your-disconnected-workstation)
* [Passo 3: Gerar sua chave](#step-3-generate-your-key)
* [Passo 4: Preparar sua chave para transferência](#step-4-prepare-your-key-for-transfer)
* [Passo 5: Transferir sua chave para Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Passo 1: Prepare sua estação de trabalho conectada à Internet

Para essa primeira etapa, execute os procedimentos a seguir em sua estação de trabalho que está conectada à Internet.

### <a name="step-11-install-azure-powershell"></a>Etapa 1,1: Instalar o Azure PowerShell

Na estação de trabalho conectada à Internet, baixe e instale o módulo Azure PowerShell que inclui os cmdlets para gerenciar Azure Key Vault. Para obter instruções de instalação, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Etapa 1,2: Obter sua ID de assinatura do Azure

Inicie uma sessão de Azure PowerShell e entre em sua conta do Azure usando o seguinte comando:

```Powershell
   Connect-AzAccount
```
Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. Em seguida, use o comando [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) :

```powershell
   Get-AzSubscription
```
Na saída, localize a ID da assinatura que será usada para Azure Key Vault. Você precisará dessa ID de assinatura mais tarde.

Não feche a janela de Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Etapa 1,3: Baixe o conjunto de ferramentas BYOK para Azure Key Vault

Vá para o centro de download da Microsoft e [Baixe o conjunto de ferramentas Azure Key Vault BYOK](https://www.microsoft.com/download/details.aspx?id=45345) para sua região geográfica ou instância do Azure. Use as informações a seguir para identificar o nome do pacote a ser baixado e seu hash de pacote SHA-256 correspondente:

---
**Estados Unidos:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Européia**

KeyVault-BYOK-Tools-Europe. zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Centro**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**América Latina:**

KeyVault-BYOK-Tools-LatinAmerica. zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Japão:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Coréia:**

KeyVault-BYOK-Tools-Korea. zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**África do Sul:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**DOS EAU**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Austrália:**

KeyVault-BYOK-Tools-Australia. zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure governamental:** ](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**Governo dos EUA DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Canadá:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Alemanha:**

KeyVault-BYOK-Tools-Germany. zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Público da Alemanha:**

KeyVault-BYOK-Tools-Germany-Public. zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**Índia:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**França:**

KeyVault-BYOK-Tools-France. zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Reino Unido:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Suíça:**

KeyVault-BYOK-Tools-Switzerland. zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


Para validar a integridade do conjunto de ferramentas BYOK baixado, na sessão Azure PowerShell, use o cmdlet [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) .

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

O conjunto de ferramentas inclui:

* Um pacote KEK (chave de troca de chaves) que tem um nome que começa com **BYOK-Kek-pkg-.**
* Um pacote do Security World que tem um nome que começa com **BYOK-SecurityWorld-pkg-.**
* Um script Python chamado **verifykeypackage.py.**
* Um arquivo executável de linha de comando chamado **KeyTransferRemote. exe** e DLLs associadas.
* Um pacote C++ redistribuível do Visual, chamado **VCRedist_x64. exe.**

Copie o pacote para uma unidade USB ou outro armazenamento portátil.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Passo 2: Preparar sua estação de trabalho desconectada

Para essa segunda etapa, execute os procedimentos a seguir na estação de trabalho que não está conectada a uma rede (a Internet ou sua rede interna).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Etapa 2,1: Preparar a estação de trabalho desconectada com o nCipher nShield HSM

Instale o software de suporte do nCipher em um computador Windows e, em seguida, anexe um nCipher nShield HSM a esse computador.

Verifique se as ferramentas nCipher estão em seu caminho ( **%nfast_home%\bin**). Por exemplo, digite o seguinte:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Para obter mais informações, consulte o guia do usuário incluído no nShield HSM.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Etapa 2,2: Instalar o conjunto de ferramentas BYOK na estação de trabalho desconectada

Copie o pacote do conjunto de ferramentas BYOK da unidade USB ou de outro armazenamento portátil e, em seguida, faça o seguinte:

1. Extraia os arquivos do pacote baixado em qualquer pasta.
2. Nessa pasta, execute VCRedist_x64. exe.
3. Siga as instruções para instalar os componentes de C++ tempo de execução Visual para Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Passo 3: Gerar sua chave

Para essa terceira etapa, execute os procedimentos a seguir na estação de trabalho desconectada. Para concluir esta etapa, seu HSM deve estar no modo de inicialização. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Etapa 3,1: Alterar o modo HSM para ' I '

Se você estiver usando o nCipher nShield Edge, para alterar o modo: 1. Use o botão modo para realçar o modo necessário. 2. Em alguns segundos, pressione e segure o botão limpar por alguns segundos. Se o modo for alterado, o LED do novo modo parará de piscar e permanecerá aceso. O LED de status pode piscar irregularmente por alguns segundos e, em seguida, piscará regularmente quando o dispositivo estiver pronto. Caso contrário, o dispositivo permanecerá no modo atual, com o LED de modo apropriado aceso.

### <a name="step-32-create-a-security-world"></a>Etapa 3,2: Criar um mundo de segurança

Inicie um prompt de comando e execute o programa New-World do nCipher.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Este programa cria um arquivo do **mundo de segurança** em%NFAST_KMDATA%\local\world, que corresponde à pasta C:\ProgramData\nCipher\Key Management data\local. Você pode usar valores diferentes para o quorum, mas, em nosso exemplo, será solicitado que você insira três cartões e pinos em branco para cada um. Em seguida, quaisquer dois cartões dão acesso completo ao mundo de segurança. Esses cartões se tornam o **conjunto de cartões do administrador** para o novo mundo de segurança.

> [!NOTE]
> Se o HSM não oferecer suporte ao criptografia do pacote de DLf3072s256mRijndael mais recente, você poderá substituir--Cipher-Suite = DLf3072s256mRijndael por--Cipher-Suite = DLf1024s160mRijndael

Em seguida, faça o seguinte:

* Faça backup do arquivo do mundo. Proteja e proteja o arquivo do mundo, os cartões de administrador e seus PINs e certifique-se de que nenhuma pessoa tenha acesso a mais de um cartão.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Etapa 3,3: Alterar o modo HSM para ' O '

Se você estiver usando o nCipher nShield Edge, para alterar o modo: 1. Use o botão modo para realçar o modo necessário. 2. Em alguns segundos, pressione e segure o botão limpar por alguns segundos. Se o modo for alterado, o LED do novo modo parará de piscar e permanecerá aceso. O LED de status pode piscar irregularmente por alguns segundos e, em seguida, piscará regularmente quando o dispositivo estiver pronto. Caso contrário, o dispositivo permanecerá no modo atual, com o LED de modo apropriado aceso.

### <a name="step-34-validate-the-downloaded-package"></a>Etapa 3,4: Validar o pacote baixado

Esta etapa é opcional, mas recomendada para que você possa validar o seguinte:

* A chave de troca de chave que está incluída no conjunto de ferramentas foi gerada de um nCipher nShield HSM autêntico.
* O hash do mundo de segurança que está incluído no conjunto de ferramentas foi gerado em um nCipher nShield HSM autêntico.
* A chave de troca de chaves é não exportável.

> [!NOTE]
> Para validar o pacote baixado, o HSM deve estar conectado, ligado e deve ter um mundo de segurança nele (como aquele que você acabou de criar).

Para validar o pacote baixado:

1. Execute o script verifykeypackage.py digitando um dos seguintes, dependendo da sua região geográfica ou instância do Azure:

   * Por América do Norte:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Para a Europa:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Para a Ásia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Por América Latina:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Para o Japão:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Para a Coreia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Para a África do Sul:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * Para dos EAU:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * Para a Austrália:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Para o [Azure governamental](https://azure.microsoft.com/features/gov/), que usa a instância do governo dos EUA do Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Para o governo dos EUA DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Para o Canadá:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Para a Alemanha:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Para a Alemanha pública:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Para a Índia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Para a França:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Para o Reino Unido:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
   * Para a Suíça:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1

     > [!TIP]
     > O software nCipher nShield inclui Python em%NFAST_HOME%\python\bin
     >
     >
2. Confirme que você vê o seguinte, que indica a validação bem-sucedida: **Disso ÊXITO**

Esse script valida a cadeia de signatários até a chave raiz nShield. O hash dessa chave raiz é inserido no script e seu valor deve ser **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Você também pode confirmar esse valor separadamente visitando o [site do nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation).

Agora você está pronto para criar uma nova chave.

### <a name="step-35-create-a-new-key"></a>Etapa 3,5: Criar uma chave nova

Gere uma chave usando o programa nCipher nShield **GenerateKey** .

Execute o seguinte comando para gerar a chave:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Ao executar esse comando, use estas instruções:

* O parâmetro *Protect* deve ser definido como o **módulo**Value, conforme mostrado. Isso cria uma chave protegida por módulo. O conjunto de ferramentas BYOK não oferece suporte a chaves protegidas por OCS.
* Substitua o valor de *contosokey pelo* para o **ident** e **Plains** por qualquer valor de cadeia de caracteres. Para minimizar as sobrecargas administrativas e reduzir o risco de erros, recomendamos que você use o mesmo valor para ambos. O valor **ident** deve conter apenas números, traços e letras minúsculas.
* O pubexp é deixado em branco (padrão) neste exemplo, mas você pode especificar valores específicos. Para obter mais informações, consulte a [documentação do nCipher.](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

Esse comando cria um arquivo de chave de token em sua pasta%NFAST_KMDATA%\local com um nome começando com **key_simple_** , seguido pelo **ident** que foi especificado no comando. Por exemplo: **key_simple_contosokey**. Este arquivo contém uma chave criptografada.

Faça backup desse arquivo de chave de token em um local seguro.

> [!IMPORTANT]
> Quando você transfere posteriormente sua chave para Azure Key Vault, a Microsoft não pode exportar essa chave de volta para você, portanto, é extremamente importante que você faça backup da sua chave e do mundo de segurança com segurança. Entre em contato com [nCipher](https://www.ncipher.com/about-us/contact-us) para obter diretrizes e práticas recomendadas para fazer backup de sua chave.
>


Agora você está pronto para transferir sua chave para Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Passo 4: Preparar sua chave para transferência

Para essa quarta etapa, execute os procedimentos a seguir na estação de trabalho desconectada.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Etapa 4,1: Criar uma cópia de sua chave com permissões reduzidas

Abra um novo prompt de comando e altere o diretório atual para o local onde você descompactou o arquivo zip BYOK. Para reduzir as permissões em sua chave, em um prompt de comando, execute um dos seguintes, dependendo da sua região geográfica ou instância do Azure:

* Por América do Norte:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Para a Europa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Para a Ásia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Por América Latina:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Para o Japão:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Para a Coreia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Para a África do Sul:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* Para dos EAU:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* Para a Austrália:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Para o [Azure governamental](https://azure.microsoft.com/features/gov/), que usa a instância do governo dos EUA do Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Para o governo dos EUA DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Para o Canadá:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Para a Alemanha:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Para a Alemanha pública:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Para a Índia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Para a França:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Para o Reino Unido:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* Para a Suíça:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

Quando você executar esse comando, substitua *contosokey pelo* com o mesmo valor especificado na **etapa 3,5: Crie uma nova chave** na etapa [gerar sua chave](#step-3-generate-your-key) .

Você será solicitado a conectar seus cartões de administração do mundo de segurança.

Quando o comando for concluído, você verá **resultado: O** sucesso e a cópia da sua chave com permissões reduzidas estão no arquivo chamado\<key_xferacId_ contosokey pelo >.

Você pode inspecionar as ACLS usando os seguintes comandos usando os utilitários nCipher nShield:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Ao executar esses comandos, substitua contosokey pelo com o mesmo valor especificado na **etapa 3,5: Crie uma nova chave** na etapa [gerar sua chave](#step-3-generate-your-key) .

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Etapa 4,2: Criptografar sua chave usando a chave de troca de chaves da Microsoft

Execute um dos seguintes comandos, dependendo de sua região geográfica ou instância do Azure:

* Por América do Norte:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Europa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Ásia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Por América Latina:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o Japão:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Coreia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a África do Sul:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para dos EAU:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Austrália:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o [Azure governamental](https://azure.microsoft.com/features/gov/), que usa a instância do governo dos EUA do Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o governo dos EUA DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o Canadá:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Alemanha:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Alemanha pública:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Índia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a França:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o Reino Unido:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Suíça:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Ao executar esse comando, use estas instruções:

* Substitua *contosokey pelo* pelo identificador que você usou para gerar a chave na **etapa 3,5: Crie uma nova chave** na etapa [gerar sua chave](#step-3-generate-your-key) .
* Substitua *SubscriptionId* pela ID da assinatura do Azure que contém o cofre de chaves. Você recuperou esse valor anteriormente, **na etapa 1,2: Obtenha sua ID** de assinatura do Azure na etapa [preparar sua estação de trabalho conectada à Internet](#step-1-prepare-your-internet-connected-workstation) .
* Substitua *ContosoFirstHSMKey* por um rótulo que é usado para o nome do arquivo de saída.

Quando isso for concluído com êxito, ele exibirá **o resultado: Êxito** e há um novo arquivo na pasta atual que tem o seguinte nome: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Etapa 4,3: Copie o pacote de transferência de chave para a estação de trabalho conectada à Internet

Use uma unidade USB ou outro armazenamento portátil para copiar o arquivo de saída da etapa anterior (KeyTransferPackage-ContosoFirstHSMkey. byok) para sua estação de trabalho conectada à Internet.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Passo 5: Transferir sua chave para Azure Key Vault

Para essa etapa final, na estação de trabalho conectada à Internet, use o cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) para carregar o pacote de transferência de chave que você copiou da estação de trabalho desconectada para o Azure Key Vault HSM:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Se o upload for bem-sucedido, você verá exibindo as propriedades da chave que você acabou de adicionar.

## <a name="next-steps"></a>Passos Seguintes

Agora você pode usar essa chave protegida por HSM em seu cofre de chaves. Para obter mais informações, consulte esta [comparação](https://azure.microsoft.com/pricing/details/key-vault/)de preço e recurso.
