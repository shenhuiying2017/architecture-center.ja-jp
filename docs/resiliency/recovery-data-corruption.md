---
title: "データの破損または偶発的な削除から復旧する"
description: "データの破損または偶発的なデータの削除から復旧する方法、回復力と高可用性を備えたフォールト トレラント アプリケーションを設計する方法、障害復旧を計画する方法に関する記事"
author: adamglick
ms.date: 08/18/2016
ms.openlocfilehash: b75c774f85c42f64472167897f08a7302ab50a3f
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2017
---
[!INCLUDE [header](../_includes/header.md)]
# <a name="azure-resiliency-technical-guidance-recovery-from-data-corruption-or-accidental-deletion"></a><span data-ttu-id="1103a-103">Azure の回復性技術ガイダンス - データの破損または偶発的な削除からの復旧</span><span class="sxs-lookup"><span data-stu-id="1103a-103">Azure resiliency technical guidance: recovery from data corruption or accidental deletion</span></span>
<span data-ttu-id="1103a-104">堅牢なビジネス継続性計画の一環として、データが破損した場合や誤って削除された場合に備えた計画を作成します。</span><span class="sxs-lookup"><span data-stu-id="1103a-104">Part of a robust business continuity plan is having a plan if your data gets corrupted or accidentally deleted.</span></span> <span data-ttu-id="1103a-105">以下に、アプリケーション エラーによってデータが破損したか、オペレーターのエラーによってデータが誤って削除された後の復旧に関する情報を示します。</span><span class="sxs-lookup"><span data-stu-id="1103a-105">The following is information about recovery after data has been corrupted or accidentally deleted, due to application errors or operator error.</span></span>

