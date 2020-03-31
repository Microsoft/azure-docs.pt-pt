---
title: Como gerar e transferir chaves protegidas por HSM para o Cofre de Chaves Azure - Cofre chave Azure [ Cofre de Chaves Azure ] Microsoft Docs
description: Use este artigo para ajudá-lo a planear, gerar e, em seguida, transferir as suas próprias chaves protegidas por HSM para usar com o Cofre chave Azure. Também conhecido como BYOK ou traga a sua própria chave.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 0d51e1aaf1d9b0472245b9a7f29148517a0a7b93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77429309"
---
# <a name="import-hsm-protected-keys-for-key-vault-legacy"></a>Importar chaves protegidas por HSM para o Cofre-Chave (legado)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para obter mais garantias, quando utilizar o Cofre chave Azure, pode importar ou gerar chaves em módulos de segurança de hardware (HSMs) que nunca saem do limite HSM. Este cenário costuma chamar-se *Bring Your Own Key* ou BYOK. O Azure Key Vault utiliza a família nCipher nShield de HSMs (FIPS 140-2 Level 2 validado) para proteger as suas chaves.

Use a informação neste tópico para ajudá-lo a planear, gerar e, em seguida, transferir as suas próprias chaves protegidas por HSM para usar com o Cofre chave Azure.

Esta funcionalidade não está disponível para a Azure China.

> [!NOTE]
> Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre chave Azure?](key-vault-overview.md)  
> Para começar um tutorial, que inclui a criação de um cofre chave para chaves protegidas por HSM, veja [o que é o Cofre chave Azure?](key-vault-overview.md)

Mais informações sobre a geração e transferência de uma chave protegida por HSM através da Internet:

* Gera-se a chave a partir de uma estação de trabalho offline, o que reduz a superfície de ataque.
* A chave é encriptada com uma chave de troca (KEK), que permanece encriptada até ser transferida para os HSMs do Cofre de Chaves Azure. Apenas a versão encriptada da sua chave deixa a estação de trabalho original.
* O conjunto de ferramentas define propriedades na chave do seu inquilino que liga a chave ao mundo de segurança do Cofre chave Azure. Assim, depois de os HSMs do Cofre de Chaves Azure receberem e desencriptarem a sua chave, apenas estes HSMs podem usá-la. A sua chave não pode ser exportada. Esta ligação é imposta pelos HSMs nCipher.
* A chave de troca (KEK) que é usada para encriptar a sua chave é gerada dentro dos HSMs do Cofre chave Azure e não é exportável. Os HSMs impõem que não pode existir uma versão não encriptada da KEK fora dos mesmos. Além disso, o conjunto de ferramentas inclui o atestado da nCipher que o KEK não é exportável e foi gerado dentro de um HSM genuíno que foi fabricado pela nCipher.
* O conjunto de ferramentas inclui o atestado do nCipher que o mundo de segurança do Cofre chave Azure também foi gerado num HSM genuíno fabricado pela nCipher. Esta atesta prova-lhe que a Microsoft está a usar hardware genuíno.
* A Microsoft utiliza KEKs separados e mundos de segurança separados em cada região geográfica. Esta separação garante que a sua chave só pode ser utilizada em centros de dados na região em que a encriptou. Por exemplo, uma chave de um cliente europeu não pode ser utilizada em centros de dados na América do Norte ou na Ásia.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Mais informações sobre hCipher HSMs e serviços microsoft

nCipher Security, uma empresa de Cartões de Dados Entrust, é um líder no mercado geral da HSM, capacitando as organizações líderes mundiais, fornecendo confiança, integridade e controlo às suas informações e aplicações críticas de negócios. as soluções criptográficas da nCipher asseguram tecnologias emergentes – cloud, IoT, blockchain, pagamentos digitais – e ajudam a cumprir novos mandatos de conformidade, utilizando a mesma tecnologia comprovada de que as organizações globais dependem hoje para proteger contra ameaças aos seus dados sensíveis, comunicações de rede e infraestruturas empresariais. nCipher oferece confiança para aplicações críticas de negócios, garantindo a integridade dos dados e colocando os clientes em total controlo – hoje, amanhã, em todos os momentos.

