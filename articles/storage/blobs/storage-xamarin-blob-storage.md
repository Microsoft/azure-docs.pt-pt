---
title: Como usar o armazenamento de objeto (BLOB) do Xamarin | Microsoft Docs
description: A biblioteca de cliente de armazenamento do Azure para Xamarin permite que os desenvolvedores criem aplicativos iOS, Android e Windows Store com suas interfaces de usuário nativas. Este tutorial mostra como usar o Xamarin para criar um aplicativo que usa o armazenamento de BLOBs do Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8a1c91c8a8a59af26386e70e68e7c4fd93f5eaa9
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726346"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Como usar o armazenamento de BLOBs do Xamarin

O Xamarin permite que os desenvolvedores usem C# uma base de código compartilhada para criar aplicativos Ios, Android e Windows Store com suas interfaces de usuário nativas. Este tutorial mostra como usar o armazenamento de BLOBs do Azure com um aplicativo Xamarin. Se você quiser saber mais sobre o armazenamento do Azure, antes de mergulhar no código, consulte [introdução ao armazenamento do Microsoft Azure](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Criar um novo aplicativo Xamarin

Para este tutorial, criaremos um aplicativo direcionado para Android, iOS e Windows. Esse aplicativo simplesmente criará um contêiner e carregará um blob nesse contêiner. Usaremos o Visual Studio no Windows, mas os mesmos aprendizados podem ser aplicados ao criar um aplicativo usando Xamarin Studio no macOS.

Siga estas etapas para criar seu aplicativo:

1. Se você ainda não fez isso, baixe e instale o [Xamarin para Visual Studio](https://www.xamarin.com/download).
2. Abra o Visual Studio e crie um aplicativo em branco (portátil nativo): O **arquivo > novo projeto de > > entre plataformas > aplicativo em branco (portátil nativo)** .
3. Clique com o botão direito do mouse em sua solução no painel de Gerenciador de Soluções e selecione **gerenciar pacotes NuGet para solução**. Pesquise **WindowsAzure. Storage** e instale a versão estável mais recente em todos os projetos em sua solução.
4. Compile e execute seu projeto.

Agora você deve ter um aplicativo que permite clicar em um botão que incrementa um contador.

## <a name="create-container-and-upload-blob"></a>Criar contêiner e carregar blob

Em seguida, em `(Portable)` seu projeto, você adicionará um código `MyClass.cs`ao. Esse código cria um contêiner e carrega um blob nesse contêiner. `MyClass.cs`deve ser semelhante ao seguinte:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Certifique-se de substituir "your_account_name_here" e "your_account_key_here" pelo nome da conta real e pela chave de conta.

Todos os projetos do iOS, Android e Windows Phone têm referências ao seu projeto portátil, o que significa que você pode escrever todo o código compartilhado em um único lugar e usá-lo em todos os seus projetos. Agora você pode adicionar a seguinte linha de código a cada projeto para começar a tirar proveito:`MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp. Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp. iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Executar a aplicação

Agora você pode executar esse aplicativo em um emulador Android ou Windows Phone. Você também pode executar esse aplicativo em um emulador do iOS, mas isso exigirá um Mac. Para obter instruções específicas sobre como fazer isso, leia a documentação para [conectar o Visual Studio a um Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Depois de executar o aplicativo, ele criará o contêiner `mycontainer` em sua conta de armazenamento. Ele deve conter o blob, `myblob`, que tem o texto, `Hello, world!`. Você pode verificar isso usando o [Gerenciador de armazenamento do Microsoft Azure](https://storageexplorer.com/).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu a criar um aplicativo de plataforma cruzada no Xamarin que usa o armazenamento do Azure, concentrando-se especificamente em um cenário no armazenamento de BLOBs. No entanto, você pode fazer muito mais com o armazenamento de BLOBs, mas também com o armazenamento de tabela, arquivo e fila. Consulte os seguintes artigos para saber mais:

* [Introdução ao armazenamento de Blobs do Azure através do .NET](storage-dotnet-how-to-use-blobs.md)
* [Introdução aos arquivos do Azure](../files/storage-files-introduction.md)
* [Programar para os Ficheiros do Azure com .NET](../files/storage-dotnet-how-to-use-files.md)
* [Introdução ao armazenamento de Tabelas do Azure através do .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Introdução ao Armazenamento de filas do Azure através do .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