## <a name="virtual-machines"></a><span data-ttu-id="1103a-106">Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="1103a-106">Virtual Machines</span></span>
<span data-ttu-id="1103a-107">Azure Virtual Machines (サービスとしてのインフラストラクチャ VM と呼ばれる場合もあります) をアプリケーション エラーや偶発的な削除から保護するには、 [Azure Backup](https://azure.microsoft.com/services/backup/)を使用します。</span><span class="sxs-lookup"><span data-stu-id="1103a-107">To protect your Azure Virtual Machines (sometimes called infrastructure-as-a-service VMs) from application errors or accidental deletion, use [Azure Backup](https://azure.microsoft.com/services/backup/).</span></span> <span data-ttu-id="1103a-108">Azure Backup を使用すると、複数の VM ディスク間で一貫性のあるバックアップを作成できます。</span><span class="sxs-lookup"><span data-stu-id="1103a-108">Azure Backup enables the creation of backups that are consistent across multiple VM disks.</span></span> <span data-ttu-id="1103a-109">さらに、バックアップ コンテナーをリージョン間でレプリケートし、リージョン損失からの復旧に備えることができます。</span><span class="sxs-lookup"><span data-stu-id="1103a-109">In addition, the Backup Vault can be replicated across regions to provide recovery from region loss.</span></span>

## <a name="storage"></a><span data-ttu-id="1103a-110">Storage</span><span class="sxs-lookup"><span data-stu-id="1103a-110">Storage</span></span>
<span data-ttu-id="1103a-111">Azure Storage は自動レプリカによるデータの回復性を備えていますが、アプリケーション コード (または開発者やユーザー) による偶発的または意図しない削除や更新などから発生するデータの破損を防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="1103a-111">Note that while Azure Storage provides data resiliency through automated replicas, this does not prevent your application code (or developers/users) from corrupting data through accidental or unintended deletion, update, and so on.</span></span> <span data-ttu-id="1103a-112">アプリケーション エラーやユーザー エラーが発生した場合にデータの忠実性を維持するには、監査ログと共にセカンダリ ストレージの場所にデータをコピーするなどの高度な手法が必要です。</span><span class="sxs-lookup"><span data-stu-id="1103a-112">Maintaining data fidelity in the face of application or user error requires more advanced techniques, such as copying the data to a secondary storage location with an audit log.</span></span> <span data-ttu-id="1103a-113">開発者は BLOB の[スナップショット機能](https://msdn.microsoft.com/library/azure/ee691971.aspx)を利用できます。これは、特定の時点における BLOB の内容の読み取り専用スナップショットを作成する機能です。</span><span class="sxs-lookup"><span data-stu-id="1103a-113">Developers can take advantage of the blob [snapshot capability](https://msdn.microsoft.com/library/azure/ee691971.aspx), which can create read-only point-in-time snapshots of blob contents.</span></span> <span data-ttu-id="1103a-114">この機能を Azure Storage BLOB のデータ忠実性ソリューションの基盤として使用できます。</span><span class="sxs-lookup"><span data-stu-id="1103a-114">This can be used as the basis of a data-fidelity solution for Azure Storage blobs.</span></span>

### <a name="blob-and-table-storage-backup"></a><span data-ttu-id="1103a-115">BLOB と Table Storage のバックアップ</span><span class="sxs-lookup"><span data-stu-id="1103a-115">Blob and Table Storage Backup</span></span>
<span data-ttu-id="1103a-116">BLOB とテーブルは高い持続性を持ち、常にデータの最新状態を表します。</span><span class="sxs-lookup"><span data-stu-id="1103a-116">While blobs and tables are highly durable, they always represent the current state of the data.</span></span> <span data-ttu-id="1103a-117">意図しない変更やデータの削除からの復旧では、以前の状態にデータを復元する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1103a-117">Recovery from unwanted modification or deletion of data may require restoring data to a previous state.</span></span> <span data-ttu-id="1103a-118">これは、Azure で提供されている、特定の時点のコピーを保存して保持する機能を活用することで実現できます。</span><span class="sxs-lookup"><span data-stu-id="1103a-118">This can be achieved by taking advantage of the capabilities provided by Azure to store and retain point-in-time copies.</span></span>

<span data-ttu-id="1103a-119">Azure BLOB については、 [BLOB スナップショット機能](https://msdn.microsoft.com/library/ee691971.aspx)を使用して特定の時点のバックアップを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="1103a-119">For Azure Blobs, you can perform point-in-time backups using the [blob snapshot feature](https://msdn.microsoft.com/library/ee691971.aspx).</span></span> <span data-ttu-id="1103a-120">各スナップショットについて、BLOB 内で前回のスナップショットの状態との差分を保存するために必要なストレージのみに課金されます。</span><span class="sxs-lookup"><span data-stu-id="1103a-120">For each snapshot, you are only charged for the storage required to store the differences within the blob since the last snapshot state.</span></span> <span data-ttu-id="1103a-121">スナップショットには、差分を取るための基になる BLOB が存在している必要があるため、別の BLOB または別のストレージ アカウントにコピーすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1103a-121">The snapshots are dependent on the existence of the original blob they are based on, so a copy operation to another blob or even another storage account is advisable.</span></span> <span data-ttu-id="1103a-122">これにより、バックアップ データを誤って削除してしまうことから確実に保護できます。</span><span class="sxs-lookup"><span data-stu-id="1103a-122">This ensures that backup data is properly protected against accidental deletion.</span></span> <span data-ttu-id="1103a-123">Azure テーブルについては、別のテーブルまたは別の Azure BLOB をコピー先として特定の時点のコピーを作成できます。</span><span class="sxs-lookup"><span data-stu-id="1103a-123">For Azure Tables, you can make point-in-time copies to a different table or to Azure Blobs.</span></span> <span data-ttu-id="1103a-124">テーブルおよび BLOB に対してアプリケーション レベルのバックアップを実行する場合は、以下の詳細なガイダンスと例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1103a-124">More detailed guidance and examples of performing application-level backups of tables and blobs can be found here:</span></span>

* [<span data-ttu-id="1103a-125">Protecting Your Tables Against Application Errors (アプリケーション エラーからのテーブルの保護)</span><span class="sxs-lookup"><span data-stu-id="1103a-125">Protecting Your Tables Against Application Errors</span></span>](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
* [<span data-ttu-id="1103a-126">Protecting Your Blobs Against Application Errors (アプリケーション エラーからの BLOB の保護)</span><span class="sxs-lookup"><span data-stu-id="1103a-126">Protecting Your Blobs Against Application Errors</span></span>](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

## <a name="database"></a><span data-ttu-id="1103a-127">データベース</span><span class="sxs-lookup"><span data-stu-id="1103a-127">Database</span></span>
<span data-ttu-id="1103a-128">Azure SQL Database で使用できる [ビジネス継続性](/azure/sql-database/sql-database-business-continuity/) (バックアップ、復元) のオプションは複数あります。</span><span class="sxs-lookup"><span data-stu-id="1103a-128">There are several [business continuity](/azure/sql-database/sql-database-business-continuity/) (backup, restore) options available for Azure SQL Database.</span></span> <span data-ttu-id="1103a-129">データベースは、[データベース コピー](/azure/sql-database/sql-database-copy/)機能を使用するか、SQL Server bacpac ファイルを[エクスポート](/azure/sql-database/sql-database-export/)および[インポート](https://msdn.microsoft.com/library/hh710052.aspx)することでコピーできます。</span><span class="sxs-lookup"><span data-stu-id="1103a-129">Databases can be copied by using the [Database Copy](/azure/sql-database/sql-database-copy/) functionality, or by  [exporting](/azure/sql-database/sql-database-export/) and [importing](https://msdn.microsoft.com/library/hh710052.aspx) a SQL Server bacpac file.</span></span> <span data-ttu-id="1103a-130">データベース コピーではトランザクション上の一貫性が維持されますが、bacpac (Import/Export サービス) ではこの一貫性は維持されません。</span><span class="sxs-lookup"><span data-stu-id="1103a-130">Database Copy provides transactionally consistent results, while a bacpac (through the import/export service) does not.</span></span> <span data-ttu-id="1103a-131">これらのオプションの両方が、データセンター内でキュー ベースのサービスとして実行され、現時点では完了までの時間に関する SLA は提供されていません。</span><span class="sxs-lookup"><span data-stu-id="1103a-131">Both of these options run as queue-based services within the data center, and they do not currently provide a time-to-completion SLA.</span></span>

> [!NOTE]
> <span data-ttu-id="1103a-132">データベース コピーと Import/Export のオプションはソース データベースに大きな負荷がかかります。</span><span class="sxs-lookup"><span data-stu-id="1103a-132">The database copy and import/export options place a significant degree of load on the source database.</span></span> <span data-ttu-id="1103a-133">そのため、リソースの競合や調整イベントがトリガーされることがあります。</span><span class="sxs-lookup"><span data-stu-id="1103a-133">They can trigger resource contention or throttling events.</span></span>
> 
> 

### <a name="sql-database-backup"></a><span data-ttu-id="1103a-134">SQL Database のバックアップ</span><span class="sxs-lookup"><span data-stu-id="1103a-134">SQL Database Backup</span></span>
<span data-ttu-id="1103a-135">Microsoft Azure SQL Database の特定の時点のバックアップは、 [Azure SQL データベースをコピーする](/azure/sql-database/sql-database-copy/)ことによって実現します。</span><span class="sxs-lookup"><span data-stu-id="1103a-135">Point-in-time backups for Microsoft Azure SQL Database are achieved by [copying your Azure SQL database](/azure/sql-database/sql-database-copy/).</span></span> <span data-ttu-id="1103a-136">このコマンドを使用すると、同じ論理データベース サーバー上に、または別のサーバーに、トランザクション上の一貫性があるデータベースのコピーを作成できます。</span><span class="sxs-lookup"><span data-stu-id="1103a-136">You can use this command to create a transactionally consistent copy of a database on the same logical database server or to a different server.</span></span> <span data-ttu-id="1103a-137">いずれの場合も、データベース コピーは完全に機能し、ソース データベースから完全に独立しています。</span><span class="sxs-lookup"><span data-stu-id="1103a-137">In either case, the database copy is fully functional and completely independent of the source database.</span></span> <span data-ttu-id="1103a-138">作成する各コピーは、特定の時点の回復オプションを表します。</span><span class="sxs-lookup"><span data-stu-id="1103a-138">Each copy you create represents a point-in-time recovery option.</span></span> <span data-ttu-id="1103a-139">ソース データベース名で新しいデータベースの名前を変更して、データベースの状態を完全に回復できます。</span><span class="sxs-lookup"><span data-stu-id="1103a-139">You can recover the database state completely by renaming the new database with the source database name.</span></span> <span data-ttu-id="1103a-140">または、Transact-SQL クエリを使用して新しいデータベースからデータの特定のサブセットを復元することができます。</span><span class="sxs-lookup"><span data-stu-id="1103a-140">Alternatively, you can recover a specific subset of data from the new database by using Transact-SQL queries.</span></span> <span data-ttu-id="1103a-141">SQL Database の詳細については、「 [Azure SQL Database によるビジネス継続性の概要](/azure/sql-database/sql-database-business-continuity/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1103a-141">For additional details about SQL Database, see [Overview of business continuity with Azure SQL Database](/azure/sql-database/sql-database-business-continuity/).</span></span>

### <a name="sql-server-on-virtual-machines-backup"></a><span data-ttu-id="1103a-142">Virtual Machines 上の SQL Server のバックアップ</span><span class="sxs-lookup"><span data-stu-id="1103a-142">SQL Server on Virtual Machines Backup</span></span>
<span data-ttu-id="1103a-143">Azure のサービスとしてのインフラストラクチャである仮想マシン (多くの場合、IaaS または IaaS VM と呼ばれます) で使用される SQL Server の場合、従来のバックアップとログ配布という 2 つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="1103a-143">For SQL Server used with Azure infrastructure as a service virtual machines (often called IaaS or IaaS VMs), there are two options: traditional backups and log shipping.</span></span> <span data-ttu-id="1103a-144">従来のバックアップを使用すると、特定の時点の状態に復元できますが、回復プロセスは低速です。</span><span class="sxs-lookup"><span data-stu-id="1103a-144">Using traditional backups enables you to restore to a specific point in time, but the recovery process is slow.</span></span> <span data-ttu-id="1103a-145">従来のバックアップを復元するには、最初に完全バックアップから開始し、その後にそれ以降に作成されたバックアップを適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1103a-145">Restoring traditional backups requires starting with an initial full backup, and then applying any backups taken after that.</span></span> <span data-ttu-id="1103a-146">2 番目のオプションでは、ログ バックアップの復元を (たとえば 2 時間) 遅延するログ配布セッションを構成します。</span><span class="sxs-lookup"><span data-stu-id="1103a-146">The second option is to configure a log shipping session to delay the restore of log backups (for example, by two hours).</span></span> <span data-ttu-id="1103a-147">これにより、プライマリで発生したエラーから回復するための時間が確保されます。</span><span class="sxs-lookup"><span data-stu-id="1103a-147">This provides a window to recover from errors made on the primary.</span></span>

## <a name="other-azure-platform-services"></a><span data-ttu-id="1103a-148">その他の Azure プラットフォーム サービス</span><span class="sxs-lookup"><span data-stu-id="1103a-148">Other Azure platform services</span></span>
<span data-ttu-id="1103a-149">Azure の一部のプラットフォーム サービスは、ユーザー制御のストレージ アカウントまたは Azure SQL Database に情報を格納します。</span><span class="sxs-lookup"><span data-stu-id="1103a-149">Some Azure platform services store information in a user-controlled storage account or Azure SQL Database.</span></span> <span data-ttu-id="1103a-150">アカウントまたはストレージのリソースが削除されたか破損した場合は、サービスに重大なエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1103a-150">If the account or storage resource is deleted or corrupted, this could cause serious errors with the service.</span></span> <span data-ttu-id="1103a-151">そのような場合に備えて、バックアップを保持することが重要です。バックアップがあれば、リソースが削除されたか破損した場合にリソースを作成し直すことができます。</span><span class="sxs-lookup"><span data-stu-id="1103a-151">In these cases, it is important to maintain backups that would enable you to re-create these resources if they were deleted or corrupted.</span></span>

<span data-ttu-id="1103a-152">Azure Web Sites と Azure Mobile Services では、関連付けられたデータベースをバックアップし、管理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1103a-152">For Azure Web Sites and Azure Mobile Services, you must backup and maintain the associated databases.</span></span> <span data-ttu-id="1103a-153">Azure Media Service と Virtual Machines では、関連付けられた Azure Storage アカウントとそのアカウントのすべてのリソースを管理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1103a-153">For Azure Media Service and Virtual Machines, you must maintain the associated Azure Storage account and all resources in that account.</span></span> <span data-ttu-id="1103a-154">たとえば、Virtual Machines の場合、Azure Blob Storage で VM ディスクをバックアップし、管理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1103a-154">For example, for Virtual Machines, you must back up and manage the VM disks in Azure blob storage.</span></span>

## <a name="checklists-for-data-corruption-or-accidental-deletion"></a><span data-ttu-id="1103a-155">データの破損または偶発的な削除に対するチェックリスト</span><span class="sxs-lookup"><span data-stu-id="1103a-155">Checklists for data corruption or accidental deletion</span></span>
## <a name="virtual-machines-checklist"></a><span data-ttu-id="1103a-156">Virtual Machines のチェックリスト</span><span class="sxs-lookup"><span data-stu-id="1103a-156">Virtual Machines checklist</span></span>
1. <span data-ttu-id="1103a-157">このドキュメントの「Virtual Machines」セクションを確認する。</span><span class="sxs-lookup"><span data-stu-id="1103a-157">Review the Virtual Machines section of this document.</span></span>
2. <span data-ttu-id="1103a-158">Azure Backup (または Azure Blob Storage と VHD スナップショットを使用した独自のバックアップ システム) を使用して、VM ディスクをバックアップし、管理する。</span><span class="sxs-lookup"><span data-stu-id="1103a-158">Back up and maintain the VM disks with Azure Backup (or your own backup system by using Azure blob storage and VHD snapshots).</span></span>

## <a name="storage-checklist"></a><span data-ttu-id="1103a-159">Storage のチェックリスト</span><span class="sxs-lookup"><span data-stu-id="1103a-159">Storage checklist</span></span>
1. <span data-ttu-id="1103a-160">このドキュメントの「Storage」セクションを確認する。</span><span class="sxs-lookup"><span data-stu-id="1103a-160">Review the Storage section of this document.</span></span>
2. <span data-ttu-id="1103a-161">重要なストレージ リソースを定期的にバックアップする。</span><span class="sxs-lookup"><span data-stu-id="1103a-161">Regularly back up critical storage resources.</span></span>
3. <span data-ttu-id="1103a-162">BLOB に対してスナップショット機能の使用を検討する。</span><span class="sxs-lookup"><span data-stu-id="1103a-162">Consider using the snapshot feature for blobs.</span></span>

## <a name="database-checklist"></a><span data-ttu-id="1103a-163">Database のチェックリスト</span><span class="sxs-lookup"><span data-stu-id="1103a-163">Database checklist</span></span>
1. <span data-ttu-id="1103a-164">このドキュメントの「データベース」セクションを確認する。</span><span class="sxs-lookup"><span data-stu-id="1103a-164">Review the Database section of this document.</span></span>
2. <span data-ttu-id="1103a-165">データベース コピー コマンドを使用して特定の時点のバックアップを作成する。</span><span class="sxs-lookup"><span data-stu-id="1103a-165">Create point-in-time backups by using the Database Copy command.</span></span>

## <a name="sql-server-on-virtual-machines-backup-checklist"></a><span data-ttu-id="1103a-166">Virtual Machines 上の SQL Server のバックアップ チェックリスト</span><span class="sxs-lookup"><span data-stu-id="1103a-166">SQL Server on Virtual Machines Backup checklist</span></span>
1. <span data-ttu-id="1103a-167">このドキュメントの「Virtual Machines 上の SQL Server のバックアップ」セクションを確認する。</span><span class="sxs-lookup"><span data-stu-id="1103a-167">Review the SQL Server on Virtual Machines Backup section of this document.</span></span>
2. <span data-ttu-id="1103a-168">従来のバックアップと復元の手法を使用する。</span><span class="sxs-lookup"><span data-stu-id="1103a-168">Use traditional backup and restore techniques.</span></span>
3. <span data-ttu-id="1103a-169">遅延ログ配布セッションを作成する。</span><span class="sxs-lookup"><span data-stu-id="1103a-169">Create a delayed log shipping session.</span></span>

## <a name="web-apps-checklist"></a><span data-ttu-id="1103a-170">Web Apps のチェックリスト</span><span class="sxs-lookup"><span data-stu-id="1103a-170">Web Apps checklist</span></span>
1. <span data-ttu-id="1103a-171">関連付けられたデータベースがある場合は、バックアップして管理する。</span><span class="sxs-lookup"><span data-stu-id="1103a-171">Back up and maintain the associated database, if any.</span></span>

## <a name="media-services-checklist"></a><span data-ttu-id="1103a-172">Media Services のチェックリスト</span><span class="sxs-lookup"><span data-stu-id="1103a-172">Media Services checklist</span></span>
1. <span data-ttu-id="1103a-173">関連付けられたストレージ リソースをバックアップして管理する。</span><span class="sxs-lookup"><span data-stu-id="1103a-173">Back up and maintain the associated storage resources.</span></span>

## <a name="more-information"></a><span data-ttu-id="1103a-174">詳細情報</span><span class="sxs-lookup"><span data-stu-id="1103a-174">More information</span></span>
<span data-ttu-id="1103a-175">Azure のバックアップと復元の機能の詳細については、 [ストレージ、バックアップ、復旧のシナリオ](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1103a-175">For more information about backup and restore features in Azure, see [Storage, backup and recovery scenarios](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/).</span></span>

