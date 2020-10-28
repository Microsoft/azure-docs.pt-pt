---
title: Como gerar e transferir chaves protegidas pelo HSM para o Cofre da Chave Azure - Cofre da Chave Azure
description: Use este artigo para ajudá-lo a planear, gerar e, em seguida, transferir as suas próprias chaves protegidas pelo HSM para usar com o Cofre da Chave Azure. Também conhecido como BYOK ou traga a sua própria chave.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: f5d58f89aa87a39d12b2d6f6a3a91254a653a088
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92784665"
---
# <a name="import-hsm-protected-keys-for-key-vault-ncipher"></a>Chaves protegidas pelo HSM para o Cofre-Chave (nCipher)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para obter uma garantia adicional, quando utilizar o Azure Key Vault, pode importar ou gerar chaves em módulos de segurança de hardware (HSMs) que nunca saem do limite HSM. Este cenário costuma chamar-se *Bring Your Own Key* ou BYOK. O Azure Key Vault utiliza nCipher nShield family of HSMs (FIPS 140-2 Nível 2 validado) para proteger as suas chaves.

> [!NOTE]
> O método de importação da chave HSM descrito neste documento funciona apenas com a família nCipher nShield de HSMs. Para importar chaves HSM de outros HSMs [consulte aqui.](hsm-protected-keys-byok.md)

Use as informações neste tópico para ajudá-lo a planear, gerar e, em seguida, transferir as suas próprias chaves protegidas pelo HSM para usar com o Cofre da Chave Azure. 

Esta funcionalidade não se encontra disponível para o Azure China 21Vianet.

> [!NOTE]
> Para mais informações sobre o Azure Key Vault, veja [o que é o Cofre da Chave Azure?](../general/overview.md)  
> Para um tutorial de iniciação, que inclui a criação de um cofre chave para chaves protegidas pelo HSM, veja [o que é a Azure Key Vault?](../general/overview.md)

Mais informações sobre a geração e transferência de uma chave protegida pelo HSM através da Internet:

* Gera a chave a partir de uma estação de trabalho offline, o que reduz a superfície de ataque.
* A chave é encriptada com uma chave de troca de chaves (KEK), que permanece encriptada até ser transferida para o Cofre de Azure HSMs. Apenas a versão encriptada da sua chave deixa a estação de trabalho original.
* O conjunto de ferramentas define propriedades na chave do seu inquilino que liga a sua chave ao mundo de segurança Azure Key Vault. Assim, depois de os HSMs Azure Key Vault receberem e desencriptarem a sua chave, apenas estes HSMs podem usá-lo. A sua chave não pode ser exportada. Esta ligação é aplicada pelos HSMs nCipher.
* A Chave de Troca de Chaves (KEK) que é usada para encriptar a sua chave é gerada dentro dos HSMs Azure Key Vault e não é exportável. Os HSMs impõem que não pode existir uma versão não encriptada da KEK fora dos mesmos. Além disso, o bloco de ferramentas inclui a atestação do nCipher de que o KEK não é exportável e foi gerado dentro de um HSM genuíno que foi fabricado pelo nCipher.
* O toolset inclui atestado do nCipher que o mundo de segurança Azure Key Vault também foi gerado num HSM genuíno fabricado pelo nCipher. Este atestado prova que a Microsoft está a usar hardware genuíno.
* A Microsoft usa KEKs separados e mundos de segurança separados em cada região geográfica. Esta separação garante que a sua chave só pode ser usada em centros de dados na região em que a encripta. Por exemplo, uma chave de um cliente europeu não pode ser utilizada em centros de dados na América do Norte ou na Ásia.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Mais informações sobre nCipher HSMs e serviços microsoft

nCipher Security, uma empresa de datacard da Entrust, é líder no mercado hsm de propósito geral, capacitando as organizações líderes mundiais, fornecendo confiança, integridade e controlo às suas informações e aplicações críticas empresariais. as soluções criptográficas da nCipher asseguram tecnologias emergentes – cloud, IoT, blockchain, pagamentos digitais – e ajudam a cumprir novos mandatos de conformidade, utilizando a mesma tecnologia comprovada de que as organizações globais dependem hoje para proteger contra ameaças aos seus dados sensíveis, comunicações de rede e infraestruturas empresariais. nCipher oferece confiança para aplicações críticas empresariais, garantindo a integridade dos dados e colocando os clientes em total controlo – hoje, amanhã, em todos os momentos.