A Microsoft tem colaborado com a nCipher Security para melhorar o estado de arte para os HSMs. Estas melhorias permitem-lhe obter as vantagens típicas dos serviços alojados sem abdicar do controlo sobre as suas chaves. Especificamente, estas melhorias permitem que a Microsoft faça a gestão dos HSMs para que o utilizador não tenha de o fazer. Como serviço de nuvem, o Azure Key Vault escala-se em curto prazo para atender aos picos de utilização da sua organização. Ao mesmo tempo, a sua chave está protegida no interior dos HSMs da Microsoft: você mantém o controlo sobre o ciclo de vida da chave, uma vez que a gera e transfere para os HSMs da Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementar o BYOK (Bring Your Own Key) para o Azure Key Vault

Utilize as seguintes informações e procedimentos se gerar a sua própria chave protegida por HSM e, em seguida, transferi-la para o Cofre de Chaves Azure — o cenário de trazer a sua própria chave (BYOK).

## <a name="prerequisites-for-byok"></a>Pré-requisitos para o BYOK

Consulte a tabela seguinte para obter uma lista de pré-requisitos para trazer a sua própria chave (BYOK) para o Cofre de Chaves Azure.

| Requisito | Mais informações |
| --- | --- |
| Uma subscrição do Azure |Para criar um Cofre chave Azure, precisa de uma subscrição Azure: [Inscreva-se para teste gratuito](https://azure.microsoft.com/pricing/free-trial/) |
| O nível de serviço Azure Key Vault Premium para suportar chaves protegidas por HSM |Para obter mais informações sobre os níveis de serviço e capacidades para o Azure Key Vault, consulte o site de preços do cofre de [chaves Azure.](https://azure.microsoft.com/pricing/details/key-vault/) |
| nCipher nShield HSMs, smartcards e software de suporte |Deve ter acesso a um Módulo de Segurança de Hardware nCipher e ao conhecimento operacional básico dos HSMs nCipher nShield. Consulte o Módulo de Segurança de [Hardware nCipher nShield](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) para obter a lista de modelos compatíveis ou para comprar um HSM se não tiver um. |
| O seguinte hardware e software:<ol><li>Uma estação de trabalho offline x64 com um sistema de funcionamento mínimo do Windows 7 e nCipher nShield software que é pelo menos a versão 11.50.<br/><br/>Se esta estação de trabalho executar o Windows 7, tem de [instalar o Microsoft .NET Framework 4.5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Uma estação de trabalho que está ligada à Internet e tem um sistema operativo Windows mínimo do Windows 7 e [do Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) versão **mínima 1.1.0** instalada.</li><li>Uma unidade USB ou outro dispositivo de armazenamento portátil que tenha pelo menos 16 MB de espaço livre.</li></ol> |Por motivos de segurança, recomendamos que a primeira estação de trabalho não esteja ligada a uma rede. No entanto, esta recomendação não é aplicada programáticamente.<br/><br/>Nas instruções que se seguem, esta estação de trabalho é referida como a estação de trabalho desligada.</p></blockquote><br/>Além disso, se a chave do seu inquilino for para uma rede de produção, recomendamos que utilize uma segunda estação de trabalho separada para descarregar o conjunto de ferramentas e faça upload da chave do inquilino. Contudo, para fins de teste, pode utilizar a mesma estação de trabalho.<br/><br/>Nas instruções que se seguem, esta segunda estação de trabalho é referida como a estação de trabalho ligada à Internet.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Gere e transfira a sua chave para o Cofre de Chaves Azure HSM

Utilizará os seguintes cinco passos para gerar e transferir a sua chave para um Cofre De Chaves Azure HSM:

* [Passo 1: Prepare a sua estação de trabalho ligada à Internet](#step-1-prepare-your-internet-connected-workstation)
* [Passo 2: Prepare a sua estação de trabalho desligada](#step-2-prepare-your-disconnected-workstation)
* [Passo 3: Gere a sua chave](#step-3-generate-your-key)
* [Passo 4: preparar a transferência da chave](#step-4-prepare-your-key-for-transfer)
* [Passo 5: transferir a chave para o Cofre de Chaves do Azure](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Passo 1: Prepare a sua estação de trabalho ligada à Internet

Para este primeiro passo, faça os seguintes procedimentos no seu posto de trabalho que está ligado à Internet.

### <a name="step-11-install-azure-powershell"></a>Passo 1.1: Instalar a PowerShell Azure

A partir da estação de trabalho ligada à Internet, descarregue e instale o módulo Azure PowerShell que inclui os cmdlets para gerir o Cofre chave Azure. Para instruções de instalação, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Passo 1.2: Obtenha o seu ID de subscrição Azure

Inicie uma sessão Azure PowerShell e inicie o seu serviço Azure utilizando o seguinte comando:

```Powershell
   Connect-AzAccount
```
Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. Em seguida, utilize o comando [Get-AzSubscription:](/powershell/module/az.accounts/get-azsubscription)

```powershell
   Get-AzSubscription
```
A partir da saída, localize o ID para a subscrição que utilizará para o Cofre de Chaves Azure. Você precisará deste ID de subscrição mais tarde.

Não feche a janela Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Passo 1.3: Descarregue o conjunto de ferramentas BYOK para o Cofre de Chaves Azure

Vá ao Microsoft Download Center e [descarregue o conjunto de ferramentas Azure Key Vault BYOK](https://www.microsoft.com/download/details.aspx?id=45345) para a sua região geográfica ou instância de Azure. Utilize as seguintes informações para identificar o nome do pacote para descarregar e o seu pacote SHA-256 correspondente hash:

---
**Estados Unidos:**

KeyVault-BYOK-Tools-United States.zip

2E8C0032040043010666A4e8C67B79015524E4EC24A2D3A6DC513CA1823b0D4

---
**Europa:**

KeyVault-BYOK-Tools-Europe.zip

9AA63e2E7F20CF9BB6248888754203721D2F8D300910634A32DFA1FB19E4A

---
**Ásia:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927af621DF665328F8a13616F44C977388EC7121EF6B5

---
**América Latina:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6fD80C4D03756642F25A53892DD1B01A4FACB619

---
**Japão:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027Af923A76F6f6BF98B4D4B8E94632DEC7DF

---
**Coreia:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDA05344ED136F

---
**África do Sul:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAD896DA732E3143D14CB9FC83AC3C6766F46D98620784a

---
**Emirados Sea:**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962A0913EA411DAB97929248C65F365FD93BB9F241D5FC0D3

---
**Austrália:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64a3D3e2452A025223eb166901C40A

---
[**Governo azure:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A736065092391D9Aa79FF030FD3048B5795EC83ADC59DB018621A

---
**DOD do governo dos EUA:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3dDF861B79B8B991BB55C35263

---
**Canadá:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C8C2CE249E271934630885799717C7B

---
**Alemanha:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AfD9841F7BADD025D7ee8198994A29ED3C71C3f844C45D6

---
**Alemanha Pública:**

KeyVault-BYOK-Tools-Germany-Public.zip

54534936D0A0C99C8117DB724C34A5e50FD204CFCBD75C78972B785865364A29

---
**Índia:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA7764134F61B0E0940121C436C8

---
**França:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3e4125B0C09E9f60897C9ae3A8B4CB0E7D13A14F3 EDBD280128F8FE7DF

---
**Reino Unido:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCA9e5eB29BB056489D3782B3B80849

---
**Suíça:**

KeyVault-BYOK-Tools-Switzerland.zip

88CF8D3999E26D456D4e0BC57E5C94913ABF1D73A89013FCE3BBD999999ad2FE9

---


Para validar a integridade do seu conjunto de ferramentas BYOK descarregado, a partir da sua sessão Azure PowerShell, utilize o [cmdlet Get-FileHash.](https://technet.microsoft.com/library/dn520872.aspx)

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

O conjunto de ferramentas inclui:

* Um pacote chave de troca (KEK) que tem um nome começando com **BYOK-KEK-pkg-.**
* Um pacote do Security World que tem um nome a começar com **BYOK-SecurityWorld-pkg-.**
* Um guião de pitão chamado **verifykeypackage.py.**
* Um ficheiro executável de linha de comando chamado **KeyTransferRemote.exe** e DLLs associados.
* Um Pacote Redistribuável Visual C++, chamado **vcredist_x64.exe.**

Copie o pacote para outro armazenamento portátil ou uma unidade USB.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Passo 2: Prepare a sua estação de trabalho desligada

Para este segundo passo, faça os seguintes procedimentos sobre a estação de trabalho que não esteja ligado a uma rede (nem à Internet nem à sua rede interna).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Passo 2.1: Preparar a estação de trabalho desligada com nCipher nShield HSM

Instale o software de suporte nCipher num computador Windows e, em seguida, fixe um NCipher nShield HSM a esse computador.

Certifique-se de que as ferramentas nCipher estão no seu caminho **(%nfast_home%\bin).** Por exemplo, escreva o seguinte:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Para mais informações, consulte o guia do utilizador incluído no NShield HSM.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Passo 2.2: Instale o conjunto de ferramentas BYOK na estação de trabalho desligada

Copie o conjunto de ferramentas BYOK da unidade USB ou de outro armazenamento portátil e, em seguida, faça o seguinte:

1. Extraia os ficheiros do pacote transferido para qualquer pasta.
2. Nessa pasta, execute o ficheiro vcredist_x64.exe.
3. Siga as instruções para instalar os componentes de tempo de execução Visual C++ para o Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Passo 3: Gere a sua chave

Para este terceiro passo, faça os seguintes procedimentos na estação de trabalho desligada. Para completar este passo, o seu HSM deve estar no modo de inicialização. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Passo 3.1: Mude o modo HSM para 'I'

Se estiver a utilizar nCipher nShield Edge, para alterar o modo: 1. Utilize o botão Modo para realçar o modo necessário. 2. Dentro de alguns segundos, prima e segure o botão Clear durante alguns segundos. Se o modo mudar, o LED do novo modo para de piscar e permanece aceso. O LED de Estado pode piscar irregularmente durante alguns segundos e, em seguida, piscar regularmente quando o dispositivo está pronto. Caso contrário, o dispositivo permanece no modo atual, com o LED do modo apropriado aceso.

### <a name="step-32-create-a-security-world"></a>Passo 3.2: Criar um mundo de segurança

Inicie um pedido de comando e execute o programa nCipher new-world.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Este programa cria um ficheiro **Universo de Segurança** em %NFAST_KMDATA%\local\world, que corresponde à pasta C:\ProgramData\nCipher\Key Management Data\local. Pode utilizar valores diferentes para o quórum, mas no nosso exemplo é-lhe pedido que introduza três cartões em branco e pins para cada um deles. Então, qualquer dois cartões dão acesso total ao mundo da segurança. Estes cartões tornam-se o **Conjunto de Cartões do Administrador** para o novo universo de segurança.

> [!NOTE]
> Se o seu HSM não suportar a mais recente suíte cypher DLf3072s256mRijndael, pode substituir --cipher-suite= DLf3072s256mRijndael com --cipher-suite=DLf1024s160mRijndael
> 
> O mundo da segurança criado com o new-world.exe que os navios com a versão de software nCipher 12.50 não são compatíveis com este procedimento BYOK. Estão disponíveis duas opções:
> 1) Downgrade nCipher versão de software para 12.40.2 para criar um novo mundo de segurança.
> 2) Contacte o suporte nCipher e solicite-os para fornecer um hotfix para a versão de software 12.50, que lhe permite utilizar a versão 12.40.2 do new-world.exe que é compatível com este procedimento BYOK.

Em seguida, faça o seguinte:

* Crie uma cópia de segurança do ficheiro do universo. Proteja o ficheiro do universo, os Cartões de Administrador e os respetivos pins e certifique-se de que ninguém tem acesso a mais do que um cartão.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Passo 3.3: Alterar o modo HSM para 'O'

Se estiver a utilizar nCipher nShield Edge, para alterar o modo: 1. Utilize o botão Modo para realçar o modo necessário. 2. Dentro de alguns segundos, prima e segure o botão Clear durante alguns segundos. Se o modo mudar, o LED do novo modo para de piscar e permanece aceso. O LED de Estado pode piscar irregularmente durante alguns segundos e, em seguida, piscar regularmente quando o dispositivo está pronto. Caso contrário, o dispositivo permanece no modo atual, com o LED do modo apropriado aceso.

### <a name="step-34-validate-the-downloaded-package"></a>Passo 3.4: Validar o pacote descarregado

Este passo é opcional, mas recomendado para que possa validar o seguinte:

* A chave de troca que está incluída no conjunto de ferramentas foi gerada a partir de um nCipher nCipher nShield HSM genuíno.
* O hash do Security World que está incluído no conjunto de ferramentas foi gerado em um nCipher nCipher nShield HSM genuíno.
* A Chave de Troca de Chaves não é exportável.

> [!NOTE]
> Para validar o pacote transferido, o HSM tem de estar ligado e tem de ter um universo de segurança no mesmo (tal como o que acabou de criar).

Para validar o pacote descarregado:

1. Execute o roteiro verifykeypackage.py digitando um dos seguintes, dependendo da sua região geográfica ou instância de Azure:

   * Para a América do Norte

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Para a Europa:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Para a Ásia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Para a América Latina:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Para o Japão:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Para a Coreia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Para a África do Sul:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * Para os Emirados Unidos:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * Para a Austrália:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Para o [Governo Azure](https://azure.microsoft.com/features/gov/), que utiliza a instância governamental dos EUA de Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Para o Governo dos EUA DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Para o Canadá:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Para a Alemanha:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Para o Público alemão:

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
     > O software nCipher nShield inclui python a %NFAST_HOME%\python\bin
     >
     >
2. Confirme que vê o seguinte, o que indica validação bem sucedida: **Resultado: SUCESSO**

Este script valida a corrente do signatário até à chave raiz nShield. O hash desta chave de raiz é incorporado no script e o respetivo valor deve ser **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Também pode confirmar este valor separadamente visitando o [website nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation).

Está pronto para criar uma nova chave.

### <a name="step-35-create-a-new-key"></a>Passo 3.5: Criar uma nova chave

Gere uma chave utilizando o programa nCipher nShield **generatekey.**

Execute o seguinte comando para criar a chave:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Quando executar este comando, utilize estas instruções:

* O parâmetro *protect* tem de ser definido para o valor **module**, conforme mostrado. Esta ação cria uma chave protegida por um módulo. O conjunto de ferramentas BYOK não suporta chaves protegidas por OCS.
* Substitua o valor *contosokey* por **ident** e **plainname** por qualquer valor da cadeia. Para minimizar as despesas gerais administrativas e reduzir o risco de erros, recomendamos que utilize o mesmo valor para ambos. O valor **do identificador** deve conter apenas números, traços e letras minúsculas.
* O pubexp fica em branco (predefinição) neste exemplo, mas pode especificar valores. Para mais informações, consulte a [documentação do nCipher.](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

Este comando cria um ficheiro Chave Tokenized na sua pasta %NFAST_KMDATA%\local com um nome a partir de **key_simple_**, seguido do **identificador** especificado no comando. Por exemplo: **key_simple_contosokey**. Este ficheiro contém uma chave encriptada.

Crie uma cópia de segurança deste Ficheiro de Chave com Token numa localização segura.

> [!IMPORTANT]
> Quando mais tarde transferes a tua chave para o Azure Key Vault, a Microsoft não pode exportar esta chave de volta para ti, por isso torna-se extremamente importante que voltes a trabalhar com segurança na tua chave e segurança. Contacte o [nCipher](https://www.ncipher.com/about-us/contact-us) para obter orientação e as melhores práticas para fazer o backup da sua chave.
>


Está pronto para transferir a chave para o Cofre chave Azure.

## <a name="step-4-prepare-your-key-for-transfer"></a>Passo 4: preparar a transferência da chave

Para este quarto passo, faça os seguintes procedimentos na estação de trabalho desligada.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Passo 4.1: Crie uma cópia da sua chave com permissões reduzidas

Abra um novo pedido de comando e altere o diretório atual para o local onde desapertou o ficheiro ZIP BYOK. Para reduzir as permissões na sua chave, a partir de um pedido de comando, executar uma das seguintes, dependendo da sua região geográfica ou instância de Azure:

* Para a América do Norte

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Para a Europa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Para a Ásia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Para a América Latina:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Para o Japão:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Para a Coreia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Para a África do Sul:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* Para os Emirados Unidos:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* Para a Austrália:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Para o [Governo Azure](https://azure.microsoft.com/features/gov/), que utiliza a instância governamental dos EUA de Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Para o Governo dos EUA DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Para o Canadá:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Para a Alemanha:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Para o Público alemão:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Para a Índia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Para a França:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Para o Reino Unido:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* Para a Suíça:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

Quando executar este comando, substitua o *contosokey* pelo mesmo valor especificado no **Passo 3.5: Crie uma nova tecla** a partir do Passo-Chave [Generate.](#step-3-generate-your-key)

É-lhe pedido que ligue os seus cartões de administração do mundo da segurança.

Quando o comando estiver concluído, vê **Resultado: SUCESSO** e a cópia da sua\<chave com permissões reduzidas estão no ficheiro denominado key_xferacId_> contosokey.

Pode inspecionar o ACLS utilizando os seguintes comandos utilizando os utilitários nCipher nShield:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Quando executar estes comandos, substitua o contosokey pelo mesmo valor especificado no **Passo 3.5: Crie uma nova tecla** a partir do Passo-Chave [Generate.](#step-3-generate-your-key)

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Passo 4.2: Criptografe a sua chave utilizando a chave de troca da Microsoft

Executar um dos seguintes comandos, dependendo da sua região geográfica ou instância de Azure:

* Para a América do Norte

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Europa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Ásia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a América Latina:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o Japão:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Coreia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a África do Sul:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para os Emirados Unidos:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Austrália:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o [Governo Azure](https://azure.microsoft.com/features/gov/), que utiliza a instância governamental dos EUA de Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o Governo dos EUA DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o Canadá:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Alemanha:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o Público alemão:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Índia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a França:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o Reino Unido:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Suíça:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Quando executar este comando, utilize estas instruções:

* Substitua o *contosokey* pelo identificador que usou para gerar a chave no **Passo 3.5: Crie uma nova tecla** a partir do Passo Gerar o seu passo [chave.](#step-3-generate-your-key)
* Substitua o *SubscriptionID* pelo ID da subscrição Azure que contém o seu cofre chave. Você recuperou este valor anteriormente, no **Passo 1.2: Obtenha o seu ID de subscrição Azure** a partir do passo de [estação de trabalho ligado à Internet.](#step-1-prepare-your-internet-connected-workstation)
* Substitua o *ContosoFirstHSMKey* por uma etiqueta que é utilizada para o nome do ficheiro de saída.

Quando isto completa com sucesso, exibe **Resultado: SUCESSO** e há um novo ficheiro na pasta atual que tem o seguinte nome: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Passo 4.3: Copie o seu pacote de transferência chave para a estação de trabalho ligada à Internet

Utilize uma unidade USB ou outro armazenamento portátil para copiar o ficheiro de saída do passo anterior (KeyTransferPackage-ContosoFirstHSMkey.byok) para a sua estação de trabalho ligada à Internet.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Passo 5: transferir a chave para o Cofre de Chaves do Azure

Para este último passo, na estação de trabalho ligada à Internet, utilize o cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) para carregar o pacote de transferência chave que copiou da estação de trabalho desligada para o Cofre de Chaves Azure HSM:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Se o upload for bem sucedido, vê-se que mostrou as propriedades da chave que acabou de adicionar.

## <a name="next-steps"></a>Passos seguintes

Agora pode usar esta chave protegida por HSM no seu cofre chave. Para mais informações, consulte este preço e [comparação](https://azure.microsoft.com/pricing/details/key-vault/)de recursos.
