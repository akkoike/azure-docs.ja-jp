---
title: "Azure PowerShell での SQL Server 仮想マシンの作成 (クラシック) | Microsoft Docs"
description: "SQL Server 仮想マシン ギャラリー のイメージを使用して Azure VM を作成するための手順と PowerShell スクリプトを提供します。 このトピックでは、クラシック デプロイメント モードを使用します。"
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 7402249aa87ffe985ae13f28a701e22af3afd450
ms.openlocfilehash: 38fd2e8fce99762cb7c6b6fb160f23a786fb0532


---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Azure PowerShell を使用した SQL Server 仮想マシンのプロビジョニング (クラシック)
## <a name="overview"></a>概要
この記事では、PowerShell コマンドレットを使用して、Azure で SQL Server 仮想マシンを作成する手順を説明します。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

同じ作業を Resource Manager デプロイメント モデルで行う方法については、[Resource Manager バージョンの Azure PowerShell を使用した SQL Server 仮想マシンのプロビジョニング](../sql/virtual-machines-windows-ps-sql-create.md)に関する記事をご覧ください。

### <a name="install-and-configure-powershell"></a>PowerShell のインストールと構成:
1. Azure アカウントを持っていない場合は、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)にアクセスしてください。
2. [最新の Azure PowerShell コマンドをダウンロードしてインストールします](/powershell/azureps-cmdlets-docs)。
3. Windows PowerShell を起動し、 **Add-AzureAccount** コマンドを使用して Azure サブスクリプションに接続します。
   
        Add-AzureAccount

## <a name="determine-your-target-azure-region"></a>ターゲット Azure リージョンの決定
SQL Server の仮想マシンは、特定の Azure リージョンに存在するクラウド サービスでホストされます。 次の手順は、このチュートリアルの残りの部分に使用されるリージョン、ストレージ アカウント、およびクラウド サービスの決定に役立ちます。

1. SQL Server VM のホストに使用するデータ センターを決定します。 次の PowerShell コマンドによって、使用可能なリージョンの詳細が文末の要約リストとともに表示されます。
   
        Get-AzureLocation
        (Get-AzureLocation).Name
2. 希望する場所を特定したら、 **$dcLocation** という名前の変数をそのリージョンに設定します。
   
       $dcLocation = "<region name>"

## <a name="set-your-subscription-and-storage-account"></a>サブスクリプションとストレージ アカウントの設定
1. 新しい仮想マシンに使用する Azure サブスクリプションを決定します。
   
        (Get-AzureSubscription).SubscriptionName
2. ターゲットの Azure サブスクリプションを **$subscr** 変数に割り当てます。 次に、これを現在の Azure のサブスクリプションとして設定します。
   
        $subscr="<subscription name>"
        Select-AzureSubscription -SubscriptionName $subscr –Current