A Microsoft tem colaborado com a NCipher Security para melhorar o estado da arte para os HSMs. Estas melhorias permitem-lhe obter as vantagens típicas dos serviços alojados sem abdicar do controlo sobre as suas chaves. Especificamente, estas melhorias permitem que a Microsoft faça a gestão dos HSMs para que o utilizador não tenha de o fazer. Como serviço em nuvem, o Azure Key Vault escala-se em curto prazo para atender os picos de uso da sua organização. Ao mesmo tempo, a sua chave está protegida dentro dos HSMs da Microsoft: Mantém o controlo sobre o ciclo de vida chave porque gera a chave e transfere-a para os HSMs da Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementar o BYOK (Bring Your Own Key) para o Azure Key Vault

Utilize as seguintes informações e procedimentos se gerar a sua própria chave protegida pelo HSM e, em seguida, transferi-la para a Azure Key Vault - o cenário trazer a sua própria chave (BYOK).

## <a name="prerequisites-for-byok"></a>Pré-requisitos para o BYOK

Consulte a seguinte tabela para obter uma lista de pré-requisitos para trazer a sua própria chave (BYOK) para Azure Key Vault.

| Requisito | Mais informações |
| --- | --- |
| Uma assinatura do Azure |Para criar um Cofre de Chaves Azure, precisa de uma subscrição Azure: [Inscreva-se para teste gratuito](https://azure.microsoft.com/pricing/free-trial/) |
| O nível de serviço Azure Key Vault Premium para suportar chaves protegidas pelo HSM |Para obter mais informações sobre os níveis de serviço e as capacidades para o Azure Key Vault, consulte o site de preços do [cofre de chaves Azure.](https://azure.microsoft.com/pricing/details/key-vault/) |
| nCipher nShield HSMs, smartcards e software de suporte |Você deve ter acesso a um módulo de segurança de hardware nCipher e conhecimentos operacionais básicos de nCipher nShield HSMs. Consulte [nCipher nShield Hardware Security Module](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) para obter a lista de modelos compatíveis ou para adquirir um HSM se não tiver um. |
| O seguinte hardware e software:<ol><li>Uma estação de trabalho offline x64 com um sistema de funcionamento mínimo do Windows 7 e software nCipher nShield que é pelo menos a versão 11.50.<br/><br/>Se esta estação de trabalho executar o Windows 7, tem de [instalar o Microsoft .NET Framework 4.5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Uma estação de trabalho que está ligada à Internet e tem um sistema operativo mínimo Windows do Windows 7 e [Azure PowerShell](/powershell/azure/?view=azps-1.2.0) **na versão mínima 1.1.0** instalada.</li><li>Uma unidade USB ou outro dispositivo de armazenamento portátil que tenha pelo menos 16 MB de espaço livre.</li></ol> |Por motivos de segurança, recomendamos que a primeira estação de trabalho não esteja ligada a uma rede. No entanto, esta recomendação não é aplicada programáticamente.<br/><br/>Nas instruções que se seguem, esta estação de trabalho é referida como a estação de trabalho desligada.</p></blockquote><br/>Além disso, se a chave do seu inquilino for para uma rede de produção, recomendamos que use uma segunda estação de trabalho separada para descarregar o depósito de ferramentas e fazer o upload da chave do inquilino. Contudo, para fins de teste, pode utilizar a mesma estação de trabalho.<br/><br/>Nas instruções que se seguem, esta segunda estação de trabalho é referida como a estação de trabalho ligada à Internet.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Gere e transfira a sua chave para Azure Key Vault HSM

Utilizará os seguintes cinco passos para gerar e transferir a sua chave para um Cofre de Chaves Azure HSM:

* [Passo 1: Prepare a sua estação de trabalho ligada à Internet](#step-1-prepare-your-internet-connected-workstation)
* [Passo 2: Prepare a sua estação de trabalho desligada](#step-2-prepare-your-disconnected-workstation)
* [Passo 3: Gere a sua chave](#step-3-generate-your-key)
* [Passo 4: preparar a transferência da chave](#step-4-prepare-your-key-for-transfer)
* [Passo 5: transferir a chave para o Cofre de Chaves do Azure](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Passo 1: Prepare a sua estação de trabalho ligada à Internet

Para este primeiro passo, faça os seguintes procedimentos no seu posto de trabalho que esteja ligado à Internet.

### <a name="step-11-install-azure-powershell"></a>Passo 1.1: Instalar a azure powershell

A partir da estação de trabalho ligada à Internet, descarregue e instale o módulo Azure PowerShell que inclui os cmdlets para gerir o Azure Key Vault. Para obter instruções de instalação, consulte [como instalar e configurar a Azure PowerShell](/powershell/azure/).

### <a name="step-12-get-your-azure-subscription-id"></a>Passo 1.2: Obtenha o seu ID de assinatura Azure

Inicie uma sessão Azure PowerShell e inicie sessão na sua conta Azure utilizando o seguinte comando:

```Powershell
   Connect-AzAccount
```
Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. Em seguida, use o comando [Get-AzSubscription:](/powershell/module/az.accounts/get-azsubscription)

```powershell
   Get-AzSubscription
```
A partir da saída, localize o ID para a subscrição que utilizará para Azure Key Vault. Mais tarde, vai precisar deste ID de assinatura.

Não feche a janela Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Passo 1.3: Descarregue o toolset BYOK para Azure Key Vault

Vá ao Microsoft Download Center e [descarregue o azure Key Vault BYOK para](https://www.microsoft.com/download/details.aspx?id=45345) a sua região geográfica ou instância de Azure. Utilize as seguintes informações para identificar o nome do pacote para descarregar e o seu haxixe de embalagem SHA-256 correspondente:

---
**Estados Unidos:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C0032040043010636A4E8C67B777901524E4EC24A2D3A6DC513CA1823B0D4

---
**Europa:**

KeyVault-BYOK-Tools-Europe.zip

9aaa63E2E7F20CF9BB62485868754203721D2F888300910634A32DFA1F19E4A

---
**Ásia:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C97738EC7121EF6B5

---
**América Latina:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D0375662F25A5A5A538922D1B01A4FACB619

---
**Japão:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4B8E94632DEC77DF

---
**Coreia:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDA05344ED136F

---
**África do Sul:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA

---
**Emirados Árabes Unidos:**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962AA0913EA411DAB97792248C65F365FD953BB9F241D5FC0D3

---
**Austrália:**

KeyVault-BYOK-Tools-Australia.zip

CD0F7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB16901C40A

---
[**Governo de Azure:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AAAA79FF0FD3048B5795EC83ADC59DB018621A

---
**DoD do governo dos EUA:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B91BB55C35263

---
**Canadá:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42C88C2CE249E27193463085799717C7B

---
**Alemanha:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AFC02AFD9841F7BADD025D7EE819894A29ED3C71C3F844C45D6

---
**Público alemão:**

KeyVault-BYOK-Tools-Germany-Public.zip

54534936D0A0C99C817DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**Índia:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CBA77641134F61B0E0940121C436C8

---
**França:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Reino Unido:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Suíça:**

KeyVault-BYOK-Tools-Switzerland.zip

88CF8D39899E26D456D4E0BC5E5C913ABF1D73A89013FCE3BBD9599AD2FE9

---


Para validar a integridade do seu toolset BYOK descarregado, a partir da sua sessão Azure PowerShell, utilize o cmdlet [Get-FileHash.](/powershell/module/microsoft.powershell.utility/get-filehash)

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

O instrumento inclui:

* Um pacote chave de troca de chaves (KEK) que tem um nome começando por **BYOK-KEK-pkg-.**
* Um pacote do Security World que tem um nome que começa com **BYOK-SecurityWorld-pkg-.**
* Um guião de pitão chamado **verifykeypackage.py.**
* Um ficheiro executável de linha de comando chamado **KeyTransferRemote.exe** e DLLs associados.
* Um Pacote Redistribuable Visual C++, nomeado **vcredist_x64.exe.**

Copie o pacote para outro armazenamento portátil ou uma unidade USB.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Passo 2: Prepare a sua estação de trabalho desligada

Para este segundo passo, faça os seguintes procedimentos no posto de trabalho que não esteja ligado a uma rede (nem a Internet nem a sua rede interna).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Passo 2.1: Preparar a estação de trabalho desligada com nCipher nShield HSM

Instale o software de suporte nCipher num computador Windows e, em seguida, prenda um nCipher nShield HSM a esse computador.

Certifique-se de que as ferramentas nCipher estão no seu caminho **(%nfast_home%\bin** ). Por exemplo, escreva o seguinte:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Para mais informações, consulte o manual do utilizador incluído no nShield HSM.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Passo 2.2: Instalar o manual de ferramentas BYOK na estação de trabalho desligada

Copie o conjunto de ferramentas BYOK da unidade USB ou de outro armazenamento portátil e, em seguida, faça o seguinte:

1. Extraia os ficheiros do pacote transferido para qualquer pasta.
2. Nessa pasta, execute o ficheiro vcredist_x64.exe.
3. Siga as instruções para instalar os componentes de tempo de execução Visual C++ para Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Passo 3: Gere a sua chave

Para este terceiro passo, faça os seguintes procedimentos na estação de trabalho desligada. Para completar este passo, o seu HSM deve estar no modo de inicialização. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Passo 3.1: Alterar o modo HSM para 'I'

Se estiver a utilizar nCipher nShield Edge, para alterar o modo: 1. Utilize o botão Modo para realçar o modo necessário. 2. Dentro de alguns segundos, prima e mantenha o botão Clear durante alguns segundos. Se o modo mudar, o LED do novo modo para de piscar e permanece aceso. O LED de estado pode piscar irregularmente durante alguns segundos e, em seguida, piscar regularmente quando o dispositivo estiver pronto. Caso contrário, o dispositivo permanece no modo atual, com o modo adequado LED aceso.

### <a name="step-32-create-a-security-world"></a>Passo 3.2: Criar um mundo de segurança

Inicie um pedido de comando e execute o programa nCipher new-world.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Este programa cria um ficheiro **Universo de Segurança** em %NFAST_KMDATA%\local\world, que corresponde à pasta C:\ProgramData\nCipher\Key Management Data\local. Pode usar valores diferentes para o quórum mas, no nosso exemplo, é solicitado que introduza três cartões e pinos em branco para cada um. Então, qualquer duas cartas dão acesso total ao mundo da segurança. Estes cartões tornam-se o **Conjunto de Cartões do Administrador** para o novo universo de segurança.

> [!NOTE]
> Se o seu HSM não suportar a mais recente suíte cifra DLf3072s256mRijndael, pode substituir --cifra-suite= DLf3072s256mRijndael por --cifra-suite=DLf1024s160mRijndael
> 
> O mundo da segurança criado com new-world.exe que os navios com a versão 12.50 do software nCipher não são compatíveis com este procedimento BYOK. Estão disponíveis duas opções:
> 1) Downgrade nCipher versão de software para 12.40.2 para criar um novo mundo de segurança.
> 2) Contacte o suporte nCipher e solicite-lhes que forneçam um hotfix para a versão de software 12.50, que lhe permite utilizar a versão 12.40.2 do new-world.exe que seja compatível com este procedimento BYOK.

Em seguida, faça o seguinte:

* Crie uma cópia de segurança do ficheiro do universo. Proteja o ficheiro do universo, os Cartões de Administrador e os respetivos pins e certifique-se de que ninguém tem acesso a mais do que um cartão.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Passo 3.3: Alterar o modo HSM para 'O'

Se estiver a utilizar nCipher nShield Edge, para alterar o modo: 1. Utilize o botão Modo para realçar o modo necessário. 2. Dentro de alguns segundos, prima e mantenha o botão Clear durante alguns segundos. Se o modo mudar, o LED do novo modo para de piscar e permanece aceso. O LED de estado pode piscar irregularmente durante alguns segundos e, em seguida, piscar regularmente quando o dispositivo estiver pronto. Caso contrário, o dispositivo permanece no modo atual, com o modo adequado LED aceso.

### <a name="step-34-validate-the-downloaded-package"></a>Passo 3.4: Validar o pacote descarregado

Este passo é opcional, mas recomendado para que possa validar o seguinte:

* A Chave de Troca de Chaves que está incluída no teclado foi gerada a partir de um genuíno nCipher nShield HSM.
* O haxixe do Mundo de Segurança que está incluído no toolset foi gerado num genuíno nCipher nShield HSM.
* A Chave de Troca de Chaves não é exportável.

> [!NOTE]
> Para validar o pacote descarregado, o HSM deve ser conectado, ligado e deve ter um mundo de segurança sobre ele (como o que acabou de criar).

Para validar o pacote descarregado:

1. Executar o verifykeypackage.py script grafando um dos seguintes, dependendo da sua região geográfica ou da instância de Azure:

   * Para a América do Norte

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
      ```
   * Para a Europa:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
      ```
   * Para a Ásia:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
        ```
   * Para a América Latina:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
        ```
   * Para o Japão:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
        ```
   * Para a Coreia:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
        ```
   * Para a África do Sul:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
        ```
   * Para os Emirados Árabes Unidos:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
        ```
   * Para a Austrália:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
        ```
   * Para [o Governo de Azure](https://azure.microsoft.com/features/gov/), que usa a instância do governo dos EUA de Azure:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
        ```
   * Para o DoD do governo dos EUA:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
        ```
   * Para o Canadá:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
        ```
   * Para a Alemanha:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * Para o Público alemão:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * Para a Índia:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
      ```
   * Para a França:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
        ```
   * Para o Reino Unido:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
        ```
   * Para a Suíça:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1
        ```

     > [!TIP]
     > O software nCipher nShield inclui python em %NFAST_HOME%\python\bin
     >
     >
2. Confirme que vê o seguinte, o que indica validação bem sucedida: **Resultado: SUCESSO**

Este script valida a corrente do signatário até à tecla raiz nShield. O hash desta chave de raiz é incorporado no script e o respetivo valor deve ser **59178a47 de508c3f 291277ee 184f46c4 f1d9c639** . Também pode confirmar este valor separadamente visitando o [site nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation).

Agora estás pronto para criar uma nova chave.

### <a name="step-35-create-a-new-key"></a>Passo 3.5: Criar uma nova chave

Gere uma chave utilizando o programa nCipher nShield **generatekey.**

Execute o seguinte comando para criar a chave:

```azurepowershell
generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=
```

Quando executar este comando, utilize estas instruções:

* O parâmetro *protect* tem de ser definido para o valor **module** , conforme mostrado. Esta ação cria uma chave protegida por um módulo. O conjunto de ferramentas BYOK não suporta chaves protegidas por OCS.
* Substitua o valor *contosokey* por **ident** e **plainname** por qualquer valor da cadeia. Para minimizar as despesas administrativas e reduzir o risco de erros, recomendamos que utilize o mesmo valor para ambos. O valor **do identificador** deve conter apenas números, traços e letras minúsculas.
* O pubexp fica em branco (predefinição) neste exemplo, mas pode especificar valores. Para mais informações, consulte a [documentação nCipher.](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

Este comando cria um ficheiro Chave Tokenized na pasta %NFAST_KMDATA%\local com um nome a começar por **key_simple_** , seguido do **identificador** especificado no comando. Por exemplo: **key_simple_contosokey** . Este ficheiro contém uma chave encriptada.

Crie uma cópia de segurança deste Ficheiro de Chave com Token numa localização segura.

> [!IMPORTANT]
> Quando mais tarde transferir a sua chave para o Azure Key Vault, a Microsoft não pode exportar esta chave de volta para si, pelo que se torna extremamente importante que faça o seu back up do seu mundo de chaves e segurança com segurança. Contacte [nCipher](https://www.ncipher.com/about-us/contact-us) para obter orientação e boas práticas para fazer backup da sua chave.
>


Está agora pronto para transferir a sua chave para o Cofre da Chave Azure.

## <a name="step-4-prepare-your-key-for-transfer"></a>Passo 4: preparar a transferência da chave

Para este quarto passo, faça os seguintes procedimentos na estação de trabalho desligada.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Passo 4.1: Criar uma cópia da sua chave com permissões reduzidas

Abra uma nova solicitação de comando e altere o diretório atual para o local onde abriu o ficheiro zip BYOK. Para reduzir as permissões na sua chave, a partir de um pedido de comando, execute uma das seguintes, dependendo da sua região geográfica ou da instância de Azure:

* Para a América do Norte

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-
   ```
* Para a Europa:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
   ```
* Para a Ásia:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
   ```
* Para a América Latina:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
   ```
* Para o Japão:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
   ```
* Para a Coreia:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
   ```
* Para a África do Sul:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
   ```
* Para os Emirados Árabes Unidos:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
   ```
* Para a Austrália:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
   ```
* Para [o Governo de Azure](https://azure.microsoft.com/features/gov/), que usa a instância do governo dos EUA de Azure:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
   ```
* Para o DoD do governo dos EUA:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
   ```
* Para o Canadá:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
   ```
* Para a Alemanha:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* Para o Público alemão:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* Para a Índia:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
   ```
* Para a França:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
   ```
* Para o Reino Unido:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
   ```
* Para a Suíça:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1
   ```

Quando executar este comando, substitua o *contosokey* pelo mesmo valor especificado no **Passo 3.5: Crie uma nova chave** a partir do passo de chave [Gerar.](#step-3-generate-your-key)

É-lhe pedido que ligue os seus cartões de administração do mundo da segurança.

Quando o comando estiver concluído, vê **Resultado: SUCESSO** e a cópia da sua chave com permissões reduzidas estão no ficheiro denominado key_xferacId_ \<contosokey> .

Pode inspecionar o ACLS utilizando os seguintes comandos utilizando os utilitários nCipher nShield:

* aclprint.py:

   ```cmd
   "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
   ```
* kmfile-dump.exe:

   ```cmd
   "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
   ```
  Quando executar estes comandos, substitua o contosokey pelo mesmo valor especificado no **Passo 3.5: Crie uma nova chave** a partir do passo de chave [Gerar.](#step-3-generate-your-key)

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Passo 4.2: Criptografe a sua chave utilizando a chave de troca de chaves da Microsoft

Executar um dos seguintes comandos, dependendo da sua região geográfica ou da sua instância de Azure:

* Para a América do Norte

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para a Europa:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para a Ásia:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para a América Latina:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para o Japão:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para a Coreia:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para a África do Sul:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para os Emirados Árabes Unidos:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para a Austrália:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para [o Governo de Azure](https://azure.microsoft.com/features/gov/), que usa a instância do governo dos EUA de Azure:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para o DoD do governo dos EUA:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para o Canadá:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para a Alemanha:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para o Público alemão:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para a Índia:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para a França:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para o Reino Unido:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Para a Suíça:

  ```azurepowershell
  KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
  ```


Quando executar este comando, utilize estas instruções:

* Substitua *o contosokey* pelo identificador que usou para gerar a tecla no **Passo 3.5: Crie uma nova chave** a partir do passo de chave [Gerar.](#step-3-generate-your-key)
* Substitua *a SubscriçãoID* pelo ID da subscrição Azure que contém o seu cofre de chaves. Você recuperou este valor anteriormente, no **Passo 1.2: Obtenha o seu ID de subscrição Azure** a partir do passo de [estação de trabalho ligado à Internet.](#step-1-prepare-your-internet-connected-workstation)
* Substitua *o ContosoFirstHSMKey* por uma etiqueta que seja utilizada para o nome do ficheiro de saída.

Quando isto termina com sucesso, apresenta **Resultado: SUCESSO** e há um novo ficheiro na pasta atual que tem o seguinte nome: KeyTransferPackage- *ContosoFirstHSMkey* .byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Passo 4.3: Copie o seu pacote de transferência chave para a estação de trabalho ligada à Internet

Utilize uma unidade USB ou outro armazenamento portátil para copiar o ficheiro de saída do passo anterior (KeyTransferPackage-ContosoFirstHSMkey.byok) para a sua estação de trabalho ligada à Internet.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Passo 5: transferir a chave para o Cofre de Chaves do Azure

Para este último passo, na estação de trabalho ligada à Internet, utilize o cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) para carregar o pacote de transferência de chaves que copiou da estação de trabalho desligada para o Cofre de Chaves Azure HSM:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Se o upload for bem sucedido, vê as propriedades da chave que acabou de adicionar.

## <a name="next-steps"></a>Passos seguintes

Agora pode utilizar esta chave protegida pelo HSM no seu cofre de chaves. Para mais informações, consulte este preço e [comparação](https://azure.microsoft.com/pricing/details/key-vault/)de recursos.