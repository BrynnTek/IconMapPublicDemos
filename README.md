# Icon Map public demos

Ready-made **Icon Map for Fabric** demos that you install into your own Microsoft Fabric workspace, in your own
tenant, with your own sign-in. Nobody outside your organisation needs access to your tenant.

Each demo is a folder of Fabric items in Fabric's own Git format. You connect a workspace to the folder, Fabric
creates the items, and the demo's data arrives through a OneLake data share from Tekantis, so nothing is copied
by hand and no code runs.

| Folder | Demo | What it shows |
|---|---|---|
| [`london-buses`](london-buses) | **London Buses - Live Network** | A London bus network rebuilt from open data: every bus live on the map in 3D, headways and bunching, Countdown boards on 3D shelters, road disruptions, run times against the timetable, shelter need, walking access and neighbourhood context. |

Icon Map for Fabric is in private preview. These demos are worked examples by Tekantis Limited, built from
publicly available data. They are not affiliated with or endorsed by any bus operator or by Transport for London.

---

## Install the London Buses demo

About 15 minutes. You do every step yourself, in your own tenant.

### Before you start

| You need | Why |
|---|---|
| **Icon Map for Fabric** enabled for your tenant | Tekantis enables this for private preview customers. Without it, Fabric reports that the Icon Map item type isn't supported when it syncs. |
| A workspace on a **Fabric capacity or trial**, where you're an **Admin** | Only a workspace admin can connect a workspace to Git. |
| Your Fabric admin to have switched on, in **Admin portal → Tenant settings**: **Users can sync workspace items with GitHub repositories** and **Users can export items to Git repositories in other geographical locations** (both under Git integration), and **Users can accept external data shares** (Export and sharing settings) | The first two let the workspace connect to GitHub: Fabric can't confirm which region a GitHub repository is stored in, so without the second the GitHub option stays greyed out. The third lets you accept the demo's data. Settings can take about 15 minutes to apply. |
| A **GitHub** account | To hold your own copy of this repository. |
| The **data share invitation** from Tekantis | Tekantis sends it to the work email address you give us. The data stays in Tekantis's OneLake and is read in place. |

### 1. Make your own copy of this repository

On GitHub, open <https://github.com/Tekantis-Limited/IconMapPublicDemos> and select **Fork**. Choose your
organisation as the owner and keep the name.

Your workspace connects to *your* fork, not to ours, so you can save your own changes to it.

> Using Azure DevOps instead? Create a repository, select **Import repository**, and import
> `https://github.com/Tekantis-Limited/IconMapPublicDemos.git`. In step 4 choose **Azure DevOps** instead of
> GitHub. You don't need a token for Azure DevOps.

### 2. Create a GitHub token for Fabric

In GitHub: **Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token**.

- **Repository access:** *Only select repositories* → your fork
- **Permissions → Repository permissions → Contents:** *Read and write*

Copy the token. Fabric stores it as a connection that only you can use.

### 3. Create the workspace

In Fabric: **Workspaces → New workspace**. Name it, for example, `Icon Map Demo - London Buses`. Under
**Advanced**, choose your Fabric capacity or trial. Leave it empty: Git creates the items.

### 4. Connect the workspace to the demo folder

In the new workspace: **Workspace settings → Git integration → GitHub**.

1. **Add account**: give it a display name, paste the token from step 2, and enter your fork's URL
   (`https://github.com/<your organisation>/IconMapPublicDemos`). Select **Add**.
2. Choose: **Repository** your fork, **Branch** `main`, **Git folder** `london-buses`.
3. Select **Connect and sync**.

When it finishes, the workspace holds three items:

| Item | Type | What it is |
|---|---|---|
| **London Buses - Live Network (demo)** | Icon Map | The map: pages, layers, 3D models, charts and the live feeds |
| **DemoSettings** | Variable library | One variable, `DemoLakehouse`: which lakehouse the map reads its data from |
| **DemoData** | Lakehouse | Empty for now. The data arrives in step 5. |

### 5. Accept the data share into DemoData

Open the invitation link from Tekantis and sign in with the email address it was sent to.

1. Select **Accept**.
2. Choose this workspace and the lakehouse **DemoData**.
3. Choose **Files** as the location, and accept.

Check it worked: open **DemoData**. Under **Files** there is a folder **london-buses** (it shows as a shortcut)
holding 21 `.parquet` files and a `models` folder.

> The folder must be called exactly `london-buses` and sit directly under **Files**: the map reads
> `Files/london-buses/...`. If it arrived with another name, rename the shortcut.

### 6. Point the map at your lakehouse

Open **DemoSettings**. The variable **DemoLakehouse** still points at Tekantis's lakehouse, because Git copies
the variable's value as it is.

1. In the **DemoLakehouse** row, select the value, then **…** to choose an item.
2. Pick **DemoData** in *this* workspace.
3. Select **Save**.

### 7. Open the map

Open **London Buses - Live Network (demo)**. It reads `DemoLakehouse` as it opens, so every page now reads your
DemoData.

- The pages run along the top: **The network**, **Live fleet**, **Street level**, **Bunching** and the rest.
- The live buses, Countdown boards, alerts and disruptions come from a demo feed run by Tekantis. They need no
  setup and appear within a minute.
- To check the wiring: select **Editing**, open the **Data** tab and expand **Item references**. It lists
  `DemoLakehouse` with **DemoData** as its current value.

That's it. To save the resolved settings into the map itself, make any small change and save, or use
**Source control → Commit** to save it to your fork.

### If something isn't right

| You see | Do this |
|---|---|
| The **GitHub** option is greyed out | Your Fabric admin needs both Git integration settings in *Before you start*, including the one for other geographical locations. |
| Git sync fails, saying an item type isn't supported | Icon Map for Fabric isn't enabled for your tenant yet. Contact Tekantis. |
| The map opens but the analysis pages are empty | Check step 5: DemoData → Files → **london-buses** must exist with that exact name. |
| A yellow bar says a variable needs attention | Check step 6: **DemoLakehouse** must point at DemoData in this workspace, and the library must be named **DemoSettings**. |
| Analysis pages work but no buses move | The demo feed slows down when nobody is watching and catches up within a minute of being opened. Reload after a minute. |
| The invitation link says it has expired or isn't for you | Ask Tekantis for a new invitation to the email address you sign in to Fabric with. |

### Removing the demo

Delete the workspace. To stop reading Tekantis's data sooner, delete the **london-buses** shortcut in DemoData.
Tekantis can also end the share at any time.

---

## For Tekantis: preparing a client

1. Ask for the email address they sign in to Fabric with.
2. In the **Icon Map Demo - London Buses** workspace, open **DemoData**, select the **london-buses** folder under
   Files, then **… → External data share**, enter their email address, create the share, and copy the
   invitation link. (Or use the Fabric REST API: `POST /workspaces/{ws}/items/{lakehouse}/externalDataShares`
   with `paths: ["Files/london-buses"]`.)
3. Send them the link of this repository and the invitation link. Invitations expire after 90 days if they
   aren't accepted.

Licence: see [LICENSE](LICENSE) (MIT) and [NOTICE](NOTICE) (trademarks, data sources and the demo feed).