3. 既存のストレージ アカウントを確認します。 次のスクリプトでは、選択したリージョン内に存在するすべてのストレージ アカウントが表示されます。
   
        (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   
   > [!NOTE]
   > 新しいストレージ アカウントが必要な場合は、次の例のように、New-AzureStorageAccount コマンドを使用してすべてが小文字のストレージ アカウント名を最初に作成します: **New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation**
   > 
   > 
4. 対象のストレージ アカウント名を **$staccount**に割り当てます。 次に、 **Set-AzureSubscription** を使用してサブスクリプションと現在のストレージ アカウントを設定します。
   
        $staccount="<storage account name>"
        Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## <a name="select-a-sql-server-virtual-machine-image"></a>SQL Server 仮想マシン イメージの選択
1. ギャラリーから使用可能な SQL Server 仮想マシン イメージのリストを見つけます。 これらのイメージにはすべて、"SQL" で始まる **ImageFamily** プロパティが含まれています。 次のクエリでは、SQL Server が事前にインストールされている、使用可能なイメージ ファミリが表示されます。
   
        Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
2. 仮想マシン イメージのファミリが見つかったら、そのファミリに発行されたイメージが複数ある可能性があります。 次のスクリプトを使用して、選択したイメージ ファミリについて発行された最新の仮想マシン イメージ名を見つけます (**SQL Server 2016 RTM Enterprise on Windows Server 2012 R2** など)。
   
        $family="<ImageFamily value>"
        $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
   
        echo "Selected SQL Server image name:"
        echo "   $image"

## <a name="create-the-virtual-machine"></a>仮想マシンの作成
最後に、PowerShell を使用して仮想マシンを作成します。

1. 新しい VM をホストするクラウド サービスを作成します。 既存のクラウド サービスを代わりに使用することもできます。 クラウド サービスの短縮名を使用して、新しい **$svcname** 変数を作成します。
   
        $svcname = "<cloud service name>"
        New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
2. 仮想マシンの名前とサイズを指定します。 仮想マシンのサイズの詳細については、「[Azure の仮想マシンのサイズ](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
   
        $vmname="<machine name>"
        $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
        $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
3. ローカル管理者アカウントとパスワードを指定します。
   
        $cred=Get-Credential -Message "Type the name and password of the local administrator account."
        $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
4. 次のスクリプトを実行して、仮想マシンを作成します。
   
        New-AzureVM –ServiceName $svcname -VMs $vm1

> [!NOTE]
> 詳細な説明と構成オプションについては、「 **Azure PowerShell を使用して Windows ベースの仮想マシンを作成および事前構成する** 」の「 [コマンド セットの構築](../../virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」セクションを参照してください。
> 
> 

## <a name="example-powershell-script"></a>PowerShell サンプル スクリプト
次のスクリプトは、**SQL Server 2016 RTM Enterprise on Windows Server 2012 R2** 仮想マシンを作成する完全なスクリプトの例です。 このスクリプトを使用する場合、このトピックの前のステップに基づいて、初期変数をカスタマイズする必要があります。

    # Customize these variables based on your settings and requirements:
    $dcLocation = "East US"
    $subscr="mysubscription"
    $staccount="mystorageaccount"
    $family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
    $svcname = "mycloudservice"
    $vmname="myvirtualmachine"
    $vmsize="A5"

    # Set the current subscription and storage account
    # Comment out the New-AzureStorageAccount line if the account already exists
    Select-AzureSubscription -SubscriptionName $subscr –Current
    New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

    # Select the most recent VM image in this image family:
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    # Create the new cloud service; comment out this line if cloud service exists already:
    New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

    # Create the VM config:
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    # Set administrator credentials:
    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    # Create the SQL Server VM:
    New-AzureVM –ServiceName $svcname -VMs $vm1


## <a name="connect-with-remote-desktop"></a>リモート デスクトップへの接続
1. 現在のユーザーのドキュメント フォルダに .RDP ファイルを作成し、これらの仮想マシンを起動してセットアップを完了します。
   
        $documentspath = [environment]::getfolderpath("mydocuments")
        Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
2. ドキュメント ディレクトリで、RDP ファイルを起動します。 以前に提供された管理ユーザー名とパスワードを使用して接続します (たとえば、ユーザー名が VMAdmin であった場合、ユーザーとして「\VMAdmin」を指定し、パスワードを入力します)。
   
        cd $documentspath
        .\vm1.rdp

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>リモート アクセス用の SQL Server コンピューターの構成を完了する
リモート デスクトップを使用してコンピューターにログオンした後、「 [Azure VM で SQL Server への接続を構成する手順](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)」の説明に基づいて SQL Server を構成します。

## <a name="next-steps"></a>次のステップ
PowerShell を使用した仮想マシンのプロビジョニングの詳細については、 [仮想マシンのドキュメント](../../virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)を参照してください。 SQL Server および Premium Storage に関連するその他のスクリプトについては、「 [仮想マシン上での Azure Premium Storage と SQL Server の使用](virtual-machines-windows-classic-sql-server-premium-storage.md)」を参照してください。

多くの場合、次の手順はこの新しい SQL Server VM にデータベースを移行することです。 データベース移行方法については、「 [Azure VM の SQL Server へのデータベースの移行](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)」を参照してください。

Azure ポータルを使用して SQL Virtual Machines を作成する方法についても興味がある場合は、 [Azure での SQL Server 仮想マシンのプロビジョニング](../sql/virtual-machines-windows-portal-sql-server-provision.md)に関するページをご覧ください。 チュートリアルでは、この PowerShell のトピックで使用される従来のモデルではなく、推奨されるリソース マネージャー モデルを使用して VM を作成するポータルについて説明していることに注意してください。

これらのリソースのほかにも、 [Azure Virtual Machines における SQL Server の実行に関連するその他のトピック](../sql/virtual-machines-windows-sql-server-iaas-overview.md)もご覧になることをお勧めします。




<!--HONumber=Jan17_HO2-->


