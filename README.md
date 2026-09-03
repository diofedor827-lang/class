using System;
using MarsFPSKit;

// Token: 0x0200004C RID: 76
public class AccountController
{
	// Token: 0x17000012 RID: 18
	// (get) Token: 0x060001D3 RID: 467 RVA: 0x00009E90 File Offset: 0x00008090
	public static AccountController Instance
	{
		get
		{
			if (AccountController._instance == null)
			{
				AccountController._instance = new AccountController();
			}
			return AccountController._instance;
		}
	}

	// Token: 0x060001D4 RID: 468 RVA: 0x0000292A File Offset: 0x00000B2A
	private AccountController()
	{
	}

	// Token: 0x060001D5 RID: 469 RVA: 0x00005F78 File Offset: 0x00004178
	public void Initialize()
	{
	}

	// Token: 0x060001D6 RID: 470 RVA: 0x00009EA8 File Offset: 0x000080A8
	public AccountModel GetAccount()
	{
		if (this._accountModel == null)
		{
			this._accountModel = this.GetDefaultAccount();
		}
		return this._accountModel;
	}

	// Token: 0x060001D7 RID: 471 RVA: 0x00009EC4 File Offset: 0x000080C4
	public AccountModel GetDefaultAccount()
	{
		this._accountModel = new AccountModel
		{
			Name = Kit_GameSettings.userName,
			Points = 0,
			Statistics = new StatisticsModel
			{
				Kills = 0,
				Assists = 0,
				Deaths = 0,
				HeadShot = 0,
				WinMatch = 0,
				MatchCount = 0,
				InstalledBombs = 0,
				KilledByExplosion = 0,
				KillByMelee = 0,
				KillByShotGun = 0,
				KillByAssault = 0,
				KillByPistol = 0,
				KillBySniper = 0,
				CurrentLevel = 0
			}
		};
		return this._accountModel;
	}

	// Token: 0x060001D8 RID: 472 RVA: 0x00009F61 File Offset: 0x00008161
	public void SetAccount(AccountModel account)
	{
		this._accountModel = account;
	}

	// Token: 0x0400015A RID: 346
	private static AccountController _instance;

	// Token: 0x0400015B RID: 347
	private AccountModel _accountModel;
}
﻿using System;

// Token: 0x0200007D RID: 125
public class AccountModel
{
	// Token: 0x04000259 RID: 601
	public string Name;

	// Token: 0x0400025A RID: 602
	public StatisticsModel Statistics;

	// Token: 0x0400025B RID: 603
	public int Points;

	// Token: 0x0400025C RID: 604
	public ulong SteamID;
}
﻿using System;
using MarsFPSKit;
using Newtonsoft.Json;
using Steamworks;
using UnityEngine;

// Token: 0x0200004D RID: 77
public class AccountScreenController
{
	// Token: 0x060001D9 RID: 473 RVA: 0x00009F6A File Offset: 0x0000816A
	private AccountScreenController()
	{
	}

	// Token: 0x17000013 RID: 19
	// (get) Token: 0x060001DA RID: 474 RVA: 0x00009F97 File Offset: 0x00008197
	public static AccountScreenController Instance
	{
		get
		{
			if (AccountScreenController._instance == null)
			{
				AccountScreenController._instance = new AccountScreenController();
			}
			return AccountScreenController._instance;
		}
	}

	// Token: 0x060001DB RID: 475 RVA: 0x00009FB0 File Offset: 0x000081B0
	public void Initialize(Kit_GameInformation game)
	{
		this._accountScreenView = UnityEngine.Object.FindObjectOfType<AccountScreenView>();
		this._game = game;
		this._statistics = (IAccount)this._game.statistics;
		this._leveling = (IAccount)this._game.leveling;
		this.GetAccount();
	}

	// Token: 0x060001DC RID: 476 RVA: 0x0000A001 File Offset: 0x00008201
	private void GetAccount()
	{
		DataSender.Instance.GetAccount(SteamUser.GetSteamID().m_SteamID, new Action<string>(this.GetAccountCallback));
	}

	// Token: 0x060001DD RID: 477 RVA: 0x0000A024 File Offset: 0x00008224
	private AccountModel GetDefaultAccount()
	{
		this._accountModel = new AccountModel
		{
			Name = Kit_GameSettings.userName,
			Points = 0,
			Statistics = new StatisticsModel
			{
				Kills = 0,
				Assists = 0,
				Deaths = 0,
				HeadShot = 0,
				WinMatch = 0,
				MatchCount = 0,
				InstalledBombs = 0,
				KilledByExplosion = 0,
				KillByMelee = 0,
				KillByShotGun = 0,
				KillByAssault = 0,
				KillByPistol = 0,
				KillBySniper = 0,
				CurrentLevel = 0,
				CurrentXp = 0
			},
			SteamID = SteamUser.GetSteamID().m_SteamID
		};
		return this._accountModel;
	}

	// Token: 0x060001DE RID: 478 RVA: 0x0000A0D8 File Offset: 0x000082D8
	private void GetAccountCallback(string accountJson)
	{
		if (!accountJson.IsNullOrWhiteSpace())
		{
			this._accountModel = JsonConvert.DeserializeObject<AccountModel>(accountJson);
		}
		else
		{
			this._accountModel = this.GetDefaultAccount();
			string accountJson2 = JsonConvert.SerializeObject(this._accountModel);
			DataSender.Instance.SetAccount(accountJson2, delegate(string str)
			{
			});
		}
		this._statistics.Account = this._accountModel;
		if (this._leveling != null)
		{
			this._leveling.Account = this._accountModel;
		}
		this.AccountLoadedEvent();
		this._accountScreenView.SetView(this._accountModel);
	}

	// Token: 0x0400015C RID: 348
	private static AccountScreenController _instance;

	// Token: 0x0400015D RID: 349
	private AccountScreenView _accountScreenView;

	// Token: 0x0400015E RID: 350
	private AccountModel _accountModel;

	// Token: 0x0400015F RID: 351
	private Kit_GameInformation _game;

	// Token: 0x04000160 RID: 352
	private IAccount _statistics;

	// Token: 0x04000161 RID: 353
	private IAccount _leveling;

	// Token: 0x04000162 RID: 354
	public Action AccountLoadedEvent = delegate()
	{
	};
}
﻿using System;
using TMPro;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.UI;

// Token: 0x020000CA RID: 202
public class AccountScreenView : BaseMenuView
{
	// Token: 0x060004DD RID: 1245 RVA: 0x00016C7A File Offset: 0x00014E7A
	private void OnEnable()
	{
		this._backButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(this._previousMenu);
		});
	}

	// Token: 0x060004DE RID: 1246 RVA: 0x00016C98 File Offset: 0x00014E98
	private void OnDisable()
	{
		this._backButton.onClick.RemoveAllListeners();
	}

	// Token: 0x060004DF RID: 1247 RVA: 0x00016CAC File Offset: 0x00014EAC
	public void SetView(AccountModel accountModel)
	{
		this._accountModel = accountModel;
		this._killsText.text = this._accountModel.Statistics.Kills.ToString();
		this._deathsText.text = this._accountModel.Statistics.Deaths.ToString();
		this._assistsText.text = this._accountModel.Statistics.Assists.ToString();
		this._headshotText.text = Math.Round((double)((float)this._accountModel.Statistics.HeadShot / ((float)this._accountModel.Statistics.Kills + 1f) * 100f), 2).ToString() + "%";
		this._pointsText.text = this._accountModel.Points.ToString();
		this._kdText.text = Math.Round((double)(((float)this._accountModel.Statistics.Kills + 1f) / ((float)this._accountModel.Statistics.Deaths + 1f)), 2).ToString();
		double num = Math.Round((double)((float)(this._accountModel.Statistics.WinMatch + 1) / (float)(this._accountModel.Statistics.MatchCount + 1) * 100f), 1);
		this._winloseText.text = Mathf.Clamp((float)num, 0f, 100f).ToString() + "%";
		this._bombInstailed.text = this._accountModel.Statistics.InstalledBombs.ToString();
		this._killByExplosion.text = this._accountModel.Statistics.KilledByExplosion.ToString();
		this._matchCountText.text = this._accountModel.Statistics.MatchCount.ToString();
		this._playerNameText.text = this._accountModel.Name;
		int level = this._leveling.GetLevel();
		this._levelImage.sprite = this._leveling.levelUpIcons[Mathf.Clamp(level - 1, 0, this._leveling.levelUpIcons.Length - 1)];
		this._levelText.text = level.ToString();
		this._expImage.fillAmount = this._leveling.GetPercentageToNextLevel();
		int num2 = Mathf.Clamp(this._accountModel.Statistics.CurrentXp, 0, this._leveling.GetExpToNextLevel());
		this._expText.text = string.Format("{0}/{1}", num2, this._leveling.xpNeeded[Mathf.Clamp(level - 1, 0, this._leveling.maxLevel - 2)]);
		this.accountLoadEvent.Invoke();
	}

	// Token: 0x04000437 RID: 1079
	[SerializeField]
	private TextMeshProUGUI _playerNameText;

	// Token: 0x04000438 RID: 1080
	[SerializeField]
	private TextMeshProUGUI _killsText;

	// Token: 0x04000439 RID: 1081
	[SerializeField]
	private TextMeshProUGUI _deathsText;

	// Token: 0x0400043A RID: 1082
	[SerializeField]
	private TextMeshProUGUI _assistsText;

	// Token: 0x0400043B RID: 1083
	[SerializeField]
	private TextMeshProUGUI _headshotText;

	// Token: 0x0400043C RID: 1084
	[SerializeField]
	private TextMeshProUGUI _pointsText;

	// Token: 0x0400043D RID: 1085
	[SerializeField]
	private TextMeshProUGUI _kdText;

	// Token: 0x0400043E RID: 1086
	[SerializeField]
	private TextMeshProUGUI _bombInstailed;

	// Token: 0x0400043F RID: 1087
	[SerializeField]
	private TextMeshProUGUI _killByExplosion;

	// Token: 0x04000440 RID: 1088
	[SerializeField]
	private TextMeshProUGUI _winloseText;

	// Token: 0x04000441 RID: 1089
	[SerializeField]
	private TextMeshProUGUI _matchCountText;

	// Token: 0x04000442 RID: 1090
	[SerializeField]
	private Button _backButton;

	// Token: 0x04000443 RID: 1091
	[SerializeField]
	private Image _levelImage;

	// Token: 0x04000444 RID: 1092
	[SerializeField]
	private TextMeshProUGUI _levelText;

	// Token: 0x04000445 RID: 1093
	[SerializeField]
	private Image _expImage;

	// Token: 0x04000446 RID: 1094
	[SerializeField]
	private TextMeshProUGUI _expText;

	// Token: 0x04000447 RID: 1095
	[SerializeField]
	private LevelingServerData _leveling;

	// Token: 0x04000448 RID: 1096
	private AccountModel _accountModel;

	// Token: 0x04000449 RID: 1097
	[HideInInspector]
	public UnityEvent accountLoadEvent;
}
﻿using System;
using DG.Tweening;
using TMPro;
using UnityEngine;

// Token: 0x02000024 RID: 36
public class Achiwments : MonoBehaviour
{
	// Token: 0x060000F2 RID: 242 RVA: 0x00005EEC File Offset: 0x000040EC
	public virtual void PlaySequence()
	{
		this.SaveStats();
		this.mainSequence = DOTween.Sequence();
		this.mainSequence.Append(this._achivmentIcon.DOScale(Vector2.one, 0f)).Append(this._achivmentIcon.DOShakeScale(this.mainDuration, 0.1f, 10, 90f, true));
		this.mainSequence.OnComplete(new TweenCallback(this.EndSequence));
		this.AddPoints(this.points);
	}

	// Token: 0x060000F3 RID: 243 RVA: 0x00005F78 File Offset: 0x00004178
	public virtual void PlaySequence(int value)
	{
	}

	// Token: 0x060000F4 RID: 244 RVA: 0x00005F78 File Offset: 0x00004178
	protected virtual void SaveStats()
	{
	}

	// Token: 0x060000F5 RID: 245 RVA: 0x00005F7A File Offset: 0x0000417A
	public virtual void EndSequence()
	{
		this._achivmentIcon.DOScale(Vector2.zero, 0f);
		this.mainSequence.Kill(false);
	}

	// Token: 0x060000F6 RID: 246 RVA: 0x00005FA3 File Offset: 0x000041A3
	protected virtual void AddPoints(int point)
	{
		this.killProcess.AddScore(point);
	}

	// Token: 0x04000091 RID: 145
	public int points;

	// Token: 0x04000092 RID: 146
	[SerializeField]
	protected KillProcess killProcess;

	// Token: 0x04000093 RID: 147
	[SerializeField]
	protected RectTransform _achivmentIcon;

	// Token: 0x04000094 RID: 148
	[SerializeField]
	protected TMP_Text _achivmentText;

	// Token: 0x04000095 RID: 149
	[SerializeField]
	protected float mainDuration;

	// Token: 0x04000096 RID: 150
	protected Sequence mainSequence;
}
﻿using System;
using UnityEngine;
using UnityEngine.EventSystems;

// Token: 0x020000CB RID: 203
public class AnimateView : MonoBehaviour, IPointerEnterHandler, IEventSystemHandler, IPointerExitHandler
{
	// Token: 0x060004E2 RID: 1250 RVA: 0x00016F83 File Offset: 0x00015183
	private void OnEnable()
	{
		this._selectProcess = new LerpProcess();
		this._deselectProcess = new LerpProcess();
	}

	// Token: 0x060004E3 RID: 1251 RVA: 0x00016F9B File Offset: 0x0001519B
	private void Select()
	{
		this._selectProcess.StartProcess(delegate(float t)
		{
			this._object.transform.localScale = Vector3.Lerp(this._deselectedSize, this._selectedSize, this._curve.Evaluate(t));
		}, this._time);
	}

	// Token: 0x060004E4 RID: 1252 RVA: 0x00016FBA File Offset: 0x000151BA
	private void Deselect()
	{
		this._deselectProcess.StartProcess(delegate(float t)
		{
			this._object.transform.localScale = Vector3.Lerp(this._selectedSize, this._deselectedSize, this._curve.Evaluate(t));
		}, this._time);
	}

	// Token: 0x060004E5 RID: 1253 RVA: 0x00016FD9 File Offset: 0x000151D9
	public void OnPointerEnter(PointerEventData eventData)
	{
		this.Select();
	}

	// Token: 0x060004E6 RID: 1254 RVA: 0x00016FE1 File Offset: 0x000151E1
	public void OnPointerExit(PointerEventData eventData)
	{
		this.Deselect();
	}

	// Token: 0x0400044A RID: 1098
	[SerializeField]
	private Transform _object;

	// Token: 0x0400044B RID: 1099
	[SerializeField]
	private Color _highlightColor;

	// Token: 0x0400044C RID: 1100
	[SerializeField]
	private Color _normalColor;

	// Token: 0x0400044D RID: 1101
	[SerializeField]
	private AnimationCurve _curve;

	// Token: 0x0400044E RID: 1102
	[SerializeField]
	private float _time;

	// Token: 0x0400044F RID: 1103
	private Vector3 _selectedSize = new Vector3(1.1f, 1.1f, 1.1f);

	// Token: 0x04000450 RID: 1104
	private Vector3 _deselectedSize = Vector3.one;

	// Token: 0x04000451 RID: 1105
	private LerpProcess _selectProcess;

	// Token: 0x04000452 RID: 1106
	private LerpProcess _deselectProcess;

	// Token: 0x04000453 RID: 1107
	private bool _isHighlighted;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine.Events;

// Token: 0x020000AA RID: 170
public class ArrayEvent : UnityEvent<List<int>>
{
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.Weapons;

// Token: 0x02000098 RID: 152
public class AttachmentFireSoundOverride : Kit_AttachmentBehaviour
{
	// Token: 0x0600038E RID: 910 RVA: 0x00005F78 File Offset: 0x00004178
	public override void Selected(Kit_PlayerBehaviour pb, AttachmentUseCase auc)
	{
	}

	// Token: 0x0600038F RID: 911 RVA: 0x00005F78 File Offset: 0x00004178
	public override void Unselected(Kit_PlayerBehaviour pb, AttachmentUseCase auc)
	{
	}

	// Token: 0x040002C5 RID: 709
	public float Pitch = 1f;

	// Token: 0x040002C6 RID: 710
	public float Volume = 1f;

	// Token: 0x040002C7 RID: 711
	public bool SilencesWeapon;
}
﻿using System;
using MarsFPSKit.Weapons;
using UnityEngine;

// Token: 0x02000099 RID: 153
public class AttachmentMonoBehaviour : MonoBehaviour
{
	// Token: 0x17000044 RID: 68
	// (get) Token: 0x06000391 RID: 913 RVA: 0x0001057C File Offset: 0x0000E77C
	public string Name
	{
		get
		{
			return this._name;
		}
	}

	// Token: 0x17000045 RID: 69
	// (get) Token: 0x06000392 RID: 914 RVA: 0x00010584 File Offset: 0x0000E784
	public int ID
	{
		get
		{
			return this._id;
		}
	}

	// Token: 0x17000046 RID: 70
	// (get) Token: 0x06000393 RID: 915 RVA: 0x0001058C File Offset: 0x0000E78C
	public bool IsStartingItem
	{
		get
		{
			return this._isStartingItem;
		}
	}

	// Token: 0x17000047 RID: 71
	// (get) Token: 0x06000394 RID: 916 RVA: 0x00010594 File Offset: 0x0000E794
	public Kit_AttachmentBehaviour[] AttachmentBehaviours
	{
		get
		{
			return this._attachmentBehaviours;
		}
	}

	// Token: 0x040002C8 RID: 712
	[SerializeField]
	private string _name;

	// Token: 0x040002C9 RID: 713
	[SerializeField]
	private int _id;

	// Token: 0x040002CA RID: 714
	[SerializeField]
	private bool _isStartingItem;

	// Token: 0x040002CB RID: 715
	[SerializeField]
	private Kit_AttachmentBehaviour[] _attachmentBehaviours;

	// Token: 0x040002CC RID: 716
	public Sprite icon;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.Weapons;

// Token: 0x0200009A RID: 154
public class AttachmentRecoilOverride : Kit_AttachmentBehaviour
{
	// Token: 0x06000396 RID: 918 RVA: 0x00005F78 File Offset: 0x00004178
	public override void Selected(Kit_PlayerBehaviour pb, AttachmentUseCase auc)
	{
	}

	// Token: 0x06000397 RID: 919 RVA: 0x00005F78 File Offset: 0x00004178
	public override void Unselected(Kit_PlayerBehaviour pb, AttachmentUseCase auc)
	{
	}

	// Token: 0x040002CD RID: 717
	public float RecoilModifier = 1f;
}
﻿using System;

// Token: 0x0200004E RID: 78
public abstract class BaseMenuSwitcher
{
	// Token: 0x060001DF RID: 479
	public abstract void ShowMenu(bool isAnimated);

	// Token: 0x060001E0 RID: 480
	public abstract void HideMenu(bool isAnimated);
}
﻿using System;
using UnityEngine;

// Token: 0x020000CD RID: 205
public class BaseMenuView : MonoBehaviour
{
	// Token: 0x1700005B RID: 91
	// (get) Token: 0x060004F0 RID: 1264 RVA: 0x00017158 File Offset: 0x00015358
	public MenuType MenuType
	{
		get
		{
			return this._menuType;
		}
	}

	// Token: 0x1700005C RID: 92
	// (get) Token: 0x060004F1 RID: 1265 RVA: 0x00017160 File Offset: 0x00015360
	public MenuType PreviousMenu
	{
		get
		{
			return this._previousMenu;
		}
	}

	// Token: 0x1700005D RID: 93
	// (get) Token: 0x060004F2 RID: 1266 RVA: 0x00017168 File Offset: 0x00015368
	public bool IsActive
	{
		get
		{
			return this._isActive;
		}
	}

	// Token: 0x060004F3 RID: 1267 RVA: 0x00017170 File Offset: 0x00015370
	public virtual void MenuInit(ISwitchableMenu menuController)
	{
		this._menuController = menuController;
		this._menuSwitcher = new CanvasGroupMenuSwitcher(this._canvasGroup);
	}

	// Token: 0x060004F4 RID: 1268 RVA: 0x0001718A File Offset: 0x0001538A
	public virtual void ShowMenu(bool isAnimated)
	{
		this._menuSwitcher.ShowMenu(isAnimated);
		this._isActive = true;
	}

	// Token: 0x060004F5 RID: 1269 RVA: 0x0001719F File Offset: 0x0001539F
	public virtual void HideMenu(bool isAnimated)
	{
		this._menuSwitcher.HideMenu(isAnimated);
		this._isActive = false;
	}

	// Token: 0x0400045C RID: 1116
	[SerializeField]
	protected RectTransform _contentTransform;

	// Token: 0x0400045D RID: 1117
	[SerializeField]
	protected CanvasGroup _canvasGroup;

	// Token: 0x0400045E RID: 1118
	[SerializeField]
	protected MenuType _menuType;

	// Token: 0x0400045F RID: 1119
	[SerializeField]
	protected MenuType _previousMenu;

	// Token: 0x04000460 RID: 1120
	protected BaseMenuSwitcher _menuSwitcher;

	// Token: 0x04000461 RID: 1121
	protected ISwitchableMenu _menuController;

	// Token: 0x04000462 RID: 1122
	private bool _isActive;
}
﻿using System;
using System.Collections.Generic;

// Token: 0x02000042 RID: 66
[Serializable]
public class BattleLevelModel
{
	// Token: 0x040000F5 RID: 245
	public int ExpToNext = 1000;

	// Token: 0x040000F6 RID: 246
	public bool IsFreeGetted;

	// Token: 0x040000F7 RID: 247
	public bool IsPremiumGetted;

	// Token: 0x040000F8 RID: 248
	public List<int> FreeRewards;

	// Token: 0x040000F9 RID: 249
	public List<int> PremiumRewards;
}
﻿using System;
using System.Collections.Generic;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000043 RID: 67
public class BattleLevelView : MonoBehaviour
{
	// Token: 0x14000001 RID: 1
	// (add) Token: 0x06000183 RID: 387 RVA: 0x000087CC File Offset: 0x000069CC
	// (remove) Token: 0x06000184 RID: 388 RVA: 0x00008804 File Offset: 0x00006A04
	public event Action<int> OnItemClick = delegate(int <p0>)
	{
	};

	// Token: 0x06000185 RID: 389 RVA: 0x0000883C File Offset: 0x00006A3C
	public void ViewInit(int number, BattlePassModel battlePass, ItemsCardImagesBank imageBank, RarityContentBank rarityBank)
	{
		this._numberText.text = number.ToString();
		BattleLevelModel battleLevelModel = battlePass.BattleLevels[number];
		int showedLevels = battlePass.ShowedLevels;
		for (int i = 0; i < battleLevelModel.FreeRewards.Count; i++)
		{
			ItemCardImage itemCardImage = imageBank.GetItemCardImage(battleLevelModel.FreeRewards[i]);
			RarityContent rarityContent = rarityBank.GetRarityContent(itemCardImage.Rarity);
			Sprite background = rarityContent.Background;
			Sprite itemImage = itemCardImage.ItemImage;
			Sprite rarityImage = rarityContent.RarityImage;
			BattleRewardView battleRewardView = UnityEngine.Object.Instantiate<BattleRewardView>(this._rewardImagePrefab, this._freeIconsContent);
			battleRewardView.ViewInit(background, itemImage, rarityImage, battleLevelModel.IsFreeGetted, battleLevelModel.FreeRewards[i]);
			battleRewardView.OnClick += delegate(int id)
			{
				this.OnItemClick(id);
			};
		}
		for (int j = 0; j < battleLevelModel.PremiumRewards.Count; j++)
		{
			ItemCardImage itemCardImage2 = imageBank.GetItemCardImage(battleLevelModel.PremiumRewards[j]);
			RarityContent rarityContent2 = rarityBank.GetRarityContent(itemCardImage2.Rarity);
			Sprite background2 = rarityContent2.Background;
			Sprite itemImage2 = itemCardImage2.ItemImage;
			Sprite rarityImage2 = rarityContent2.RarityImage;
			BattleRewardView battleRewardView2 = UnityEngine.Object.Instantiate<BattleRewardView>(this._rewardImagePrefab, this._premiumIconsContent);
			battleRewardView2.ViewInit(background2, itemImage2, rarityImage2, battleLevelModel.IsPremiumGetted, battleLevelModel.PremiumRewards[j]);
			battleRewardView2.OnClick += delegate(int id)
			{
				this.OnItemClick(id);
			};
		}
		this._levelImage.color = ((number == showedLevels) ? this._selectedColor : this._deselectedColor);
		this._freeDarkerImage.enabled = (number > showedLevels);
		if (!battlePass.Premium)
		{
			this._premiumDarkerImage.enabled = true;
		}
		else
		{
			this._premiumDarkerImage.enabled = (number > showedLevels);
		}
		if (this._observeProcess == null)
		{
			this._observeProcess = new ObserveProcess();
		}
	}

	// Token: 0x06000186 RID: 390 RVA: 0x00008A00 File Offset: 0x00006C00
	public void OpenAction(Action onEndAction)
	{
		float speed = 1f / this._openTime;
		this._observeProcess.StartProcess(delegate
		{
			if (this._time <= 1f)
			{
				this._openImageTransform.localScale = Vector2.Lerp(this._bigScale, Vector2.one, Mathf.Clamp01(2f * this._time));
				this._openImageCanvasGroup.alpha = Mathf.Lerp(1f, 0f, Mathf.Clamp01(2f * this._time - 1f));
				this._time += speed * Time.deltaTime;
				return;
			}
			this._openImageTransform.localScale = Vector2.Lerp(this._bigScale, Vector2.one, Mathf.Clamp01(2f));
			this._openImageCanvasGroup.alpha = Mathf.Lerp(1f, 0f, Mathf.Clamp01(1f));
			onEndAction();
			this._observeProcess.EndProcess();
		});
	}

	// Token: 0x06000187 RID: 391 RVA: 0x00008A4C File Offset: 0x00006C4C
	private Sprite GetSpriteByID(int id, List<ItemCardImage> items)
	{
		for (int i = 0; i < items.Count; i++)
		{
			if (items[i].ID == id)
			{
				return items[i].ItemImage;
			}
		}
		return null;
	}

	// Token: 0x040000FB RID: 251
	[SerializeField]
	private BattleRewardView _rewardImagePrefab;

	// Token: 0x040000FC RID: 252
	[SerializeField]
	private TextMeshProUGUI _numberText;

	// Token: 0x040000FD RID: 253
	[SerializeField]
	private RectTransform _freeIconsContent;

	// Token: 0x040000FE RID: 254
	[SerializeField]
	private RectTransform _premiumIconsContent;

	// Token: 0x040000FF RID: 255
	[SerializeField]
	private Color _selectedColor;

	// Token: 0x04000100 RID: 256
	[SerializeField]
	private Color _deselectedColor;

	// Token: 0x04000101 RID: 257
	[SerializeField]
	private Image _levelImage;

	// Token: 0x04000102 RID: 258
	[SerializeField]
	private Image _freeDarkerImage;

	// Token: 0x04000103 RID: 259
	[SerializeField]
	private Image _premiumDarkerImage;

	// Token: 0x04000104 RID: 260
	[SerializeField]
	private float _openTime = 0.25f;

	// Token: 0x04000105 RID: 261
	[SerializeField]
	private RectTransform _openImageTransform;

	// Token: 0x04000106 RID: 262
	[SerializeField]
	private CanvasGroup _openImageCanvasGroup;

	// Token: 0x04000107 RID: 263
	[SerializeField]
	private Vector2 _bigScale;

	// Token: 0x04000108 RID: 264
	private ObserveProcess _observeProcess;

	// Token: 0x04000109 RID: 265
	private float _time;
}
﻿using System;
using System.Collections.Generic;
using System.Linq;
using MarsFPSKit;
using Newtonsoft.Json;
using Steamworks;
using UnityEngine;
using UnityEngine.Events;

// Token: 0x02000044 RID: 68
public class BattlePassController
{
	// Token: 0x14000002 RID: 2
	// (add) Token: 0x0600018B RID: 395 RVA: 0x00008AD0 File Offset: 0x00006CD0
	// (remove) Token: 0x0600018C RID: 396 RVA: 0x00008B08 File Offset: 0x00006D08
	public event Action<int> OnLevelUp = delegate(int <p0>)
	{
	};

	// Token: 0x1700000F RID: 15
	// (get) Token: 0x0600018D RID: 397 RVA: 0x00008B3D File Offset: 0x00006D3D
	public static BattlePassController Instance
	{
		get
		{
			if (BattlePassController._instance == null)
			{
				BattlePassController._instance = new BattlePassController();
			}
			return BattlePassController._instance;
		}
	}

	// Token: 0x17000010 RID: 16
	// (get) Token: 0x0600018E RID: 398 RVA: 0x00008B55 File Offset: 0x00006D55
	public bool IsRewardsThere
	{
		get
		{
			return this._rewards != null && this._rewards.Count > 0;
		}
	}

	// Token: 0x0600018F RID: 399 RVA: 0x00008B6F File Offset: 0x00006D6F
	private BattlePassController()
	{
	}

	// Token: 0x06000190 RID: 400 RVA: 0x00008B9C File Offset: 0x00006D9C
	public void Initialize()
	{
		this._view = UnityEngine.Object.FindObjectOfType<BattlePassScreenView>();
		this._game = Resources.Load<Kit_GameInformation>("Game");
		BattlePassScreenView view = this._view;
		if (view != null)
		{
			view.ViewInit();
		}
		if (this._battlePass == null)
		{
			this.LoadBattlePass();
			this._game.leveling.OnAddXp += this.AddExp;
		}
		else
		{
			this.SaveBattlePass();
		}
		StoreController.Instance.inventoryResultEvent.RemoveListener(new UnityAction(this.OnItemsUpdate));
		StoreController.Instance.inventoryResultEvent.AddListener(new UnityAction(this.OnItemsUpdate));
	}

	// Token: 0x06000191 RID: 401 RVA: 0x00008C40 File Offset: 0x00006E40
	public void GetRewards()
	{
		int[] array = new int[this._rewards.Count];
		this._rewards.CopyTo(array);
		StoreController.Instance.GetRewardItem(array);
		this._view.ShowRewards(array.ToList<int>());
		for (int i = 0; i < this._rewardedModels.Count; i++)
		{
			this._rewardedModels[i].IsFreeGetted = true;
			if (this._battlePass.Premium)
			{
				this._rewardedModels[i].IsPremiumGetted = true;
			}
		}
		this._rewards.Clear();
		this._rewardedModels.Clear();
		this.SaveBattlePass();
	}

	// Token: 0x06000192 RID: 402 RVA: 0x00008CE9 File Offset: 0x00006EE9
	public void GetPremium()
	{
		StoreController.Instance.AwakePurchase(this._battlePass.SteamItemId, 1);
	}

	// Token: 0x06000193 RID: 403 RVA: 0x00008D01 File Offset: 0x00006F01
	public void TestExp()
	{
		this.LevelUp(1, false);
		this.Initialize();
	}

	// Token: 0x06000194 RID: 404 RVA: 0x00008D11 File Offset: 0x00006F11
	public void DecrementExp()
	{
		this.LevelUp(-1, false);
		this.Initialize();
	}

	// Token: 0x06000195 RID: 405 RVA: 0x00008D21 File Offset: 0x00006F21
	public void TestGetPremium()
	{
		StoreController.Instance.GetRewardItem(new int[]
		{
			this._battlePass.SteamItemId
		});
	}

	// Token: 0x06000196 RID: 406 RVA: 0x00008D41 File Offset: 0x00006F41
	private void LoadBattlePass()
	{
		DataSender.Instance.GetBattlePass(SteamUser.GetSteamID().m_SteamID, new Action<string>(this.GetBattlePassCallback));
	}

	// Token: 0x06000197 RID: 407 RVA: 0x00008D64 File Offset: 0x00006F64
	private void SaveBattlePass()
	{
		string jsonBattlePass = JsonConvert.SerializeObject(this._battlePass);
		DataSender.Instance.SetAccountBattlePass(SteamUser.GetSteamID().m_SteamID, jsonBattlePass, delegate(string str)
		{
		});
		this._view.ViewInit();
		this._view.SetView(this._battlePass, this.IsRewardsThere);
	}

	// Token: 0x06000198 RID: 408 RVA: 0x00008DD4 File Offset: 0x00006FD4
	private void AddExp(int exp)
	{
		int num = this._battlePass.Premium ? (2 * exp) : exp;
		this._battlePass.Exp += num;
		if (this._battlePass.Exp >= this._battlePass.BattleLevels[this._battlePass.Level].ExpToNext)
		{
			int exp2 = this._battlePass.Exp - this._battlePass.BattleLevels[this._battlePass.Level].ExpToNext;
			this._battlePass.Exp = exp2;
			this.LevelUp(1, false);
		}
	}

	// Token: 0x06000199 RID: 409 RVA: 0x00008E78 File Offset: 0x00007078
	public void LevelUp(int levels = 1, bool viewUpdate = false)
	{
		if (this._battlePass.Level + levels < this._battlePass.BattleLevels.Count)
		{
			this._battlePass.Level += levels;
			this.Reward();
			this.OnLevelUp(this._battlePass.Level);
		}
		else
		{
			this._battlePass.Level = this._battlePass.BattleLevels.Count - 1;
			this.Reward();
		}
		if (viewUpdate)
		{
			BattlePassScreenView view = this._view;
			if (view != null)
			{
				view.SetView(this._battlePass, this._rewards != null && this._rewards.Count > 0);
			}
			this._view.ShowLevel();
			this.SaveBattlePass();
		}
	}

	// Token: 0x0600019A RID: 410 RVA: 0x00008F3C File Offset: 0x0000713C
	private void Reward()
	{
		this._rewards = new List<int>();
		this._rewardedModels = new List<BattleLevelModel>();
		Debug.Log(string.Format("Level: {0}", this._battlePass.Level));
		for (int i = 0; i < this._battlePass.Level + 1; i++)
		{
			BattleLevelModel battleLevelModel = this._battlePass.BattleLevels[i];
			if (!battleLevelModel.IsFreeGetted && battleLevelModel.FreeRewards.Count > 0)
			{
				this._rewards.AddRange(battleLevelModel.FreeRewards);
				this._rewardedModels.Add(battleLevelModel);
			}
			if (!battleLevelModel.IsPremiumGetted && this._battlePass.Premium && battleLevelModel.PremiumRewards.Count > 0)
			{
				this._rewards.AddRange(battleLevelModel.PremiumRewards);
				if (!this._rewardedModels.Contains(battleLevelModel))
				{
					this._rewardedModels.Add(battleLevelModel);
				}
			}
		}
	}

	// Token: 0x0600019B RID: 411 RVA: 0x00009030 File Offset: 0x00007230
	private void GetBattlePassCallback(string json)
	{
		List<BattlePassModel> list = JsonConvert.DeserializeObject<List<BattlePassModel>>(json);
		if (list.Count > 1)
		{
			if (list[0].SteamItemId == list[1].SteamItemId && list[0].CreateDateString == list[1].CreateDateString)
			{
				this._battlePass = list[0];
			}
			else
			{
				this._battlePass = list[1];
			}
		}
		else
		{
			this._battlePass = list[0];
		}
		this.LevelUp(0, false);
		if (this._battlePass != null)
		{
			this._view.ViewInit();
			this._battlePass.Premium = StoreController.Instance.openItems.Contains(this._battlePass.SteamItemId);
			this._view.SetView(this._battlePass, this.IsRewardsThere);
		}
	}

	// Token: 0x0600019C RID: 412 RVA: 0x00009108 File Offset: 0x00007308
	private void OnItemsUpdate()
	{
		if (this._battlePass != null)
		{
			this._battlePass.Premium = StoreController.Instance.openItems.Contains(this._battlePass.SteamItemId);
			this.LevelUp(0, false);
			this._view.SetView(this._battlePass, this.IsRewardsThere);
		}
	}

	// Token: 0x0400010B RID: 267
	private static BattlePassController _instance;

	// Token: 0x0400010C RID: 268
	private BattlePassModel _battlePass;

	// Token: 0x0400010D RID: 269
	private BattlePassScreenView _view;

	// Token: 0x0400010E RID: 270
	private Kit_GameInformation _game;

	// Token: 0x0400010F RID: 271
	private List<int> _rewards;

	// Token: 0x04000110 RID: 272
	private List<BattleLevelModel> _rewardedModels;
}
﻿using System;
using System.Collections.Generic;

// Token: 0x02000045 RID: 69
[Serializable]
public class BattlePassModel
{
	// Token: 0x04000111 RID: 273
	public int Level;

	// Token: 0x04000112 RID: 274
	public int Exp;

	// Token: 0x04000113 RID: 275
	public int ShowedLevels;

	// Token: 0x04000114 RID: 276
	public string Name;

	// Token: 0x04000115 RID: 277
	public string EndDateString;

	// Token: 0x04000116 RID: 278
	public int EndDateInt;

	// Token: 0x04000117 RID: 279
	public int SteamItemId;

	// Token: 0x04000118 RID: 280
	public string CreateDateString;

	// Token: 0x04000119 RID: 281
	public bool Premium;

	// Token: 0x0400011A RID: 282
	public List<BattleLevelModel> BattleLevels;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000046 RID: 70
public class BattlePassScreenView : BaseMenuView
{
	// Token: 0x0600019E RID: 414 RVA: 0x00009164 File Offset: 0x00007364
	private void OnEnable()
	{
		this._backButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(base.PreviousMenu);
		});
		this._rewardButton.onClick.AddListener(delegate()
		{
			BattlePassController.Instance.GetRewards();
		});
		this._buyPremiumButton.onClick.AddListener(delegate()
		{
			BattlePassController.Instance.GetPremium();
		});
		this._buyOneLevelButton.onClick.AddListener(delegate()
		{
			StoreController.Instance.AwakePurchase(this._oneLevelId, 1);
		});
		this._buyFiveLevelsButton.onClick.AddListener(delegate()
		{
			StoreController.Instance.AwakePurchase(this._fiveLevelId, 1);
		});
		this._buyTenLevelsButton.onClick.AddListener(delegate()
		{
			StoreController.Instance.AwakePurchase(this._tenLevelId, 1);
		});
		this._drawer.OnSequenceEnd += this.ResetItemShowing;
		LocalizationController.Instance.OnLanguageChanged += this.Localize;
	}

	// Token: 0x0600019F RID: 415 RVA: 0x0000926C File Offset: 0x0000746C
	private void OnDisable()
	{
		this._backButton.onClick.RemoveAllListeners();
		this._rewardButton.onClick.RemoveAllListeners();
		this._buyPremiumButton.onClick.RemoveAllListeners();
		this._buyOneLevelButton.onClick.RemoveAllListeners();
		this._buyFiveLevelsButton.onClick.RemoveAllListeners();
		this._buyTenLevelsButton.onClick.RemoveAllListeners();
		this._drawer.OnSequenceEnd -= this.ResetItemShowing;
		LocalizationController.Instance.OnLanguageChanged -= this.Localize;
	}

	// Token: 0x060001A0 RID: 416 RVA: 0x00009306 File Offset: 0x00007506
	public void ViewInit()
	{
		this._game = Resources.Load<Kit_GameInformation>("Game");
		this._imagesBank = Resources.Load<ItemsCardImagesBank>("Items Card Images Bank");
		this._rarityBank = Resources.Load<RarityContentBank>("Rarity Content Bank");
	}

	// Token: 0x060001A1 RID: 417 RVA: 0x00009338 File Offset: 0x00007538
	public void SetView(BattlePassModel battlePass, bool isRewardsThere)
	{
		this._battlePass = battlePass;
		this.ClearContent();
		this.SetLevels(battlePass);
		this.SetUpContentSize();
		this._scrollRect.horizontalNormalizedPosition = (float)battlePass.ShowedLevels / (float)battlePass.BattleLevels.Count;
		this.SetUpInfo(battlePass);
		this._rewardButton.gameObject.SetActive(isRewardsThere && base.IsActive);
		this._battlePassHighlighter.SetOn(isRewardsThere);
		this._premiumBlock.SetActive(!battlePass.Premium);
		this._levelsBlock.SetActive(battlePass.Premium);
		this._premiumDarkerImage.enabled = !battlePass.Premium;
		this.SetUpPrices();
	}

	// Token: 0x060001A2 RID: 418 RVA: 0x000093EC File Offset: 0x000075EC
	public override void MenuInit(ISwitchableMenu menuController)
	{
		this._menuController = menuController;
		this._menuSwitcher = new CameraMovementMenuSwitcher(this._canvasGroup, this._renderCanvasGroup, this._battlePassCamera, delegate()
		{
			this.ShowLevel();
		}, delegate()
		{
			this._drawer.SetCamera(false);
		});
	}

	// Token: 0x060001A3 RID: 419 RVA: 0x0000942A File Offset: 0x0000762A
	public override void ShowMenu(bool isAnimated)
	{
		base.ShowMenu(isAnimated);
		this._drawer.SetCamera(true);
		this._rewardButton.gameObject.SetActive(BattlePassController.Instance.IsRewardsThere);
	}

	// Token: 0x060001A4 RID: 420 RVA: 0x00009459 File Offset: 0x00007659
	public override void HideMenu(bool isAnimated)
	{
		base.HideMenu(isAnimated);
		this._rewardButton.gameObject.SetActive(false);
	}

	// Token: 0x060001A5 RID: 421 RVA: 0x00009473 File Offset: 0x00007673
	public void ShowRewards(List<int> rewards)
	{
		this._isRewardShowing = true;
		this._drawer.ShowRewards(rewards);
	}

	// Token: 0x060001A6 RID: 422 RVA: 0x00009488 File Offset: 0x00007688
	public void ShowLevel()
	{
		if (this._battlePass.ShowedLevels < this._battlePass.Level)
		{
			this._battlePass.ShowedLevels++;
			this._levelMessageView.ShowMessage(string.Format("{0} {1}", LocalizationController.Instance.GetText("PlayerStatistics", "Level"), this._battlePass.ShowedLevels));
			this._levelViews[this._battlePass.ShowedLevels].ViewInit(this._battlePass.ShowedLevels, this._battlePass, this._imagesBank, this._rarityBank);
			this._levelViews[Mathf.Clamp(this._battlePass.ShowedLevels - 1, 0, this._battlePass.BattleLevels.Count - 1)].ViewInit(this._battlePass.ShowedLevels - 1, this._battlePass, this._imagesBank, this._rarityBank);
			this._levelViews[this._battlePass.ShowedLevels].OpenAction(delegate
			{
				this.ShowLevel();
			});
		}
	}

	// Token: 0x060001A7 RID: 423 RVA: 0x000095B0 File Offset: 0x000077B0
	private void ClearContent()
	{
		if (this._levelViews != null && this._levelViews.Count > 0)
		{
			for (int i = 0; i < this._levelViews.Count; i++)
			{
				UnityEngine.Object.Destroy(this._levelViews[i].gameObject);
			}
		}
		this._levelViews = new List<BattleLevelView>();
	}

	// Token: 0x060001A8 RID: 424 RVA: 0x0000960C File Offset: 0x0000780C
	private void SetLevels(BattlePassModel battlePass)
	{
		for (int i = 0; i < battlePass.BattleLevels.Count; i++)
		{
			BattleLevelView battleLevelView = UnityEngine.Object.Instantiate<BattleLevelView>(this._battleLevelViewPrefab, this._content);
			battleLevelView.ViewInit(i, battlePass, this._imagesBank, this._rarityBank);
			battleLevelView.OnItemClick += this.ShowItem;
			this._levelViews.Add(battleLevelView);
		}
	}

	// Token: 0x060001A9 RID: 425 RVA: 0x00009673 File Offset: 0x00007873
	private void ShowItem(int item)
	{
		if (!this._isRewardShowing)
		{
			this._drawer.ShowItem(item);
			this.ShowItemName(item);
			return;
		}
		this._itemNameText.text = "";
	}

	// Token: 0x060001AA RID: 426 RVA: 0x000096A1 File Offset: 0x000078A1
	private void ResetItemShowing()
	{
		this._isRewardShowing = false;
	}

	// Token: 0x060001AB RID: 427 RVA: 0x000096AC File Offset: 0x000078AC
	private void SetUpContentSize()
	{
		int count = this._levelViews.Count;
		float x = this._battleLevelViewPrefab.GetComponent<RectTransform>().sizeDelta.x;
		this._content.sizeDelta = new Vector2(x * (float)count, this._content.sizeDelta.y);
	}

	// Token: 0x060001AC RID: 428 RVA: 0x00009700 File Offset: 0x00007900
	private void SetUpInfo(BattlePassModel battlePass)
	{
		this._nameText.text = battlePass.Name;
		this._levelText.text = string.Format("{0} {1}", LocalizationController.Instance.GetText("BattlePass", "Level"), battlePass.Level);
		this._expText.text = string.Format("{0}/{1}", battlePass.Exp, battlePass.BattleLevels[battlePass.Level].ExpToNext);
		this._expBarImage.fillAmount = (float)battlePass.Exp / (float)battlePass.BattleLevels[battlePass.Level].ExpToNext;
		this._endsInText.text = string.Format("{0}: {1} {2}", LocalizationController.Instance.GetText("BattlePass", "EndsIn"), DateTimeOffset.FromUnixTimeSeconds((long)battlePass.EndDateInt).Date.Day, LocalizationController.Instance.GetText("BattlePass", "D"));
	}

	// Token: 0x060001AD RID: 429 RVA: 0x00009815 File Offset: 0x00007A15
	private void Localize()
	{
		this.SetUpInfo(this._battlePass);
	}

	// Token: 0x060001AE RID: 430 RVA: 0x00009824 File Offset: 0x00007A24
	private void ShowItemName(int item)
	{
		for (int i = 0; i < this._game.allWeapons.Length; i++)
		{
			if (this._game.allWeapons[i].id == item)
			{
				string text = LocalizationController.Instance.GetText("WeaponNames", this._game.allWeapons[i].weaponName);
				this._itemNameText.text = text;
				return;
			}
		}
		for (int j = 0; j < this._game.allAttachments.Length; j++)
		{
			if (this._game.allAttachments[j].ID == item)
			{
				string text2 = LocalizationController.Instance.GetText("Attachments", this._game.allAttachments[j].Name);
				this._itemNameText.text = text2;
				return;
			}
		}
	}

	// Token: 0x060001AF RID: 431 RVA: 0x000098EC File Offset: 0x00007AEC
	private void SetUpPrices()
	{
		StoreController.Instance.GetItemPrice(this._battlePass.SteamItemId, delegate(string str)
		{
			this._premiumCostText.text = str;
		});
		StoreController.Instance.GetItemPrice(this._oneLevelId, delegate(string str)
		{
			this._oneLevelCostText.text = str;
		});
		StoreController.Instance.GetItemPrice(this._fiveLevelId, delegate(string str)
		{
			this._fiveLevelsCostText.text = str;
		});
		StoreController.Instance.GetItemPrice(this._tenLevelId, delegate(string str)
		{
			this._tenLevelsCostText.text = str;
		});
	}

	// Token: 0x0400011B RID: 283
	[SerializeField]
	private BattleLevelView _battleLevelViewPrefab;

	// Token: 0x0400011C RID: 284
	[SerializeField]
	private ScrollRect _scrollRect;

	// Token: 0x0400011D RID: 285
	[SerializeField]
	private RectTransform _content;

	// Token: 0x0400011E RID: 286
	[SerializeField]
	private HorizontalLayoutGroup _horizontalLayoutGroup;

	// Token: 0x0400011F RID: 287
	[SerializeField]
	private TextMeshProUGUI _nameText;

	// Token: 0x04000120 RID: 288
	[SerializeField]
	private TextMeshProUGUI _levelText;

	// Token: 0x04000121 RID: 289
	[SerializeField]
	private TextMeshProUGUI _expText;

	// Token: 0x04000122 RID: 290
	[SerializeField]
	private TextMeshProUGUI _endsInText;

	// Token: 0x04000123 RID: 291
	[SerializeField]
	private Image _expBarImage;

	// Token: 0x04000124 RID: 292
	[SerializeField]
	private Button _backButton;

	// Token: 0x04000125 RID: 293
	[SerializeField]
	private Button _rewardButton;

	// Token: 0x04000126 RID: 294
	[SerializeField]
	private BattlePasssItemRenderer _drawer;

	// Token: 0x04000127 RID: 295
	[SerializeField]
	private HighlightMenuButtonView _battlePassHighlighter;

	// Token: 0x04000128 RID: 296
	[SerializeField]
	private TextMeshProUGUI _itemNameText;

	// Token: 0x04000129 RID: 297
	[SerializeField]
	private CameraMovement _battlePassCamera;

	// Token: 0x0400012A RID: 298
	[SerializeField]
	private CanvasGroup _renderCanvasGroup;

	// Token: 0x0400012B RID: 299
	[SerializeField]
	private GameObject _premiumBlock;

	// Token: 0x0400012C RID: 300
	[SerializeField]
	private GameObject _levelsBlock;

	// Token: 0x0400012D RID: 301
	[SerializeField]
	private Button _buyPremiumButton;

	// Token: 0x0400012E RID: 302
	[SerializeField]
	private Button _buyOneLevelButton;

	// Token: 0x0400012F RID: 303
	[SerializeField]
	private Button _buyFiveLevelsButton;

	// Token: 0x04000130 RID: 304
	[SerializeField]
	private Button _buyTenLevelsButton;

	// Token: 0x04000131 RID: 305
	[SerializeField]
	private Image _premiumDarkerImage;

	// Token: 0x04000132 RID: 306
	[SerializeField]
	private TextMeshProUGUI _premiumCostText;

	// Token: 0x04000133 RID: 307
	[SerializeField]
	private TextMeshProUGUI _oneLevelCostText;

	// Token: 0x04000134 RID: 308
	[SerializeField]
	private TextMeshProUGUI _fiveLevelsCostText;

	// Token: 0x04000135 RID: 309
	[SerializeField]
	private TextMeshProUGUI _tenLevelsCostText;

	// Token: 0x04000136 RID: 310
	[SerializeField]
	private TextAnimatedMessageView _levelMessageView;

	// Token: 0x04000137 RID: 311
	private List<BattleLevelView> _levelViews;

	// Token: 0x04000138 RID: 312
	private Kit_GameInformation _game;

	// Token: 0x04000139 RID: 313
	private ItemsCardImagesBank _imagesBank;

	// Token: 0x0400013A RID: 314
	private RarityContentBank _rarityBank;

	// Token: 0x0400013B RID: 315
	private BattlePassModel _battlePass;

	// Token: 0x0400013C RID: 316
	private bool _isRewardShowing;

	// Token: 0x0400013D RID: 317
	private int _oneLevelId = 9001;

	// Token: 0x0400013E RID: 318
	private int _fiveLevelId = 9002;

	// Token: 0x0400013F RID: 319
	private int _tenLevelId = 9003;

	// Token: 0x04000140 RID: 320
	private ObserveProcess _observeProcess;
}
﻿using System;
using Newtonsoft.Json;
using UnityEngine;

// Token: 0x02000047 RID: 71
public class BattlePassSetter : MonoBehaviour
{
	// Token: 0x060001BC RID: 444 RVA: 0x00009A31 File Offset: 0x00007C31
	private void Start()
	{
		this.SetUpBattlePass();
	}

	// Token: 0x060001BD RID: 445 RVA: 0x00009A3C File Offset: 0x00007C3C
	private void SetUpBattlePass()
	{
		BattlePassModel battlePass = Resources.Load<BattlePassSettings>("BattlePass").BattlePass;
		DateTime dateTime = DateTime.Parse(battlePass.EndDateString);
		DateTimeOffset dateTimeOffset = new DateTimeOffset(dateTime);
		battlePass.EndDateInt = (int)dateTimeOffset.ToUnixTimeSeconds();
		string jsonBattlePass = JsonConvert.SerializeObject(battlePass);
		DataSender.Instance.SetBattlePass(jsonBattlePass, delegate(string str)
		{
		});
	}
}
﻿using System;
using UnityEngine;

// Token: 0x02000048 RID: 72
[CreateAssetMenu(fileName = "BattlePass", menuName = "BattlePass")]
public class BattlePassSettings : ScriptableObject
{
	// Token: 0x17000011 RID: 17
	// (get) Token: 0x060001BF RID: 447 RVA: 0x00009AAA File Offset: 0x00007CAA
	public BattlePassModel BattlePass
	{
		get
		{
			return this._battlePass;
		}
	}

	// Token: 0x04000141 RID: 321
	[SerializeField]
	private BattlePassModel _battlePass;
}
﻿using System;
using System.Collections.Generic;
using DG.Tweening;
using MarsFPSKit;
using UnityEngine;

// Token: 0x02000049 RID: 73
public class BattlePasssItemRenderer : MonoBehaviour
{
	// Token: 0x14000003 RID: 3
	// (add) Token: 0x060001C1 RID: 449 RVA: 0x00009ABC File Offset: 0x00007CBC
	// (remove) Token: 0x060001C2 RID: 450 RVA: 0x00009AF4 File Offset: 0x00007CF4
	public event Action OnSequenceEnd = delegate()
	{
	};

	// Token: 0x060001C3 RID: 451 RVA: 0x00009B2C File Offset: 0x00007D2C
	private void OnEnable()
	{
		this._drageView.OnMosueDrag += this.RotateItem;
		this._showBox._EndSequenceEvent.AddListener(delegate()
		{
			this._isShowingRewards = false;
			this._showBox.gameObject.SetActive(false);
			this.OnSequenceEnd();
		});
		this._showBox._kickCardEvent.AddListener(delegate()
		{
			this._renderCamera.transform.DOShakeRotation(this._shakeDuration * this._mainDurationMultiplier, this._shakeStranght, this._shakeVibrato, 90f, true);
		});
	}

	// Token: 0x060001C4 RID: 452 RVA: 0x00009B88 File Offset: 0x00007D88
	private void OnDisable()
	{
		this._drageView.OnMosueDrag -= this.RotateItem;
		this._showBox._EndSequenceEvent.RemoveAllListeners();
		this._showBox._kickCardEvent.RemoveAllListeners();
	}

	// Token: 0x060001C5 RID: 453 RVA: 0x00009BC4 File Offset: 0x00007DC4
	public void ShowItem(int id)
	{
		if (this._isShowingRewards)
		{
			return;
		}
		if (this._item)
		{
			UnityEngine.Object.Destroy(this._item);
		}
		this._objectEmpty.rotation = Quaternion.Euler(this._originRotation);
		GameObject prefab = this.GetPrefab(id);
		this._item = UnityEngine.Object.Instantiate<GameObject>(prefab, this._objectEmpty);
	}

	// Token: 0x060001C6 RID: 454 RVA: 0x00009C24 File Offset: 0x00007E24
	public GameObject GetPrefab(int id)
	{
		GameObject result = null;
		for (int i = 0; i < this._itemsBank.Items.Count; i++)
		{
			if (this._itemsBank.Items[i].ID == id)
			{
				return this._itemsBank.Items[i].Item;
			}
		}
		return result;
	}

	// Token: 0x060001C7 RID: 455 RVA: 0x00009C7F File Offset: 0x00007E7F
	public void SetCamera(bool on)
	{
		this._renderCamera.SetActive(on);
	}

	// Token: 0x060001C8 RID: 456 RVA: 0x00009C8D File Offset: 0x00007E8D
	public void ShowRewards(List<int> rewards)
	{
		if (this._item)
		{
			UnityEngine.Object.Destroy(this._item);
		}
		this._showBox.gameObject.SetActive(true);
		this._showBox.SeItemList(rewards);
	}

	// Token: 0x060001C9 RID: 457 RVA: 0x00009CC4 File Offset: 0x00007EC4
	private void RotateItem(Vector2 rotation)
	{
		Vector2 v = new Vector2(rotation.y, -rotation.x) * this._rotationSenesetivity;
		this._objectEmpty.Rotate(v, Space.World);
	}

	// Token: 0x04000143 RID: 323
	[SerializeField]
	private Transform _objectEmpty;

	// Token: 0x04000144 RID: 324
	[SerializeField]
	private Kit_GameInformation _game;

	// Token: 0x04000145 RID: 325
	[SerializeField]
	private Items3DBank _itemsBank;

	// Token: 0x04000146 RID: 326
	[SerializeField]
	private DragView _drageView;

	// Token: 0x04000147 RID: 327
	[SerializeField]
	private GameObject _renderCamera;

	// Token: 0x04000148 RID: 328
	[SerializeField]
	private ItemBoxTween _showBox;

	// Token: 0x04000149 RID: 329
	[Range(0f, 1f)]
	[SerializeField]
	private float _rotationSenesetivity;

	// Token: 0x0400014A RID: 330
	[SerializeField]
	private float _shakeDuration;

	// Token: 0x0400014B RID: 331
	[SerializeField]
	private float _mainDurationMultiplier;

	// Token: 0x0400014C RID: 332
	[SerializeField]
	private float _shakeStranght;

	// Token: 0x0400014D RID: 333
	[SerializeField]
	private int _shakeVibrato;

	// Token: 0x0400014E RID: 334
	private Vector3 _originRotation = new Vector3(0f, 90f);

	// Token: 0x0400014F RID: 335
	private GameObject _item;

	// Token: 0x04000150 RID: 336
	private bool _isShowingRewards;
}
﻿using System;
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.UI;

// Token: 0x0200004A RID: 74
public class BattleRewardView : MonoBehaviour, IPointerClickHandler, IEventSystemHandler
{
	// Token: 0x14000004 RID: 4
	// (add) Token: 0x060001CD RID: 461 RVA: 0x00009DA8 File Offset: 0x00007FA8
	// (remove) Token: 0x060001CE RID: 462 RVA: 0x00009DE0 File Offset: 0x00007FE0
	public event Action<int> OnClick = delegate(int <p0>)
	{
	};

	// Token: 0x060001CF RID: 463 RVA: 0x00009E15 File Offset: 0x00008015
	public void ViewInit(Sprite backgroundSprite, Sprite itemSprite, Sprite raritySprite, bool isRewarded, int id)
	{
		this._backgroundImage.sprite = backgroundSprite;
		this._itemImage.sprite = itemSprite;
		this._rarityImage.sprite = raritySprite;
		this._isRewardedImage.enabled = isRewarded;
		this._id = id;
	}

	// Token: 0x060001D0 RID: 464 RVA: 0x00009E50 File Offset: 0x00008050
	public void OnPointerClick(PointerEventData eventData)
	{
		this.OnClick(this._id);
	}

	// Token: 0x04000152 RID: 338
	[SerializeField]
	private Image _backgroundImage;

	// Token: 0x04000153 RID: 339
	[SerializeField]
	private Image _itemImage;

	// Token: 0x04000154 RID: 340
	[SerializeField]
	private Image _isRewardedImage;

	// Token: 0x04000155 RID: 341
	[SerializeField]
	private Image _rarityImage;

	// Token: 0x04000156 RID: 342
	private int _id;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x0200009C RID: 156
[CreateAssetMenu(menuName = "MarsFPSKit/ItemIcon")]
public class BigItemIcon : ScriptableObject
{
	// Token: 0x040002D1 RID: 721
	public List<ItemIcon> itemIcons;
}
﻿using System;
using ExitGames.Client.Photon;
using MarsFPSKit;
using Photon.Pun;
using Photon.Realtime;
using UnityEngine;

// Token: 0x0200009D RID: 157
public class BombPlace : MonoBehaviour, IPunObservable
{
	// Token: 0x0600039B RID: 923 RVA: 0x000105AF File Offset: 0x0000E7AF
	public void ViewInit(BombView bombView, PvPGMBPlantingBomb plantingBombMode)
	{
		this._plantingBombMode = plantingBombMode;
		this._bombSetupTime = bombView.SetupTime;
		this._main = plantingBombMode.Main;
	}

	// Token: 0x0600039C RID: 924 RVA: 0x000105D0 File Offset: 0x0000E7D0
	public void CancelSetupBomb(Kit_PlayerBehaviour pb)
	{
		pb.OnBombStuped -= this.BombSetuped;
	}

	// Token: 0x0600039D RID: 925 RVA: 0x000105E4 File Offset: 0x0000E7E4
	private void BombSetuped()
	{
		if (this._plantingBombMode.IsEndingRound)
		{
			return;
		}
		if (this._targetPlayer)
		{
			this._targetPlayer.OnBombStuped -= this.BombSetuped;
		}
		PhotonNetwork.RaiseEvent(Kit_EventIDs.setupBombEvent, this._bombPlace, new RaiseEventOptions
		{
			Receivers = ReceiverGroup.All
		}, SendOptions.SendReliable);
		Kit_KillFeedManager kit_KillFeedManager = (Kit_KillFeedManager)this._main.killFeed;
		if (PhotonNetwork.PlayerList.Length > 1 || (this._main.currentBotManager != null && this._main.currentBotManager.bots != null && this._main.currentBotManager.bots.Count > 0))
		{
			kit_KillFeedManager.killFX.AddScore(300);
			this._main.gameInformation.statistics.OnInstalledBomb(this._main);
		}
	}

	// Token: 0x0600039E RID: 926 RVA: 0x000106D0 File Offset: 0x0000E8D0
	private Vector3 GetBombPlace(Transform player)
	{
		RaycastHit raycastHit;
		if (Physics.Raycast(new Ray(player.position, Vector3.down), out raycastHit, 1f))
		{
			return raycastHit.point;
		}
		return player.position;
	}

	// Token: 0x0600039F RID: 927 RVA: 0x00010709 File Offset: 0x0000E909
	private void Start()
	{
		this._main = UnityEngine.Object.FindObjectOfType<Kit_IngameMain>();
	}

	// Token: 0x060003A0 RID: 928 RVA: 0x00010718 File Offset: 0x0000E918
	private void Update()
	{
		if (this._main && this._main.currentGameModeBehaviour.AreEnoughPlayersThere(this._main))
		{
			if (Input.GetKeyDown(this._bombKey))
			{
				if (this._targetPlayer && this._targetPlayer.IsInBombPlace && this._targetPlayer.IsBomb)
				{
					this._targetPlayer.StartSetupBomb(this._bombSetupTime);
					this._targetPlayer.OnBombStuped += this.BombSetuped;
					this._bombPlace = this.GetBombPlace(this._targetPlayer.transform);
					return;
				}
			}
			else if (Input.GetKeyUp(this._bombKey) && this._targetPlayer && this._targetPlayer.IsInBombPlace && this._targetPlayer.IsBomb)
			{
				this._targetPlayer.CancelSetupBomb();
				this._targetPlayer.OnBombStuped -= this.BombSetuped;
			}
		}
	}

	// Token: 0x060003A1 RID: 929 RVA: 0x00010824 File Offset: 0x0000EA24
	private void OnTriggerEnter(Collider other)
	{
		Kit_PlayerBehaviour component = other.GetComponent<Kit_PlayerBehaviour>();
		if (component && !component.isBot && component.IsBomb)
		{
			component.IsInBombPlace = true;
			this._targetPlayer = component;
		}
	}

	// Token: 0x060003A2 RID: 930 RVA: 0x00010860 File Offset: 0x0000EA60
	private void OnTriggerExit(Collider other)
	{
		Kit_PlayerBehaviour component = other.GetComponent<Kit_PlayerBehaviour>();
		if (component)
		{
			component.IsInBombPlace = false;
			this._targetPlayer = null;
		}
	}

	// Token: 0x060003A3 RID: 931 RVA: 0x0001088A File Offset: 0x0000EA8A
	public void OnPhotonSerializeView(PhotonStream stream, PhotonMessageInfo info)
	{
		if (stream.IsWriting)
		{
			stream.SendNext(this._bombSetupTime);
			return;
		}
		this._bombSetupTime = (float)stream.ReceiveNext();
	}

	// Token: 0x040002D2 RID: 722
	[SerializeField]
	private Renderer _renderer;

	// Token: 0x040002D3 RID: 723
	[SerializeField]
	private KeyCode _bombKey = KeyCode.F;

	// Token: 0x040002D4 RID: 724
	private float _bombSetupTime = 5f;

	// Token: 0x040002D5 RID: 725
	private KeyCode _leftMouseKey = KeyCode.Mouse0;

	// Token: 0x040002D6 RID: 726
	private PvPGMBPlantingBomb _plantingBombMode;

	// Token: 0x040002D7 RID: 727
	private Vector3 _bombPlace;

	// Token: 0x040002D8 RID: 728
	private Kit_PlayerBehaviour _targetPlayer;

	// Token: 0x040002D9 RID: 729
	private Kit_IngameMain _main;
}
﻿using System;
using Photon.Pun;
using UnityEngine;

// Token: 0x0200009E RID: 158
public class BombSpawner : MonoBehaviourPunCallbacks
{
	// Token: 0x17000048 RID: 72
	// (get) Token: 0x060003A5 RID: 933 RVA: 0x000108DD File Offset: 0x0000EADD
	public Transform[] BombPlaces
	{
		get
		{
			return this._bombPlacePosition;
		}
	}

	// Token: 0x060003A6 RID: 934 RVA: 0x000108E8 File Offset: 0x0000EAE8
	public BombView SpawnBomb(PvPGMBPlantingBomb plantingBombMode)
	{
		if (!this._bomb)
		{
			this._bomb = UnityEngine.Object.Instantiate<GameObject>(this._bombPrefab, this._bombPoisition.position, this._bombPoisition.rotation).GetComponent<BombView>();
			this._bomb.ViewInit(plantingBombMode);
		}
		return this._bomb;
	}

	// Token: 0x060003A7 RID: 935 RVA: 0x00010940 File Offset: 0x0000EB40
	public BombPlace[] SpawnBombPlace(PvPGMBPlantingBomb plantingBombMode)
	{
		if (this._places == null)
		{
			for (int i = 0; i < this._bombPlacePosition.Length; i++)
			{
				this._bombPlacePosition[i].gameObject.SetActive(true);
			}
			this._places = new BombPlace[this._bombPlacePosition.Length];
			for (int j = 0; j < this._places.Length; j++)
			{
				this._places[j] = UnityEngine.Object.Instantiate<GameObject>(this._bombPlacePrefab, this._bombPlacePosition[j].position, this._bombPlacePosition[j].rotation).GetComponent<BombPlace>();
				this._places[j].ViewInit(this._bomb, plantingBombMode);
			}
		}
		return this._places;
	}

	// Token: 0x060003A8 RID: 936 RVA: 0x000109F0 File Offset: 0x0000EBF0
	public void ResetPosition()
	{
		BombView bomb = this._bomb;
		if (bomb == null)
		{
			return;
		}
		bomb.SetActive(this._bombPoisition);
	}

	// Token: 0x060003A9 RID: 937 RVA: 0x00010A08 File Offset: 0x0000EC08
	public void SetTeam(int team)
	{
		BombView bomb = this._bomb;
		if (bomb == null)
		{
			return;
		}
		bomb.SetTeam(team);
	}

	// Token: 0x060003AA RID: 938 RVA: 0x00010A1B File Offset: 0x0000EC1B
	public void SetupBomb(Vector3 place)
	{
		this._bomb.SetupBomb(place);
	}

	// Token: 0x040002DA RID: 730
	[SerializeField]
	private GameObject _bombPrefab;

	// Token: 0x040002DB RID: 731
	[SerializeField]
	private GameObject _bombPlacePrefab;

	// Token: 0x040002DC RID: 732
	[SerializeField]
	private Transform[] _bombPlacePosition;

	// Token: 0x040002DD RID: 733
	[SerializeField]
	private Transform _bombPoisition;

	// Token: 0x040002DE RID: 734
	private BombView _bomb;

	// Token: 0x040002DF RID: 735
	private BombPlace _bombPlace;

	// Token: 0x040002E0 RID: 736
	private BombPlace[] _places;
}
﻿using System;
using ExitGames.Client.Photon;
using MarsFPSKit;
using Photon.Pun;
using Photon.Realtime;
using UnityEngine;

// Token: 0x0200009F RID: 159
public class BombView : Kit_InteractableObject, IPunObservable
{
	// Token: 0x17000049 RID: 73
	// (get) Token: 0x060003AC RID: 940 RVA: 0x00010A31 File Offset: 0x0000EC31
	public int Team
	{
		get
		{
			return this._team;
		}
	}

	// Token: 0x1700004A RID: 74
	// (get) Token: 0x060003AD RID: 941 RVA: 0x00010A39 File Offset: 0x0000EC39
	// (set) Token: 0x060003AE RID: 942 RVA: 0x00010A41 File Offset: 0x0000EC41
	public bool IsBombSetup { get; set; }

	// Token: 0x1700004B RID: 75
	// (get) Token: 0x060003AF RID: 943 RVA: 0x00010A4A File Offset: 0x0000EC4A
	// (set) Token: 0x060003B0 RID: 944 RVA: 0x00010A52 File Offset: 0x0000EC52
	public bool IsBombDeactivated { get; set; }

	// Token: 0x1700004C RID: 76
	// (get) Token: 0x060003B1 RID: 945 RVA: 0x00010A5B File Offset: 0x0000EC5B
	public float SetupTime
	{
		get
		{
			return this._setupTime;
		}
	}

	// Token: 0x1700004D RID: 77
	// (get) Token: 0x060003B2 RID: 946 RVA: 0x00010A63 File Offset: 0x0000EC63
	public bool IsActive
	{
		get
		{
			return this._viewObject.enabled;
		}
	}

	// Token: 0x060003B3 RID: 947 RVA: 0x00010A70 File Offset: 0x0000EC70
	private void Update()
	{
		if (Input.GetKeyUp(KeyCode.F) && this._saper)
		{
			this._saper.CancelSetupBomb();
			this._saper.OnBombStuped -= this.BombDeactivated;
			this._saper = null;
		}
	}

	// Token: 0x060003B4 RID: 948 RVA: 0x00010ABC File Offset: 0x0000ECBC
	public void ViewInit(PvPGMBPlantingBomb plantingBombMode)
	{
		this._plantingBombMode = plantingBombMode;
	}

	// Token: 0x060003B5 RID: 949 RVA: 0x00010AC8 File Offset: 0x0000ECC8
	public void SetActive(Transform point)
	{
		base.transform.position = point.position;
		base.transform.rotation = point.rotation;
		this._viewObject.enabled = true;
		this._collider.enabled = true;
		this._rigidbody.position = point.position;
		this._rigidbody.isKinematic = false;
		base.transform.parent = null;
		this.IsBombSetup = false;
		this.IsBombDeactivated = false;
		this._lampObject.SetActive(true);
	}

	// Token: 0x060003B6 RID: 950 RVA: 0x00010B52 File Offset: 0x0000ED52
	public void SetPassive()
	{
		this._rigidbody.isKinematic = true;
		this._viewObject.enabled = false;
		this._collider.enabled = false;
		this._lampObject.SetActive(false);
	}

	// Token: 0x060003B7 RID: 951 RVA: 0x00010B84 File Offset: 0x0000ED84
	public void SetTeam(int team)
	{
		this._team = team;
	}

	// Token: 0x060003B8 RID: 952 RVA: 0x00010B8D File Offset: 0x0000ED8D
	public void GetBomb(Kit_PlayerBehaviour who)
	{
		this.SetPassive();
	}

	// Token: 0x060003B9 RID: 953 RVA: 0x00010B98 File Offset: 0x0000ED98
	public void DropBomb(Vector3 position)
	{
		base.transform.position = position;
		this._viewObject.enabled = true;
		this._collider.enabled = true;
		this._rigidbody.position = position;
		this._rigidbody.isKinematic = false;
		base.transform.parent = null;
		this._lampObject.SetActive(true);
	}

	// Token: 0x060003BA RID: 954 RVA: 0x00010BFC File Offset: 0x0000EDFC
	public void SetupBomb(Vector3 place)
	{
		base.transform.position = place;
		this._rigidbody.position = place;
		this._viewObject.enabled = true;
		this._rigidbody.isKinematic = true;
		this._collider.enabled = true;
		this.IsBombSetup = true;
		this._plantingBombMode.OnBombSetup();
		this._lampObject.SetActive(true);
	}

	// Token: 0x060003BB RID: 955 RVA: 0x00010C63 File Offset: 0x0000EE63
	public void CancelSetupBomb(Kit_PlayerBehaviour who)
	{
		who.OnBombStuped -= this.BombDeactivated;
	}

	// Token: 0x060003BC RID: 956 RVA: 0x00010C78 File Offset: 0x0000EE78
	public void BombExplosion()
	{
		if (this.IsBombSetup)
		{
			UnityEngine.Object.Instantiate<Kit_Explosion>(this._explosionParticles, base.transform.position, base.transform.rotation).Explode(true, false, -1, "Bomb");
			this._viewObject.enabled = false;
			this._lampObject.SetActive(false);
		}
	}

	// Token: 0x060003BD RID: 957 RVA: 0x00010CD3 File Offset: 0x0000EED3
	private void BombDeactivated()
	{
		PhotonNetwork.RaiseEvent(Kit_EventIDs.deactivateBombEvent, null, new RaiseEventOptions
		{
			Receivers = ReceiverGroup.All
		}, SendOptions.SendReliable);
	}

	// Token: 0x060003BE RID: 958 RVA: 0x00005F78 File Offset: 0x00004178
	private void OnDestroyNotification(Kit_PlayerBehaviour who)
	{
	}

	// Token: 0x060003BF RID: 959 RVA: 0x00010CF4 File Offset: 0x0000EEF4
	public override void Interact(Kit_PlayerBehaviour who)
	{
		if (who.main.currentGameModeBehaviour.AreEnoughPlayersThere(who.main))
		{
			if (who.myTeam == this._team && !this.IsBombSetup)
			{
				this._owner = who;
				this._owner.IsBomb = true;
				PhotonNetwork.RaiseEvent(Kit_EventIDs.getBombEvent, who.id, new RaiseEventOptions
				{
					Receivers = ReceiverGroup.All
				}, SendOptions.SendReliable);
				return;
			}
			if (who.myTeam != this._team && this.IsBombSetup && !this.IsBombDeactivated)
			{
				who.StartSetupBomb(this._setupTime);
				who.OnBombStuped += this.BombDeactivated;
				this._saper = who;
			}
		}
	}

	// Token: 0x060003C0 RID: 960 RVA: 0x00010DB0 File Offset: 0x0000EFB0
	public void OnPhotonSerializeView(PhotonStream stream, PhotonMessageInfo info)
	{
		if (stream.IsWriting)
		{
			stream.SendNext(this._team);
			stream.SendNext(this._viewObject.enabled);
			stream.SendNext(this._rigidbody.isKinematic);
			stream.SendNext(this._collider.enabled);
			stream.SendNext(this.IsBombSetup);
			stream.SendNext(this.IsBombDeactivated);
			stream.SendNext(this._lampObject.activeInHierarchy);
			return;
		}
		this._team = (int)stream.ReceiveNext();
		this._viewObject.enabled = (bool)stream.ReceiveNext();
		this._rigidbody.isKinematic = (bool)stream.ReceiveNext();
		this._collider.enabled = (bool)stream.ReceiveNext();
		this.IsBombSetup = (bool)stream.ReceiveNext();
		this.IsBombDeactivated = (bool)stream.ReceiveNext();
		this._lampObject.SetActive((bool)stream.ReceiveNext());
	}

	// Token: 0x040002E1 RID: 737
	[SerializeField]
	private Rigidbody _rigidbody;

	// Token: 0x040002E2 RID: 738
	[SerializeField]
	private Renderer _viewObject;

	// Token: 0x040002E3 RID: 739
	[SerializeField]
	private Collider _collider;

	// Token: 0x040002E4 RID: 740
	[SerializeField]
	private float _setupTime = 5f;

	// Token: 0x040002E5 RID: 741
	[SerializeField]
	private Kit_Explosion _explosionParticles;

	// Token: 0x040002E6 RID: 742
	[SerializeField]
	private GameObject _lampObject;

	// Token: 0x040002E7 RID: 743
	private int _team;

	// Token: 0x040002E8 RID: 744
	private Kit_PlayerBehaviour _owner;

	// Token: 0x040002E9 RID: 745
	private PvPGMBPlantingBomb _plantingBombMode;

	// Token: 0x040002EA RID: 746
	private Kit_PlayerBehaviour _saper;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using UnityEngine;

// Token: 0x0200007E RID: 126
public class BoundsWeaponValues
{
	// Token: 0x17000034 RID: 52
	// (get) Token: 0x0600033C RID: 828 RVA: 0x0000FC1E File Offset: 0x0000DE1E
	public static BoundsWeaponValues Instance
	{
		get
		{
			if (BoundsWeaponValues._instance == null)
			{
				BoundsWeaponValues._instance = new BoundsWeaponValues();
			}
			return BoundsWeaponValues._instance;
		}
	}

	// Token: 0x17000035 RID: 53
	// (get) Token: 0x0600033D RID: 829 RVA: 0x0000FC36 File Offset: 0x0000DE36
	// (set) Token: 0x0600033E RID: 830 RVA: 0x0000FC3E File Offset: 0x0000DE3E
	public Dictionary<WeaponFeatureType, Vector2> FeatureBoundValues { get; private set; }

	// Token: 0x0600033F RID: 831 RVA: 0x0000FC47 File Offset: 0x0000DE47
	private BoundsWeaponValues()
	{
		this._game = Resources.Load<Kit_GameInformation>("Game");
		this.SetBoundsData();
	}

	// Token: 0x06000340 RID: 832 RVA: 0x0000FC68 File Offset: 0x0000DE68
	private void SetBoundsData()
	{
		this.FeatureBoundValues = new Dictionary<WeaponFeatureType, Vector2>();
		Kit_WeaponBase[] allWeapons = this._game.allWeapons;
		for (int i = 0; i < allWeapons.Length; i++)
		{
			if (allWeapons[i] is Kit_ModernWeaponScript)
			{
				Kit_ModernWeaponScript kit_ModernWeaponScript = (Kit_ModernWeaponScript)allWeapons[i];
				this.CheckValue(kit_ModernWeaponScript.baseDamage, ref this._damage);
				this.CheckValue(kit_ModernWeaponScript.range, ref this._range);
				this.CheckValue((float)kit_ModernWeaponScript.bulletsPerMag, ref this._bulletsPerMag);
				this.CheckValue(kit_ModernWeaponScript.bulletSpreadAimBase, ref this._accuracy);
				this.CheckValue((kit_ModernWeaponScript.recoilPerShotMax - kit_ModernWeaponScript.recoilPerShotMin).magnitude, ref this._recoil);
				this.CheckValue((float)kit_ModernWeaponScript.RPM, ref this._rpm);
			}
			else if (allWeapons[i] is Kit_ModernMeleeScript)
			{
				Kit_ModernMeleeScript kit_ModernMeleeScript = (Kit_ModernMeleeScript)allWeapons[i];
				this.CheckValue(kit_ModernMeleeScript.primaryAttackSettings.stabDamage, ref this._damage);
				this.CheckValue(kit_ModernMeleeScript.primaryAttackSettings.stabReach, ref this._range);
			}
		}
		this.FeatureBoundValues.Add(WeaponFeatureType.Accuracy, new Vector2(0.05f, this._accuracy.x));
		this.FeatureBoundValues.Add(WeaponFeatureType.BulletsPerMag, this._bulletsPerMag);
		this.FeatureBoundValues.Add(WeaponFeatureType.Damage, this._damage);
		this.FeatureBoundValues.Add(WeaponFeatureType.Range, this._range);
		this.FeatureBoundValues.Add(WeaponFeatureType.Recoil, new Vector2(5f, this._recoil.x));
		this.FeatureBoundValues.Add(WeaponFeatureType.RPM, this._rpm);
	}

	// Token: 0x06000341 RID: 833 RVA: 0x0000FE0B File Offset: 0x0000E00B
	private void CheckValue(float val, ref Vector2 feature)
	{
		if (val < feature.x)
		{
			feature = new Vector2(0f, feature.y);
			return;
		}
		if (val > feature.y)
		{
			feature = new Vector2(feature.x, val);
		}
	}

	// Token: 0x0400025D RID: 605
	private Vector2 _damage;

	// Token: 0x0400025E RID: 606
	private Vector2 _range;

	// Token: 0x0400025F RID: 607
	private Vector2 _accuracy;

	// Token: 0x04000260 RID: 608
	private Vector2 _bulletsPerMag;

	// Token: 0x04000261 RID: 609
	private Vector2 _recoil;

	// Token: 0x04000262 RID: 610
	private Vector2 _rpm;

	// Token: 0x04000263 RID: 611
	private static BoundsWeaponValues _instance;

	// Token: 0x04000264 RID: 612
	private Kit_GameInformation _game;
}
﻿using System;
using System.Collections.Generic;
using Photon.Pun;
using UnityEngine;

// Token: 0x02000002 RID: 2
public class Buoyancy : MonoBehaviour
{
	// Token: 0x06000001 RID: 1 RVA: 0x00002050 File Offset: 0x00000250
	private void Start()
	{
		this.pv = base.GetComponent<PhotonView>();
		this.forces = new List<Vector3[]>();
		Quaternion rotation = base.transform.rotation;
		Vector3 position = base.transform.position;
		base.transform.rotation = Quaternion.identity;
		base.transform.position = Vector3.zero;
		if (base.GetComponent<Collider>() == null)
		{
			base.gameObject.AddComponent<MeshCollider>();
			Debug.LogWarning(string.Format("[Buoyancy.cs] Object \"{0}\" had no collider. MeshCollider has been added.", base.name));
		}
		this.isMeshCollider = (base.GetComponent<MeshCollider>() != null);
		Bounds bounds = base.GetComponent<Collider>().bounds;
		if (bounds.size.x < bounds.size.y)
		{
			this.voxelHalfHeight = bounds.size.x;
		}
		else
		{
			this.voxelHalfHeight = bounds.size.y;
		}
		if (bounds.size.z < this.voxelHalfHeight)
		{
			this.voxelHalfHeight = bounds.size.z;
		}
		this.voxelHalfHeight /= (float)(2 * this.slicesPerAxis);
		if (base.GetComponent<Rigidbody>() == null)
		{
			base.gameObject.AddComponent<Rigidbody>();
			Debug.LogWarning(string.Format("[Buoyancy.cs] Object \"{0}\" had no Rigidbody. Rigidbody has been added.", base.name));
		}
		base.GetComponent<Rigidbody>().centerOfMass = new Vector3(0f, -bounds.extents.y * 0f, 0f) + base.transform.InverseTransformPoint(bounds.center);
		this.voxels = this.SliceIntoVoxels(this.isMeshCollider && this.isConcave);
		base.transform.rotation = rotation;
		base.transform.position = position;
		float num = base.GetComponent<Rigidbody>().mass / this.density;
		Buoyancy.WeldPoints(this.voxels, this.voxelsLimit);
		float y = 1000f * Mathf.Abs(Physics.gravity.y) * num;
		this.localArchimedesForce = new Vector3(0f, y, 0f) / (float)this.voxels.Count;
	}

	// Token: 0x06000002 RID: 2 RVA: 0x00002284 File Offset: 0x00000484
	private List<Vector3> SliceIntoVoxels(bool concave)
	{
		List<Vector3> list = new List<Vector3>(this.slicesPerAxis * this.slicesPerAxis * this.slicesPerAxis);
		if (concave)
		{
			MeshCollider component = base.GetComponent<MeshCollider>();
			bool convex = component.convex;
			component.convex = false;
			Bounds bounds = base.GetComponent<Collider>().bounds;
			for (int i = 0; i < this.slicesPerAxis; i++)
			{
				for (int j = 0; j < this.slicesPerAxis; j++)
				{
					for (int k = 0; k < this.slicesPerAxis; k++)
					{
						float x = bounds.min.x + bounds.size.x / (float)this.slicesPerAxis * (0.5f + (float)i);
						float y = bounds.min.y + bounds.size.y / (float)this.slicesPerAxis * (0.5f + (float)j);
						float z = bounds.min.z + bounds.size.z / (float)this.slicesPerAxis * (0.5f + (float)k);
						Vector3 vector = base.transform.InverseTransformPoint(new Vector3(x, y, z));
						if (Buoyancy.PointIsInsideMeshCollider(component, vector))
						{
							list.Add(vector);
						}
					}
				}
			}
			if (list.Count == 0)
			{
				list.Add(bounds.center);
			}
			component.convex = convex;
		}
		else
		{
			Bounds bounds2 = base.GetComponent<Collider>().bounds;
			for (int l = 0; l < this.slicesPerAxis; l++)
			{
				for (int m = 0; m < this.slicesPerAxis; m++)
				{
					for (int n = 0; n < this.slicesPerAxis; n++)
					{
						float x2 = bounds2.min.x + bounds2.size.x / (float)this.slicesPerAxis * (0.5f + (float)l);
						float y2 = bounds2.min.y + bounds2.size.y / (float)this.slicesPerAxis * (0.5f + (float)m);
						float z2 = bounds2.min.z + bounds2.size.z / (float)this.slicesPerAxis * (0.5f + (float)n);
						Vector3 item = base.transform.InverseTransformPoint(new Vector3(x2, y2, z2));
						list.Add(item);
					}
				}
			}
		}
		return list;
	}

	// Token: 0x06000003 RID: 3 RVA: 0x000024FC File Offset: 0x000006FC
	private static bool PointIsInsideMeshCollider(Collider c, Vector3 p)
	{
		foreach (Vector3 vector in new Vector3[]
		{
			Vector3.up,
			Vector3.down,
			Vector3.left,
			Vector3.right,
			Vector3.forward,
			Vector3.back
		})
		{
			RaycastHit raycastHit;
			if (!c.Raycast(new Ray(p - vector * 1000f, vector), out raycastHit, 1000f))
			{
				return false;
			}
		}
		return true;
	}

	// Token: 0x06000004 RID: 4 RVA: 0x00002598 File Offset: 0x00000798
	private static void FindClosestPoints(IList<Vector3> list, out int firstIndex, out int secondIndex)
	{
		float num = float.MaxValue;
		float num2 = float.MinValue;
		firstIndex = 0;
		secondIndex = 1;
		for (int i = 0; i < list.Count - 1; i++)
		{
			for (int j = i + 1; j < list.Count; j++)
			{
				float num3 = Vector3.Distance(list[i], list[j]);
				if (num3 < num)
				{
					num = num3;
					firstIndex = i;
					secondIndex = j;
				}
				if (num3 > num2)
				{
					num2 = num3;
				}
			}
		}
	}

	// Token: 0x06000005 RID: 5 RVA: 0x00002608 File Offset: 0x00000808
	private static void WeldPoints(IList<Vector3> list, int targetCount)
	{
		if (list.Count <= 2 || targetCount < 2)
		{
			return;
		}
		while (list.Count > targetCount)
		{
			int index;
			int index2;
			Buoyancy.FindClosestPoints(list, out index, out index2);
			Vector3 item = (list[index] + list[index2]) * 0.5f;
			list.RemoveAt(index2);
			list.RemoveAt(index);
			list.Add(item);
		}
	}

	// Token: 0x06000006 RID: 6 RVA: 0x00002669 File Offset: 0x00000869
	private float GetWaterLevel(float x, float z)
	{
		return this.waterHeight;
	}

	// Token: 0x06000007 RID: 7 RVA: 0x00002671 File Offset: 0x00000871
	public void SetWaterHeight(float height)
	{
		this.waterHeight = height;
	}

	// Token: 0x06000008 RID: 8 RVA: 0x0000267C File Offset: 0x0000087C
	private void FixedUpdate()
	{
		if (this.pv && !this.pv.IsMine)
		{
			return;
		}
		this.forces.Clear();
		foreach (Vector3 position in this.voxels)
		{
			Vector3 vector = base.transform.TransformPoint(position);
			float waterLevel = this.GetWaterLevel(vector.x, vector.z);
			if (vector.y - this.voxelHalfHeight < waterLevel)
			{
				float num = (waterLevel - vector.y) / (2f * this.voxelHalfHeight) + 0.5f;
				if (num > 1f)
				{
					num = 1f;
				}
				else if (num < 0f)
				{
					num = 0f;
				}
				Vector3 vector2 = -base.GetComponent<Rigidbody>().GetPointVelocity(vector) * 0.1f * base.GetComponent<Rigidbody>().mass + Mathf.Sqrt(num) * this.localArchimedesForce;
				base.GetComponent<Rigidbody>().AddForceAtPosition(vector2, vector);
				this.forces.Add(new Vector3[]
				{
					vector,
					vector2
				});
			}
		}
	}

	// Token: 0x06000009 RID: 9 RVA: 0x000027E8 File Offset: 0x000009E8
	private void OnDrawGizmos()
	{
		if (this.voxels == null || this.forces == null)
		{
			return;
		}
		Gizmos.color = Color.yellow;
		foreach (Vector3 position in this.voxels)
		{
			Gizmos.DrawCube(base.transform.TransformPoint(position), new Vector3(0.05f, 0.05f, 0.05f));
		}
		Gizmos.color = Color.cyan;
		foreach (Vector3[] array in this.forces)
		{
			Gizmos.DrawCube(array[0], new Vector3(0.05f, 0.05f, 0.05f));
			Gizmos.DrawLine(array[0], array[0] + array[1] / base.GetComponent<Rigidbody>().mass);
		}
	}

	// Token: 0x04000001 RID: 1
	public float density = 500f;

	// Token: 0x04000002 RID: 2
	public int slicesPerAxis = 2;

	// Token: 0x04000003 RID: 3
	public bool isConcave;

	// Token: 0x04000004 RID: 4
	public int voxelsLimit = 16;

	// Token: 0x04000005 RID: 5
	private const float DAMPFER = 0.1f;

	// Token: 0x04000006 RID: 6
	private const float WATER_DENSITY = 1000f;

	// Token: 0x04000007 RID: 7
	private float voxelHalfHeight;

	// Token: 0x04000008 RID: 8
	private Vector3 localArchimedesForce;

	// Token: 0x04000009 RID: 9
	private List<Vector3> voxels;

	// Token: 0x0400000A RID: 10
	private bool isMeshCollider;

	// Token: 0x0400000B RID: 11
	private List<Vector3[]> forces;

	// Token: 0x0400000C RID: 12
	private PhotonView pv;

	// Token: 0x0400000D RID: 13
	private float waterHeight;
}
﻿using System;
using UnityEngine;
using UnityEngine.EventSystems;

// Token: 0x02000106 RID: 262
public class ButtonInteractionView : MonoBehaviour, IPointerEnterHandler, IEventSystemHandler, IPointerClickHandler
{
	// Token: 0x14000011 RID: 17
	// (add) Token: 0x060006AB RID: 1707 RVA: 0x0001F7E0 File Offset: 0x0001D9E0
	// (remove) Token: 0x060006AC RID: 1708 RVA: 0x0001F818 File Offset: 0x0001DA18
	public event Action OnEnter = delegate()
	{
	};

	// Token: 0x14000012 RID: 18
	// (add) Token: 0x060006AD RID: 1709 RVA: 0x0001F850 File Offset: 0x0001DA50
	// (remove) Token: 0x060006AE RID: 1710 RVA: 0x0001F888 File Offset: 0x0001DA88
	public event Action OnClick = delegate()
	{
	};

	// Token: 0x060006AF RID: 1711 RVA: 0x0001F8BD File Offset: 0x0001DABD
	public void OnPointerEnter(PointerEventData eventData)
	{
		this.OnEnter();
	}

	// Token: 0x060006B0 RID: 1712 RVA: 0x0001F8CA File Offset: 0x0001DACA
	public void OnPointerClick(PointerEventData eventData)
	{
		this.OnClick();
	}
}
﻿using System;
using System.Collections.Generic;
using DG.Tweening;
using DG.Tweening.Core;
using DG.Tweening.Plugins.Core.PathCore;
using DG.Tweening.Plugins.Options;
using UnityEngine;
using UnityEngine.Events;

// Token: 0x020000C5 RID: 197
public class CameraFolowTween : MonoBehaviour
{
	// Token: 0x060004C4 RID: 1220 RVA: 0x000163C8 File Offset: 0x000145C8
	private void Awake()
	{
		this._toBoxPathPosition = new Vector3[this._toBoxPath.Length];
		this._toStartPathPositionInAngar = new Vector3[this._toStartPathInAngar.Length];
		this._toStartPathPositionOutAngar = new Vector3[this._toStartPathOutAngar.Length];
		for (int i = 0; i < this._toBoxPathPosition.Length; i++)
		{
			this._toBoxPathPosition[i] = this._toBoxPath[i].position;
		}
		for (int j = 0; j < this._toStartPathPositionInAngar.Length; j++)
		{
			this._toStartPathPositionInAngar[j] = this._toStartPathInAngar[j].position;
		}
		for (int k = 0; k < this._toStartPathPositionOutAngar.Length; k++)
		{
			this._toStartPathPositionOutAngar[k] = this._toStartPathOutAngar[k].position;
		}
	}

	// Token: 0x060004C5 RID: 1221 RVA: 0x00016492 File Offset: 0x00014692
	private void OnEnable()
	{
		this._boxTween._kickCardEvent.AddListener(new UnityAction(this.ShakeCamera));
		this._boxTween._EndSequenceEvent.AddListener(new UnityAction(this.FollowToStart));
	}

	// Token: 0x060004C6 RID: 1222 RVA: 0x000164CC File Offset: 0x000146CC
	private void OnDisable()
	{
		this._boxTween._kickCardEvent.RemoveListener(new UnityAction(this.ShakeCamera));
		this._boxTween._EndSequenceEvent.RemoveListener(new UnityAction(this.FollowToStart));
	}

	// Token: 0x060004C7 RID: 1223 RVA: 0x00016506 File Offset: 0x00014706
	public void ShakeCamera()
	{
		this._camera.DOShakeRotation(this._shakeDuration * this._mainDurationMultiplier, this._shakeStranght, this._shakeVibrato, 90f, true);
	}

	// Token: 0x060004C8 RID: 1224 RVA: 0x00016534 File Offset: 0x00014734
	public void FollowToBox(List<int> openItems)
	{
		GameObject[] hideObjects = this._hideObjects;
		for (int i = 0; i < hideObjects.Length; i++)
		{
			hideObjects[i].SetActive(false);
		}
		this._toBoxSequence = DOTween.Sequence();
		this._toBoxSequence.Append(this._camera.DOPath(this._toBoxPathPosition, this._followToAngarDuration * this._mainDurationMultiplier, PathType.CatmullRom, PathMode.Full3D, 10, new Color?(Color.red)).SetLookAt(this._inAngarLookAt, true).SetEase(Ease.Linear)).Append(this._camera.DOLookAt(this._showCardLookAt.position, this._lookShowCardDuration, AxisConstraint.None, null));
		this._toBoxSequence.OnComplete(delegate
		{
			this._toBoxSequence.Kill(false);
			this._boxTween.SeItemList(openItems);
		});
	}

	// Token: 0x060004C9 RID: 1225 RVA: 0x0001660C File Offset: 0x0001480C
	private void FollowToStart()
	{
		this._toStartSequence = DOTween.Sequence();
		this._toStartSequence.Append(this._camera.DOLookAt(this._toStartPathPositionOutAngar[0], this._lookInAngarDuration * this._mainDurationMultiplier, AxisConstraint.None, null)).Append(this._camera.DOPath(this._toStartPathPositionInAngar, this._followToStartInAngarDuration * this._mainDurationMultiplier, PathType.CatmullRom, PathMode.Full3D, 10, new Color?(Color.blue)).SetLookAt(this._toStartPathPositionOutAngar[0], true).SetEase(Ease.Linear));
		this._toStartSequence.OnComplete(delegate
		{
			this._toStartSequence.Kill(false);
			this._toStartSequence = DOTween.Sequence();
			this._toStartSequence.Append(this._camera.DOLookAt(this._standartCameraLookAt.position, this._lookOutAngarDuration * this._mainDurationMultiplier, AxisConstraint.None, null)).Append(this._camera.DOPath(this._toStartPathPositionOutAngar, this._followToStartOutAngarDuration * this._mainDurationMultiplier, PathType.CatmullRom, PathMode.Full3D, 10, new Color?(Color.green)).SetLookAt(this._standartCameraLookAt, true).SetEase(Ease.InOutSine));
			this._toStartSequence.OnComplete(delegate
			{
				this._toStartSequence.Kill(false);
				GameObject[] hideObjects = this._hideObjects;
				for (int i = 0; i < hideObjects.Length; i++)
				{
					hideObjects[i].SetActive(true);
				}
			});
		});
	}

	// Token: 0x040003B4 RID: 948
	[SerializeField]
	private ItemBoxTween _boxTween;

	// Token: 0x040003B5 RID: 949
	[SerializeField]
	private Transform _camera;

	// Token: 0x040003B6 RID: 950
	[SerializeField]
	private Transform _standartCameraLookAt;

	// Token: 0x040003B7 RID: 951
	[SerializeField]
	private Transform _inAngarLookAt;

	// Token: 0x040003B8 RID: 952
	[SerializeField]
	private Transform _showCardLookAt;

	// Token: 0x040003B9 RID: 953
	[SerializeField]
	private GameObject[] _hideObjects;

	// Token: 0x040003BA RID: 954
	[Header("Durations")]
	[SerializeField]
	private float _mainDurationMultiplier;

	// Token: 0x040003BB RID: 955
	[SerializeField]
	private float _followToAngarDuration;

	// Token: 0x040003BC RID: 956
	[SerializeField]
	private float _followToStartInAngarDuration;

	// Token: 0x040003BD RID: 957
	[SerializeField]
	private float _followToStartOutAngarDuration;

	// Token: 0x040003BE RID: 958
	[SerializeField]
	private float _lookInAngarDuration;

	// Token: 0x040003BF RID: 959
	[SerializeField]
	private float _lookShowCardDuration;

	// Token: 0x040003C0 RID: 960
	[SerializeField]
	private float _lookOutAngarDuration;

	// Token: 0x040003C1 RID: 961
	[SerializeField]
	private float _shakeDuration;

	// Token: 0x040003C2 RID: 962
	[SerializeField]
	private float _shakeStranght;

	// Token: 0x040003C3 RID: 963
	[SerializeField]
	private int _shakeVibrato;

	// Token: 0x040003C4 RID: 964
	[Header("Path point")]
	[SerializeField]
	private Transform[] _toBoxPath = new Transform[6];

	// Token: 0x040003C5 RID: 965
	private Vector3[] _toBoxPathPosition;

	// Token: 0x040003C6 RID: 966
	[SerializeField]
	private Transform[] _toStartPathInAngar = new Transform[6];

	// Token: 0x040003C7 RID: 967
	private Vector3[] _toStartPathPositionInAngar;

	// Token: 0x040003C8 RID: 968
	[SerializeField]
	private Transform[] _toStartPathOutAngar = new Transform[6];

	// Token: 0x040003C9 RID: 969
	private Vector3[] _toStartPathPositionOutAngar;

	// Token: 0x040003CA RID: 970
	private Sequence _toBoxSequence;

	// Token: 0x040003CB RID: 971
	private Sequence _toStartSequence;
}
﻿using System;
using UnityEngine;

// Token: 0x0200004B RID: 75
public class CameraMovement : MonoBehaviour
{
	// Token: 0x04000157 RID: 343
	public Transform[] Path;

	// Token: 0x04000158 RID: 344
	public Transform Camera;

	// Token: 0x04000159 RID: 345
	public float Time;
}
﻿using System;
using System.Collections.Generic;
using System.Linq;
using UnityEngine;

// Token: 0x0200004F RID: 79
public class CameraMovementMenuSwitcher : BaseMenuSwitcher
{
	// Token: 0x060001E2 RID: 482 RVA: 0x0000A184 File Offset: 0x00008384
	public CameraMovementMenuSwitcher(CanvasGroup mainCanvas, CanvasGroup secondCanvas, CameraMovement cameraMovement, Action onShowed, Action onHided)
	{
		this._mainCanvas = mainCanvas;
		this._secondCanvas = secondCanvas;
		this._pathForward = cameraMovement.Path;
		this._pathBackward = this._pathForward.Reverse<Transform>().ToArray<Transform>();
		this._camera = cameraMovement.Camera;
		this._moveTime = cameraMovement.Time;
		this._appiarTime = 0.25f;
		this.OnShowed = onShowed;
		this.OnHided = onHided;
		this.SetUpForwardSequence();
		this.SetUpBackwardSequence();
		this._observeProcess = new ObserveProcess();
		this._lerpProcess = new LerpProcess();
	}

	// Token: 0x060001E3 RID: 483 RVA: 0x0000A268 File Offset: 0x00008468
	public override void HideMenu(bool isAnimated)
	{
		if (isAnimated)
		{
			this._time = 0f;
			this._step = 0;
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._mainCanvas.alpha = Mathf.Lerp(1f, 0f, t);
			}, this._appiarTime);
			this._observeProcess.StartProcess(delegate
			{
				if (this._step < this._backwardSequence.Count)
				{
					this._backwardSequence[this._step]();
					return;
				}
				this._mainCanvas.interactable = false;
				this._mainCanvas.blocksRaycasts = false;
				this._secondCanvas.interactable = false;
				this._secondCanvas.blocksRaycasts = false;
				this.OnHided();
				this._observeProcess.EndProcess();
			});
			return;
		}
		this._mainCanvas.alpha = 0f;
		this._camera.position = this._pathForward[0].position;
		this._camera.rotation = this._pathForward[0].rotation;
		this._mainCanvas.interactable = false;
		this._mainCanvas.blocksRaycasts = false;
	}

	// Token: 0x060001E4 RID: 484 RVA: 0x0000A318 File Offset: 0x00008518
	public override void ShowMenu(bool isAnimated)
	{
		if (isAnimated)
		{
			this._time = 0f;
			this._step = 0;
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._secondCanvas.alpha = Mathf.Lerp(0f, 1f, t);
			}, this._appiarTime);
			this._observeProcess.StartProcess(delegate
			{
				if (this._step < this._forwardSequence.Count)
				{
					this._forwardSequence[this._step]();
					return;
				}
				this._mainCanvas.interactable = true;
				this._mainCanvas.blocksRaycasts = true;
				this._secondCanvas.interactable = true;
				this._secondCanvas.blocksRaycasts = true;
				this.OnShowed();
				this._observeProcess.EndProcess();
			});
			return;
		}
		this._mainCanvas.alpha = 1f;
		this._camera.position = this._pathForward[this._pathForward.Length - 1].position;
		this._camera.rotation = this._pathForward[this._pathForward.Length - 1].rotation;
		this._mainCanvas.interactable = true;
		this._mainCanvas.blocksRaycasts = true;
	}

	// Token: 0x060001E5 RID: 485 RVA: 0x0000A3DC File Offset: 0x000085DC
	private void SetUpForwardSequence()
	{
		this._forwardSequence = new List<Action>();
		this._timePerStep = this._moveTime / (float)(this._pathForward.Length - 1);
		for (int i = 0; i < this._pathForward.Length - 1; i++)
		{
			int id = i;
			this._forwardSequence.Add(delegate
			{
				if (this._time < this._timePerStep * (float)(id + 1))
				{
					this._camera.position = Vector3.Lerp(this._pathForward[id].position, this._pathForward[id + 1].position, (this._time - this._timePerStep * (float)id) / this._timePerStep);
					this._camera.rotation = Quaternion.Lerp(this._pathForward[id].rotation, this._pathForward[id + 1].rotation, (this._time - this._timePerStep * (float)id) / this._timePerStep);
				}
				else
				{
					this._step++;
				}
				this._time += Time.deltaTime;
			});
		}
		this._forwardSequence.Add(delegate
		{
			float num = (this._time - this._timePerStep * (float)(this._pathForward.Length - 1)) / this._appiarTime;
			if (num <= 1f)
			{
				this._mainCanvas.alpha = Mathf.Lerp(0f, 1f, num);
			}
			else
			{
				this._mainCanvas.alpha = 1f;
				this._step++;
			}
			this._time += Time.deltaTime;
		});
	}

	// Token: 0x060001E6 RID: 486 RVA: 0x0000A464 File Offset: 0x00008664
	private void SetUpBackwardSequence()
	{
		this._backwardSequence = new List<Action>();
		this._timePerStep = this._moveTime / (float)(this._pathBackward.Length - 1);
		for (int i = 0; i < this._pathBackward.Length - 1; i++)
		{
			int id = i;
			this._backwardSequence.Add(delegate
			{
				if (this._time < this._timePerStep * (float)(id + 1))
				{
					this._camera.position = Vector3.Lerp(this._pathBackward[id].position, this._pathBackward[id + 1].position, (this._time - this._timePerStep * (float)id) / this._timePerStep);
					this._camera.rotation = Quaternion.Lerp(this._pathBackward[id].rotation, this._pathBackward[id + 1].rotation, (this._time - this._timePerStep * (float)id) / this._timePerStep);
				}
				else
				{
					this._step++;
				}
				this._time += Time.deltaTime;
			});
		}
		this._backwardSequence.Add(delegate
		{
			float num = (this._time - this._timePerStep * (float)(this._pathBackward.Length - 1)) / this._appiarTime;
			if (num <= 1f)
			{
				this._secondCanvas.alpha = Mathf.Lerp(1f, 0f, num);
			}
			else
			{
				this._secondCanvas.alpha = 0f;
				this._step++;
			}
			this._time += Time.deltaTime;
		});
	}

	// Token: 0x04000163 RID: 355
	private CanvasGroup _mainCanvas;

	// Token: 0x04000164 RID: 356
	private CanvasGroup _secondCanvas;

	// Token: 0x04000165 RID: 357
	private Transform[] _pathForward;

	// Token: 0x04000166 RID: 358
	private Transform[] _pathBackward;

	// Token: 0x04000167 RID: 359
	private Transform _camera;

	// Token: 0x04000168 RID: 360
	private float _moveTime;

	// Token: 0x04000169 RID: 361
	private float _appiarTime;

	// Token: 0x0400016A RID: 362
	private float _timePerStep;

	// Token: 0x0400016B RID: 363
	private float _time;

	// Token: 0x0400016C RID: 364
	private int _step;

	// Token: 0x0400016D RID: 365
	private List<Action> _forwardSequence;

	// Token: 0x0400016E RID: 366
	private List<Action> _backwardSequence;

	// Token: 0x0400016F RID: 367
	private ObserveProcess _observeProcess;

	// Token: 0x04000170 RID: 368
	private LerpProcess _lerpProcess;

	// Token: 0x04000171 RID: 369
	private Action OnShowed = delegate()
	{
	};

	// Token: 0x04000172 RID: 370
	private Action OnHided = delegate()
	{
	};
}
﻿using System;
using UnityEngine;

// Token: 0x02000015 RID: 21
public static class CanvasExtensions
{
	// Token: 0x06000091 RID: 145 RVA: 0x00004A60 File Offset: 0x00002C60
	public static Vector3 WorldToCanvas(this Canvas canvas, Vector3 world_position, Camera camera = null)
	{
		if (camera == null)
		{
			camera = Camera.main;
		}
		Vector3 vector = camera.WorldToViewportPoint(world_position);
		RectTransform component = canvas.GetComponent<RectTransform>();
		return new Vector3(vector.x * component.sizeDelta.x - component.sizeDelta.x * 0.5f, vector.y * component.sizeDelta.y - component.sizeDelta.y * 0.5f, vector.z);
	}
}
﻿using System;
using UnityEngine;

// Token: 0x02000050 RID: 80
public class CanvasGroupMenuSwitcher : BaseMenuSwitcher
{
	// Token: 0x060001ED RID: 493 RVA: 0x0000A72B File Offset: 0x0000892B
	public CanvasGroupMenuSwitcher(CanvasGroup canvasGroup)
	{
		this._canvasGroup = canvasGroup;
		this._lerpProcess = new LerpProcess();
	}

	// Token: 0x060001EE RID: 494 RVA: 0x0000A750 File Offset: 0x00008950
	public override void HideMenu(bool isAnimated)
	{
		if (!isAnimated)
		{
			this._canvasGroup.alpha = 0f;
		}
		else
		{
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._canvasGroup.alpha = Mathf.Lerp(1f, 0f, t);
			}, this._time);
		}
		this._canvasGroup.interactable = false;
		this._canvasGroup.blocksRaycasts = false;
	}

	// Token: 0x060001EF RID: 495 RVA: 0x0000A7A8 File Offset: 0x000089A8
	public override void ShowMenu(bool isAnimated)
	{
		if (!isAnimated)
		{
			this._canvasGroup.alpha = 1f;
		}
		else
		{
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._canvasGroup.alpha = Mathf.Lerp(0f, 1f, t);
			}, this._time);
		}
		this._canvasGroup.interactable = true;
		this._canvasGroup.blocksRaycasts = true;
	}

	// Token: 0x04000173 RID: 371
	private CanvasGroup _canvasGroup;

	// Token: 0x04000174 RID: 372
	private LerpProcess _lerpProcess;

	// Token: 0x04000175 RID: 373
	private float _time = 0.25f;
}
﻿using System;
using System.Collections;
using UnityEngine;

// Token: 0x0200003C RID: 60
[RequireComponent(typeof(ParticleSystem))]
public class CFX_AutoDestructShuriken : MonoBehaviour
{
	// Token: 0x06000164 RID: 356 RVA: 0x00007F6A File Offset: 0x0000616A
	private void OnEnable()
	{
		base.StartCoroutine("CheckIfAlive");
	}

	// Token: 0x06000165 RID: 357 RVA: 0x00007F78 File Offset: 0x00006178
	private IEnumerator CheckIfAlive()
	{
		do
		{
			yield return new WaitForSeconds(0.5f);
		}
		while (base.GetComponent<ParticleSystem>().IsAlive(true));
		if (this.OnlyDeactivate)
		{
			base.gameObject.SetActive(false);
		}
		else
		{
			UnityEngine.Object.Destroy(base.gameObject);
		}
		yield break;
	}

	// Token: 0x040000D7 RID: 215
	public bool OnlyDeactivate;
}
﻿using System;
using UnityEngine;

// Token: 0x0200000C RID: 12
[RequireComponent(typeof(ParticleSystem))]
public class CFX_AutoStopLoopedEffect : MonoBehaviour
{
	// Token: 0x06000061 RID: 97 RVA: 0x000030E8 File Offset: 0x000012E8
	private void OnEnable()
	{
		this.d = this.effectDuration;
	}

	// Token: 0x06000062 RID: 98 RVA: 0x000030F8 File Offset: 0x000012F8
	private void Update()
	{
		if (this.d > 0f)
		{
			this.d -= Time.deltaTime;
			if (this.d <= 0f)
			{
				base.GetComponent<ParticleSystem>().Stop(true);
				CFX_Demo_Translate component = base.gameObject.GetComponent<CFX_Demo_Translate>();
				if (component != null)
				{
					component.enabled = false;
				}
			}
		}
	}

	// Token: 0x04000019 RID: 25
	public float effectDuration = 2.5f;

	// Token: 0x0400001A RID: 26
	private float d;
}
﻿using System;
using UnityEngine;

// Token: 0x0200000D RID: 13
public class CFX_Demo_RandomDir : MonoBehaviour
{
	// Token: 0x06000064 RID: 100 RVA: 0x0000316C File Offset: 0x0000136C
	private void Awake()
	{
		base.transform.eulerAngles = new Vector3(UnityEngine.Random.Range(this.min.x, this.max.x), UnityEngine.Random.Range(this.min.y, this.max.y), UnityEngine.Random.Range(this.min.z, this.max.z));
	}

	// Token: 0x0400001B RID: 27
	public Vector3 min = new Vector3(0f, 0f, 0f);

	// Token: 0x0400001C RID: 28
	public Vector3 max = new Vector3(0f, 360f, 0f);
}
﻿using System;
using UnityEngine;

// Token: 0x0200000E RID: 14
public class CFX_Demo_RotateCamera : MonoBehaviour
{
	// Token: 0x06000066 RID: 102 RVA: 0x00003216 File Offset: 0x00001416
	private void Update()
	{
		if (CFX_Demo_RotateCamera.rotating)
		{
			base.transform.RotateAround(this.rotationCenter.position, Vector3.up, this.speed * Time.deltaTime);
		}
	}

	// Token: 0x0400001D RID: 29
	public static bool rotating = true;

	// Token: 0x0400001E RID: 30
	public float speed = 30f;

	// Token: 0x0400001F RID: 31
	public Transform rotationCenter;
}
﻿using System;
using UnityEngine;

// Token: 0x0200000F RID: 15
public class CFX_Demo_Translate : MonoBehaviour
{
	// Token: 0x06000069 RID: 105 RVA: 0x00003264 File Offset: 0x00001464
	private void Start()
	{
		this.dir = new Vector3(UnityEngine.Random.Range(0f, 360f), UnityEngine.Random.Range(0f, 360f), UnityEngine.Random.Range(0f, 360f));
		this.dir.Scale(this.rotation);
		base.transform.localEulerAngles = this.dir;
	}

	// Token: 0x0600006A RID: 106 RVA: 0x000032CB File Offset: 0x000014CB
	private void Update()
	{
		base.transform.Translate(this.axis * this.speed * Time.deltaTime, Space.Self);
	}

	// Token: 0x04000020 RID: 32
	public float speed = 30f;

	// Token: 0x04000021 RID: 33
	public Vector3 rotation = Vector3.forward;

	// Token: 0x04000022 RID: 34
	public Vector3 axis = Vector3.forward;

	// Token: 0x04000023 RID: 35
	public bool gravity;

	// Token: 0x04000024 RID: 36
	private Vector3 dir;
}
﻿using System;
using UnityEngine;

// Token: 0x0200003D RID: 61
[RequireComponent(typeof(Light))]
public class CFX_LightIntensityFade : MonoBehaviour
{
	// Token: 0x06000167 RID: 359 RVA: 0x00007F87 File Offset: 0x00006187
	private void Start()
	{
		this.baseIntensity = base.GetComponent<Light>().intensity;
	}

	// Token: 0x06000168 RID: 360 RVA: 0x00007F9A File Offset: 0x0000619A
	private void OnEnable()
	{
		this.p_lifetime = 0f;
		this.p_delay = this.delay;
		if (this.delay > 0f)
		{
			base.GetComponent<Light>().enabled = false;
		}
	}

	// Token: 0x06000169 RID: 361 RVA: 0x00007FCC File Offset: 0x000061CC
	private void Update()
	{
		if (this.p_delay > 0f)
		{
			this.p_delay -= Time.deltaTime;
			if (this.p_delay <= 0f)
			{
				base.GetComponent<Light>().enabled = true;
			}
			return;
		}
		if (this.p_lifetime / this.duration < 1f)
		{
			base.GetComponent<Light>().intensity = Mathf.Lerp(this.baseIntensity, this.finalIntensity, this.p_lifetime / this.duration);
			this.p_lifetime += Time.deltaTime;
			return;
		}
		if (this.autodestruct)
		{
			UnityEngine.Object.Destroy(base.gameObject);
		}
	}

	// Token: 0x040000D8 RID: 216
	public float duration = 1f;

	// Token: 0x040000D9 RID: 217
	public float delay;

	// Token: 0x040000DA RID: 218
	public float finalIntensity;

	// Token: 0x040000DB RID: 219
	private float baseIntensity;

	// Token: 0x040000DC RID: 220
	public bool autodestruct;

	// Token: 0x040000DD RID: 221
	private float p_lifetime;

	// Token: 0x040000DE RID: 222
	private float p_delay;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x02000040 RID: 64
public class CFX_SpawnSystem : MonoBehaviour
{
	// Token: 0x06000173 RID: 371 RVA: 0x000082F4 File Offset: 0x000064F4
	public static GameObject GetNextObject(GameObject sourceObj, bool activateObject = true)
	{
		int instanceID = sourceObj.GetInstanceID();
		if (!CFX_SpawnSystem.instance.poolCursors.ContainsKey(instanceID))
		{
			Debug.LogError(string.Concat(new object[]
			{
				"[CFX_SpawnSystem.GetNextObject()] Object hasn't been preloaded: ",
				sourceObj.name,
				" (ID:",
				instanceID,
				")\n"
			}), CFX_SpawnSystem.instance);
			return null;
		}
		int num = CFX_SpawnSystem.instance.poolCursors[instanceID];
		GameObject gameObject;
		if (CFX_SpawnSystem.instance.onlyGetInactiveObjects)
		{
			int num2 = num;
			do
			{
				gameObject = CFX_SpawnSystem.instance.instantiatedObjects[instanceID][num];
				CFX_SpawnSystem.instance.increasePoolCursor(instanceID);
				num = CFX_SpawnSystem.instance.poolCursors[instanceID];
				if (gameObject != null && !gameObject.activeSelf)
				{
					goto IL_15E;
				}
			}
			while (num != num2);
			if (!CFX_SpawnSystem.instance.instantiateIfNeeded)
			{
				Debug.LogWarning("[CFX_SpawnSystem.GetNextObject()] There are no active instances available in the pool for \"" + sourceObj.name + "\"\nYou may need to increase the preloaded object count for this prefab?", CFX_SpawnSystem.instance);
				return null;
			}
			Debug.Log("[CFX_SpawnSystem.GetNextObject()] A new instance has been created for \"" + sourceObj.name + "\" because no active instance were found in the pool.\n", CFX_SpawnSystem.instance);
			CFX_SpawnSystem.PreloadObject(sourceObj, 1);
			List<GameObject> list = CFX_SpawnSystem.instance.instantiatedObjects[instanceID];
			gameObject = list[list.Count - 1];
		}
		else
		{
			gameObject = CFX_SpawnSystem.instance.instantiatedObjects[instanceID][num];
			CFX_SpawnSystem.instance.increasePoolCursor(instanceID);
		}
		IL_15E:
		if (activateObject && gameObject != null)
		{
			gameObject.SetActive(true);
		}
		return gameObject;
	}

	// Token: 0x06000174 RID: 372 RVA: 0x00008473 File Offset: 0x00006673
	public static void PreloadObject(GameObject sourceObj, int poolSize = 1)
	{
		CFX_SpawnSystem.instance.addObjectToPool(sourceObj, poolSize);
	}

	// Token: 0x06000175 RID: 373 RVA: 0x00008481 File Offset: 0x00006681
	public static void UnloadObjects(GameObject sourceObj)
	{
		CFX_SpawnSystem.instance.removeObjectsFromPool(sourceObj);
	}

	// Token: 0x1700000E RID: 14
	// (get) Token: 0x06000176 RID: 374 RVA: 0x0000848E File Offset: 0x0000668E
	public static bool AllObjectsLoaded
	{
		get
		{
			return CFX_SpawnSystem.instance.allObjectsLoaded;
		}
	}

	// Token: 0x06000177 RID: 375 RVA: 0x0000849C File Offset: 0x0000669C
	private void addObjectToPool(GameObject sourceObject, int number)
	{
		int instanceID = sourceObject.GetInstanceID();
		if (!this.instantiatedObjects.ContainsKey(instanceID))
		{
			this.instantiatedObjects.Add(instanceID, new List<GameObject>());
			this.poolCursors.Add(instanceID, 0);
		}
		for (int i = 0; i < number; i++)
		{
			GameObject gameObject = UnityEngine.Object.Instantiate<GameObject>(sourceObject);
			gameObject.SetActive(false);
			CFX_AutoDestructShuriken[] componentsInChildren = gameObject.GetComponentsInChildren<CFX_AutoDestructShuriken>(true);
			for (int j = 0; j < componentsInChildren.Length; j++)
			{
				componentsInChildren[j].OnlyDeactivate = true;
			}
			CFX_LightIntensityFade[] componentsInChildren2 = gameObject.GetComponentsInChildren<CFX_LightIntensityFade>(true);
			for (int j = 0; j < componentsInChildren2.Length; j++)
			{
				componentsInChildren2[j].autodestruct = false;
			}
			this.instantiatedObjects[instanceID].Add(gameObject);
			if (this.hideObjectsInHierarchy)
			{
				gameObject.hideFlags = HideFlags.HideInHierarchy;
			}
			if (this.spawnAsChildren)
			{
				gameObject.transform.parent = base.transform;
			}
		}
	}

	// Token: 0x06000178 RID: 376 RVA: 0x00008584 File Offset: 0x00006784
	private void removeObjectsFromPool(GameObject sourceObject)
	{
		int instanceID = sourceObject.GetInstanceID();
		if (!this.instantiatedObjects.ContainsKey(instanceID))
		{
			Debug.LogWarning(string.Concat(new object[]
			{
				"[CFX_SpawnSystem.removeObjectsFromPool()] There aren't any preloaded object for: ",
				sourceObject.name,
				" (ID:",
				instanceID,
				")\n"
			}), base.gameObject);
			return;
		}
		for (int i = this.instantiatedObjects[instanceID].Count - 1; i >= 0; i--)
		{
			UnityEngine.Object obj = this.instantiatedObjects[instanceID][i];
			this.instantiatedObjects[instanceID].RemoveAt(i);
			UnityEngine.Object.Destroy(obj);
		}
		this.instantiatedObjects.Remove(instanceID);
		this.poolCursors.Remove(instanceID);
	}

	// Token: 0x06000179 RID: 377 RVA: 0x00008648 File Offset: 0x00006848
	private void increasePoolCursor(int uniqueId)
	{
		Dictionary<int, int> dictionary = CFX_SpawnSystem.instance.poolCursors;
		int num = dictionary[uniqueId];
		dictionary[uniqueId] = num + 1;
		if (CFX_SpawnSystem.instance.poolCursors[uniqueId] >= CFX_SpawnSystem.instance.instantiatedObjects[uniqueId].Count)
		{
			CFX_SpawnSystem.instance.poolCursors[uniqueId] = 0;
		}
	}

	// Token: 0x0600017A RID: 378 RVA: 0x000086AA File Offset: 0x000068AA
	private void Awake()
	{
		if (CFX_SpawnSystem.instance != null)
		{
			Debug.LogWarning("CFX_SpawnSystem: There should only be one instance of CFX_SpawnSystem per Scene!\n", base.gameObject);
		}
		CFX_SpawnSystem.instance = this;
	}

	// Token: 0x0600017B RID: 379 RVA: 0x000086D0 File Offset: 0x000068D0
	private void Start()
	{
		this.allObjectsLoaded = false;
		for (int i = 0; i < this.objectsToPreload.Length; i++)
		{
			CFX_SpawnSystem.PreloadObject(this.objectsToPreload[i], this.objectsToPreloadTimes[i]);
		}
		this.allObjectsLoaded = true;
	}

	// Token: 0x040000EB RID: 235
	private static CFX_SpawnSystem instance;

	// Token: 0x040000EC RID: 236
	public GameObject[] objectsToPreload = new GameObject[0];

	// Token: 0x040000ED RID: 237
	public int[] objectsToPreloadTimes = new int[0];

	// Token: 0x040000EE RID: 238
	public bool hideObjectsInHierarchy;

	// Token: 0x040000EF RID: 239
	public bool spawnAsChildren = true;

	// Token: 0x040000F0 RID: 240
	public bool onlyGetInactiveObjects;

	// Token: 0x040000F1 RID: 241
	public bool instantiateIfNeeded;

	// Token: 0x040000F2 RID: 242
	private bool allObjectsLoaded;

	// Token: 0x040000F3 RID: 243
	private Dictionary<int, List<GameObject>> instantiatedObjects = new Dictionary<int, List<GameObject>>();

	// Token: 0x040000F4 RID: 244
	private Dictionary<int, int> poolCursors = new Dictionary<int, int>();
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using UnityEngine;

// Token: 0x02000071 RID: 113
[CreateAssetMenu(menuName = "MarsFPSKit/Input Manager/Changable")]
public class ChangableInputManager : Kit_InputManagerBase
{
	// Token: 0x06000316 RID: 790 RVA: 0x0000F330 File Offset: 0x0000D530
	public override void InitializeControls(Kit_PlayerBehaviour pb)
	{
		DefaultInputData defaultInputData = new DefaultInputData();
		pb.inputManagerData = defaultInputData;
		pb.input.weaponSlotUses = new bool[this.weaponSlotKeys.Length];
		defaultInputData.enemyPlayersAwareOff = new List<Kit_PlayerBehaviour>();
		this.spottingBoxSize = new Vector3(this.spottingBoxExtents.x, this.spottingBoxExtents.y, this.spottingMaxDistance / 2f);
	}

	// Token: 0x06000317 RID: 791 RVA: 0x0000F39C File Offset: 0x0000D59C
	public override void WriteToPlayerInput(Kit_PlayerBehaviour pb)
	{
		if (pb.inputManagerData != null && pb.inputManagerData.GetType() == typeof(DefaultInputData))
		{
			DefaultInputData defaultInputData = pb.inputManagerData as DefaultInputData;
			pb.input.hor = InputSettingsController.Instance.GetAxisInput("Horizontal");
			pb.input.ver = InputSettingsController.Instance.GetAxisInput("Vertical");
			pb.input.crouch = InputSettingsController.Instance.GetKeyInput(InputActions.Crouch);
			pb.input.sprint = InputSettingsController.Instance.GetKeyInput(InputActions.Run);
			pb.input.jump = InputSettingsController.Instance.GetKeyInput(InputActions.Jump);
			pb.input.dropWeapon = InputSettingsController.Instance.GetKeyInput(InputActions.DropWeapon);
			pb.input.lmb = InputSettingsController.Instance.GetKeyInput(InputActions.Fire);
			pb.input.rmb = InputSettingsController.Instance.GetKeyInput(InputActions.Aim);
			pb.input.reload = InputSettingsController.Instance.GetKeyInput(InputActions.Reload);
			pb.input.mouseX = Input.GetAxisRaw("Mouse X");
			pb.input.mouseY = Input.GetAxisRaw("Mouse Y");
			pb.input.leanLeft = InputSettingsController.Instance.GetKeyInput(InputActions.LeanLeft);
			pb.input.leanRight = InputSettingsController.Instance.GetKeyInput(InputActions.LeanRight);
			pb.input.thirdPerson = InputSettingsController.Instance.GetKeyInput(InputActions.ChangePerspective);
			pb.input.flashlight = InputSettingsController.Instance.GetKeyInput(InputActions.FlashLight);
			pb.input.laser = InputSettingsController.Instance.GetKeyInput(InputActions.Laser);
			pb.input.mouseWheel = Input.GetAxis("Mouse ScrollWheel");
			pb.input.dropBomb = InputSettingsController.Instance.GetKeyInput(InputActions.DropBomb);
			pb.input.prepareGrenade = InputSettingsController.Instance.GetKeyInput(InputActions.ActivateGrenade);
			if (pb.input.weaponSlotUses == null || pb.input.weaponSlotUses.Length != this.weaponSlotKeys.Length)
			{
				pb.input.weaponSlotUses = new bool[this.weaponSlotKeys.Length];
			}
			for (int i = 0; i < this.weaponSlotKeys.Length; i++)
			{
				int num = i;
				pb.input.weaponSlotUses[num] = InputSettingsController.Instance.GetKeyInput(this.WeaponSlotActions[num]);
			}
			if (Time.time > defaultInputData.lastScan)
			{
				defaultInputData.lastScan = Time.time + this.scanFrequency;
				this.ScanForEnemies(pb, defaultInputData);
			}
		}
	}

	// Token: 0x06000318 RID: 792 RVA: 0x0000F628 File Offset: 0x0000D828
	private void ScanForEnemies(Kit_PlayerBehaviour pb, DefaultInputData did)
	{
		Collider[] array = Physics.OverlapBox(pb.playerCameraTransform.position + pb.playerCameraTransform.forward * (this.spottingMaxDistance / 2f), this.spottingBoxSize, pb.playerCameraTransform.rotation, this.spottingLayer.value);
		did.enemyPlayersAwareOff.RemoveAll((Kit_PlayerBehaviour item) => item == null);
		for (int i = 0; i < array.Length; i++)
		{
			Kit_PlayerBehaviour component = array[i].transform.root.GetComponent<Kit_PlayerBehaviour>();
			if (component && component != pb && this.CanSeePlayer(pb, did, component) && this.isEnemyPlayer(pb, did, component) && !did.enemyPlayersAwareOff.Contains(component))
			{
				did.enemyPlayersAwareOff.Add(component);
				if (pb.voiceManager)
				{
					pb.voiceManager.SpottedEnemy(pb, component);
				}
			}
		}
	}

	// Token: 0x06000319 RID: 793 RVA: 0x0000F72C File Offset: 0x0000D92C
	private bool CanSeePlayer(Kit_PlayerBehaviour pb, DefaultInputData did, Kit_PlayerBehaviour enemyPlayer)
	{
		if (enemyPlayer)
		{
			Vector3 vector = enemyPlayer.playerCameraTransform.position - new Vector3(0f, 0.2f, 0f) - pb.playerCameraTransform.position;
			if (Vector3.Angle(vector, pb.playerCameraTransform.forward) < this.spottingFov)
			{
				RaycastHit raycastHit;
				return !Physics.Raycast(pb.playerCameraTransform.position, vector, out raycastHit, this.spottingRayDistance, this.spottingCheckLayers.value) || raycastHit.collider.transform.root == enemyPlayer.transform.root;
			}
		}
		return false;
	}

	// Token: 0x0600031A RID: 794 RVA: 0x0000F7E4 File Offset: 0x0000D9E4
	private bool isEnemyPlayer(Kit_PlayerBehaviour pb, DefaultInputData did, Kit_PlayerBehaviour enemyPlayer)
	{
		return pb && pb.main.currentGameModeBehaviour && (!pb.main.currentGameModeBehaviour.isTeamGameMode || pb.myTeam != enemyPlayer.myTeam);
	}

	// Token: 0x0400022B RID: 555
	public float scanFrequency = 1f;

	// Token: 0x0400022C RID: 556
	public string[] weaponSlotKeys;

	// Token: 0x0400022D RID: 557
	public InputActions[] WeaponSlotActions;

	// Token: 0x0400022E RID: 558
	[Header("Spotting")]
	public LayerMask spottingLayer;

	// Token: 0x0400022F RID: 559
	public LayerMask spottingCheckLayers;

	// Token: 0x04000230 RID: 560
	public float spottingMaxDistance = 50f;

	// Token: 0x04000231 RID: 561
	public Vector2 spottingBoxExtents = new Vector2(30f, 30f);

	// Token: 0x04000232 RID: 562
	private Vector3 spottingBoxSize;

	// Token: 0x04000233 RID: 563
	public float spottingFov = 90f;

	// Token: 0x04000234 RID: 564
	public float spottingRayDistance = 200f;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using UnityEngine;

// Token: 0x020000A0 RID: 160
public class ChangableScopeAttachment : Kit_AttachmentBehaviour
{
	// Token: 0x060003C2 RID: 962 RVA: 0x00010EF2 File Offset: 0x0000F0F2
	private void Awake()
	{
		this._lerpProcess = new LerpProcess();
		this._sceneDriver = UnityEngine.Object.FindObjectOfType<SceneDriver>();
	}

	// Token: 0x060003C3 RID: 963 RVA: 0x00010F0A File Offset: 0x0000F10A
	private void OnDestroy()
	{
		if (this._sceneDriver)
		{
			this._sceneDriver.OnUpdate -= this.CheckInput;
		}
	}

	// Token: 0x060003C4 RID: 964 RVA: 0x00010F30 File Offset: 0x0000F130
	private void CheckInput()
	{
		if (!this._playerBehaviour)
		{
			return;
		}
		Kit_PlayerBehaviour playerBehaviour = this._playerBehaviour;
		bool flag;
		if (playerBehaviour == null)
		{
			flag = false;
		}
		else
		{
			Kit_PlayerInput input = playerBehaviour.input;
			float? num = (input != null) ? new float?(input.mouseWheel) : null;
			float num2 = 0f;
			flag = (num.GetValueOrDefault() > num2 & num != null);
		}
		if (flag)
		{
			float start = this._scopeValues[this._currentValue];
			this._currentValue++;
			this._currentValue = Mathf.Clamp(this._currentValue, 0, this._scopeValues.Length - 1);
			float end = this._scopeValues[this._currentValue];
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._zoomCamera.fieldOfView = Mathf.Lerp(start, end, t);
			}, this._time);
			return;
		}
		Kit_PlayerBehaviour playerBehaviour2 = this._playerBehaviour;
		bool flag2;
		if (playerBehaviour2 == null)
		{
			flag2 = false;
		}
		else
		{
			Kit_PlayerInput input2 = playerBehaviour2.input;
			float? num = (input2 != null) ? new float?(input2.mouseWheel) : null;
			float num2 = 0f;
			flag2 = (num.GetValueOrDefault() < num2 & num != null);
		}
		if (flag2)
		{
			float start = this._scopeValues[this._currentValue];
			this._currentValue--;
			this._currentValue = Mathf.Clamp(this._currentValue, 0, this._scopeValues.Length - 1);
			float end = this._scopeValues[this._currentValue];
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._zoomCamera.fieldOfView = Mathf.Lerp(start, end, t);
			}, this._time);
		}
	}

	// Token: 0x060003C5 RID: 965 RVA: 0x000110D1 File Offset: 0x0000F2D1
	public override void Selected(Kit_PlayerBehaviour pb, AttachmentUseCase auc)
	{
		this._playerBehaviour = pb;
		if (this._playerBehaviour && this._sceneDriver)
		{
			this._sceneDriver.OnUpdate += this.CheckInput;
		}
	}

	// Token: 0x060003C6 RID: 966 RVA: 0x00010F0A File Offset: 0x0000F10A
	public override void Unselected(Kit_PlayerBehaviour pb, AttachmentUseCase auc)
	{
		if (this._sceneDriver)
		{
			this._sceneDriver.OnUpdate -= this.CheckInput;
		}
	}

	// Token: 0x040002ED RID: 749
	[SerializeField]
	private Camera _zoomCamera;

	// Token: 0x040002EE RID: 750
	[SerializeField]
	private float[] _scopeValues;

	// Token: 0x040002EF RID: 751
	private LerpProcess _lerpProcess;

	// Token: 0x040002F0 RID: 752
	private SceneDriver _sceneDriver;

	// Token: 0x040002F1 RID: 753
	private Kit_PlayerBehaviour _playerBehaviour;

	// Token: 0x040002F2 RID: 754
	private float _time = 0.1f;

	// Token: 0x040002F3 RID: 755
	private int _currentValue;
}
﻿using System;
using DG.Tweening;
using UnityEngine;

// Token: 0x020000A1 RID: 161
public class ChangeObjectives : MonoBehaviour
{
	// Token: 0x060003C8 RID: 968 RVA: 0x0001111E File Offset: 0x0000F31E
	public void Test()
	{
		this.PlaySequence();
	}

	// Token: 0x060003C9 RID: 969 RVA: 0x00011128 File Offset: 0x0000F328
	private void PlaySequence()
	{
		this.mainSequence = DOTween.Sequence();
		this.centralSpin.gameObject.SetActive(true);
		this.mainSequence.Append(this.centralSpin.DOLocalRotate(Vector3.forward * 180f, this.spinRotateDuration / this.mainDuration, RotateMode.LocalAxisAdd)).Append(this.centralSpin.parent.DOScale(Vector3.one * this.scaleMultiplier, this.scaleDuration / this.mainDuration)).Join(this.centralSpin.parent.DOLocalMoveY(0f, this.scaleDuration / this.mainDuration, false)).Join(this.objectives[0].DOLocalMoveX(-this.objectives[0].rect.width / 2f, this.objectiveMoveDuration / this.mainDuration, false)).Join(this.objectives[1].DOLocalMoveX(this.objectives[1].rect.width / 2f, this.objectiveMoveDuration / this.mainDuration, false)).AppendInterval(this.objectiveMoveDuration / this.mainDuration).Append(this.objectives[0].DOLocalMoveX(-100f, this.objectiveMoveDuration / this.mainDuration, false)).Join(this.objectives[1].DOLocalMoveX(100f, this.objectiveMoveDuration / this.mainDuration, false)).Append(this.objectives[0].parent.DOLocalRotate(Vector3.forward * 180f, this.objectiveRotateDuration / this.mainDuration, RotateMode.LocalAxisAdd)).Join(this.objectives[0].DOLocalRotate(Vector3.forward * 180f, this.objectiveRotateDuration / this.mainDuration, RotateMode.LocalAxisAdd)).Join(this.objectives[1].DOLocalRotate(Vector3.forward * 180f, this.objectiveRotateDuration / this.mainDuration, RotateMode.LocalAxisAdd)).Append(this.centralSpin.parent.DOScale(Vector3.one, this.scaleDuration / this.mainDuration)).Join(this.centralSpin.parent.DOLocalMoveY((float)(Screen.height / 2) + this._posY, this.scaleDuration / this.mainDuration, false));
		this.mainSequence.OnComplete(new TweenCallback(this.SequenceCompletted));
	}

	// Token: 0x060003CA RID: 970 RVA: 0x000113B5 File Offset: 0x0000F5B5
	private void SequenceCompletted()
	{
		this.centralSpin.gameObject.SetActive(false);
		this.mainSequence.Kill(false);
	}

	// Token: 0x040002F4 RID: 756
	[SerializeField]
	private GameObject background;

	// Token: 0x040002F5 RID: 757
	[SerializeField]
	private RectTransform centralSpin;

	// Token: 0x040002F6 RID: 758
	[SerializeField]
	private RectTransform[] objectives = new RectTransform[2];

	// Token: 0x040002F7 RID: 759
	[SerializeField]
	private float mainDuration = 1f;

	// Token: 0x040002F8 RID: 760
	[SerializeField]
	private float spinRotateDuration;

	// Token: 0x040002F9 RID: 761
	[SerializeField]
	private float objectiveRotateDuration;

	// Token: 0x040002FA RID: 762
	[SerializeField]
	private float objectiveMoveDuration;

	// Token: 0x040002FB RID: 763
	[SerializeField]
	private float scaleDuration;

	// Token: 0x040002FC RID: 764
	[SerializeField]
	private float scaleMultiplier;

	// Token: 0x040002FD RID: 765
	[SerializeField]
	private float _posY = -82f;

	// Token: 0x040002FE RID: 766
	private Sequence mainSequence;
}
﻿using System;
using TMPro;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.UI;

// Token: 0x0200000A RID: 10
public class ChatController : MonoBehaviour
{
	// Token: 0x0600005A RID: 90 RVA: 0x00002FA2 File Offset: 0x000011A2
	private void OnEnable()
	{
		this.TMP_ChatInput.onSubmit.AddListener(new UnityAction<string>(this.AddToChatOutput));
	}

	// Token: 0x0600005B RID: 91 RVA: 0x00002FC0 File Offset: 0x000011C0
	private void OnDisable()
	{
		this.TMP_ChatInput.onSubmit.RemoveListener(new UnityAction<string>(this.AddToChatOutput));
	}

	// Token: 0x0600005C RID: 92 RVA: 0x00002FE0 File Offset: 0x000011E0
	private void AddToChatOutput(string newText)
	{
		this.TMP_ChatInput.text = string.Empty;
		DateTime now = DateTime.Now;
		TMP_Text tmp_ChatOutput = this.TMP_ChatOutput;
		tmp_ChatOutput.text = string.Concat(new string[]
		{
			tmp_ChatOutput.text,
			"[<#FFFF80>",
			now.Hour.ToString("d2"),
			":",
			now.Minute.ToString("d2"),
			":",
			now.Second.ToString("d2"),
			"</color>] ",
			newText,
			"\n"
		});
		this.TMP_ChatInput.ActivateInputField();
		this.ChatScrollbar.value = 0f;
	}

	// Token: 0x04000013 RID: 19
	public TMP_InputField TMP_ChatInput;

	// Token: 0x04000014 RID: 20
	public TMP_Text TMP_ChatOutput;

	// Token: 0x04000015 RID: 21
	public Scrollbar ChatScrollbar;
}
﻿using System;
using MarsFPSKit;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000CE RID: 206
public class ChooseTeamMenuView : BaseMenuView
{
	// Token: 0x060004F7 RID: 1271 RVA: 0x000171B4 File Offset: 0x000153B4
	public override void MenuInit(ISwitchableMenu menuController)
	{
		base.MenuInit(menuController);
		this._waitProcess = new WaitProcess();
	}

	// Token: 0x060004F8 RID: 1272 RVA: 0x000171C8 File Offset: 0x000153C8
	public override void ShowMenu(bool isAnimated)
	{
		base.ShowMenu(isAnimated);
		if (!this._isSetuped)
		{
			this._waitProcess.StartProcess(delegate
			{
				this.Setup();
			}, 0.1f);
		}
	}

	// Token: 0x060004F9 RID: 1273 RVA: 0x000171F8 File Offset: 0x000153F8
	public void Setup()
	{
		TMP_Text componentInChildren = this._firstTeam.GetComponentInChildren<TextMeshProUGUI>();
		TextMeshProUGUI componentInChildren2 = this._secondTeam.GetComponentInChildren<TextMeshProUGUI>();
		TextMeshProUGUI componentInChildren3 = this._spectating.GetComponentInChildren<TextMeshProUGUI>();
		componentInChildren.text = LocalizationController.Instance.GetText("ChooseTeamMenu", this.main.gameInformation.allPvpTeams[0].teamName);
		componentInChildren2.text = LocalizationController.Instance.GetText("ChooseTeamMenu", this.main.gameInformation.allPvpTeams[1].teamName);
		componentInChildren3.text = LocalizationController.Instance.GetText("ChooseTeamMenu", "Spectate");
		this._firstTeam.onClick.AddListener(delegate()
		{
			this.MenuProcessing(0);
		});
		this._secondTeam.onClick.AddListener(delegate()
		{
			this.MenuProcessing(1);
		});
		this._spectating.onClick.AddListener(delegate()
		{
			this.main.spectatorManager.BeginSpectating(this.main, true);
			this._menuController.HideCurrentMenu();
		});
		this._isSetuped = true;
	}

	// Token: 0x060004FA RID: 1274 RVA: 0x000172F4 File Offset: 0x000154F4
	private void MenuProcessing(int team)
	{
		if (this.main.TryJoinTeam(team))
		{
			this._menuController.HideCurrentMenu();
		}
	}

	// Token: 0x04000463 RID: 1123
	public int teamSelectionId;

	// Token: 0x04000464 RID: 1124
	public Kit_IngameMain main;

	// Token: 0x04000465 RID: 1125
	public RectTransform teamGo;

	// Token: 0x04000466 RID: 1126
	public GameObject teamPrefab;

	// Token: 0x04000467 RID: 1127
	public AfterTeamSelection afterSelection;

	// Token: 0x04000468 RID: 1128
	[SerializeField]
	private Button _firstTeam;

	// Token: 0x04000469 RID: 1129
	[SerializeField]
	private Button _secondTeam;

	// Token: 0x0400046A RID: 1130
	[SerializeField]
	private Button _spectating;

	// Token: 0x0400046B RID: 1131
	private WaitProcess _waitProcess;

	// Token: 0x0400046C RID: 1132
	private bool _isSetuped;
}
﻿using System;
using DG.Tweening;
using UnityEngine;

// Token: 0x02000025 RID: 37
public class ComboKill : Achiwments
{
	// Token: 0x060000F8 RID: 248 RVA: 0x00005F78 File Offset: 0x00004178
	public override void PlaySequence()
	{
	}

	// Token: 0x060000F9 RID: 249 RVA: 0x00005FB4 File Offset: 0x000041B4
	public override void PlaySequence(int comboValue)
	{
		this._achivmentText.text = string.Format("x {0}", comboValue.ToString());
		this.SaveStats();
		this.mainSequence = DOTween.Sequence();
		this.mainSequence.Append(this._achivmentIcon.DOScale(Vector2.one, 0f)).Append(this._achivmentIcon.DOShakeScale(this.mainDuration, 0.1f, 10, 90f, true));
		this.mainSequence.OnComplete(new TweenCallback(this.EndSequence));
		this.AddPoints(this.points);
	}
}
﻿using System;

// Token: 0x020000C9 RID: 201
public static class Constants
{
	// Token: 0x040003F7 RID: 1015
	public const string SET_NEW_TOURNAMENT_API = "https://xgame-studio.online/Scripts/set_tournament.php";

	// Token: 0x040003F8 RID: 1016
	public const string SET_PLAYER_IN_CELL_API = "https://xgame-studio.online/Scripts/set_player_in_cell.php";

	// Token: 0x040003F9 RID: 1017
	public const string SET_ACCOUNT_API = "https://xgame-studio.online/Scripts/set_account.php";

	// Token: 0x040003FA RID: 1018
	public const string SET_PLAYER_STAISTICS_API = "https://xgame-studio.online/Scripts/set_player_statistics.php";

	// Token: 0x040003FB RID: 1019
	public const string SET_MATCH_STARTED_API = "https://xgame-studio.online/Scripts/set_match_started.php";

	// Token: 0x040003FC RID: 1020
	public const string SET_BATTLE_PASS_API = "https://xgame-studio.online/Scripts/set_battle_pass.php";

	// Token: 0x040003FD RID: 1021
	public const string SET_ACCOUNT_BATTLE_PASS_API = "https://xgame-studio.online/Scripts/set_account_battle_pass.php";

	// Token: 0x040003FE RID: 1022
	public const string GET_TOURNAMENTS_LIST_API = "https://xgame-studio.online/Scripts/get_tournaments.php";

	// Token: 0x040003FF RID: 1023
	public const string GET_TOURNAMENT_BY_NAME_API = "https://xgame-studio.online/Scripts/get_tournament_by_name.php";

	// Token: 0x04000400 RID: 1024
	public const string GET_TOURNAMENT_BY_ID_API = "https://xgame-studio.online/Scripts/get_tournament_by_id.php";

	// Token: 0x04000401 RID: 1025
	public const string GET_SERVER_TIME_API = "https://xgame-studio.online/Scripts/get_server_time.php";

	// Token: 0x04000402 RID: 1026
	public const string GET_PARTICIPATION_INFO_API = "https://xgame-studio.online/Scripts/get_tournament_participation_data.php";

	// Token: 0x04000403 RID: 1027
	public const string GET_MATCH_API = "https://xgame-studio.online/Scripts/get_tournament_match.php";

	// Token: 0x04000404 RID: 1028
	public const string GET_ACCOUNT_API = "https://xgame-studio.online/Scripts/get_account.php";

	// Token: 0x04000405 RID: 1029
	public const string GET_BATTLE_PASS_API = "https://xgame-studio.online/Scripts/get_battle_pass.php";

	// Token: 0x04000406 RID: 1030
	public const string EXCLUDE_PLAYER_FROM_TOURNAMENT_API = "https://xgame-studio.online/Scripts/exit_player_from_tournament.php";

	// Token: 0x04000407 RID: 1031
	public const string CHECK_TOURNAMENT_CODE_API = "https://xgame-studio.online/Scripts/check_tournament_code.php";

	// Token: 0x04000408 RID: 1032
	public const string CHECK_TOURNAMENT_MEMBER_API = "https://xgame-studio.online/Scripts/check_tournament_member.php";

	// Token: 0x04000409 RID: 1033
	public const string SEND_END_MATCH_API = "https://xgame-studio.online/Scripts/end_tournament_match.php";

	// Token: 0x0400040A RID: 1034
	public const string TEST_WEBHOOK_API = "https://hook.integromat.com/9vp8vk280bdul2sin5wlavd8pgsgn45w";

	// Token: 0x0400040B RID: 1035
	public const string ADD_STEAM_ITEM_API = "https://xgame-studio.online/Scripts/add_steam_item.php";

	// Token: 0x0400040C RID: 1036
	public const string RUSSIAN_PATH = "Rus";

	// Token: 0x0400040D RID: 1037
	public const string ENGLISH_PATH = "Eng";

	// Token: 0x0400040E RID: 1038
	public const string GROUP_DEFAULTVALUES = "DefaultValues";

	// Token: 0x0400040F RID: 1039
	public const string GROUP_GAMEMODESDISCRIPTIONS = "GameModesDiscriptions";

	// Token: 0x04000410 RID: 1040
	public const string GROUP_GAMEMMODES = "GameModes";

	// Token: 0x04000411 RID: 1041
	public const string GROUP_GRAPHIC_OPTIONS = "GraphicOptions";

	// Token: 0x04000412 RID: 1042
	public const string GROUP_OPTIONS_SCREEN = "OptionsScreen";

	// Token: 0x04000413 RID: 1043
	public const string GROUP_OPTIONS_HOVER_TEXT = "OptionsHoverText";

	// Token: 0x04000414 RID: 1044
	public const string GROUP_OPTIONS_DROPDOWN = "OptionsDropdown";

	// Token: 0x04000415 RID: 1045
	public const string GROUP_WEAPON_CATEGORIES = "WeaponCategories";

	// Token: 0x04000416 RID: 1046
	public const string GROUP_WEAPON_NAMES = "WeaponNames";

	// Token: 0x04000417 RID: 1047
	public const string GROUP_WEAPON_DESCRIPTIONS = "WeaponDescriptions";

	// Token: 0x04000418 RID: 1048
	public const string GROUP_TASK_DESCRIPTIONS = "TasksDescription";

	// Token: 0x04000419 RID: 1049
	public const string GROUP_WEAPON_FEATURES = "WeaponFeature";

	// Token: 0x0400041A RID: 1050
	public const string GROUP_CHOOSE_TEAM_MENU = "ChooseTeamMenu";

	// Token: 0x0400041B RID: 1051
	public const string GROUP_PAUSE_MENU = "PauseMenu";

	// Token: 0x0400041C RID: 1052
	public const string GROUP_ATTACHMENTS = "Attachments";

	// Token: 0x0400041D RID: 1053
	public const string GROUP_ATTACHMENTS_DESCRIPTION = "AttachmentsDescription";

	// Token: 0x0400041E RID: 1054
	public const string GROUP_HUD = "HUD";

	// Token: 0x0400041F RID: 1055
	public const string GROUP_REGISTRATION_VIEW = "TournamentRegistrationView";

	// Token: 0x04000420 RID: 1056
	public const string GROUP_SPECTATING_VIEW = "SpectatingScreen";

	// Token: 0x04000421 RID: 1057
	public const string GROUP_TOURNAMENTS_PARTICIPATIONSCREEN = "TournamentsParticipationScreen";

	// Token: 0x04000422 RID: 1058
	public const string GROUP_PLAYER_STATISTICS = "PlayerStatistics";

	// Token: 0x04000423 RID: 1059
	public const string GROUP_INPUT_OPTIONS_HOVER_TEXT = "InputOptionsHoverText";

	// Token: 0x04000424 RID: 1060
	public const string KEY_PLAYER = "Player";

	// Token: 0x04000425 RID: 1061
	public const string KEY_TOURNAMENT = "Tournament";

	// Token: 0x04000426 RID: 1062
	public const string KEY_PLAYERS1 = "Players1";

	// Token: 0x04000427 RID: 1063
	public const string KEY_PLAYERS2 = "Players2";

	// Token: 0x04000428 RID: 1064
	public const string KEY_DEATHMATCH = "Deathmatch";

	// Token: 0x04000429 RID: 1065
	public const string KEY_TEAMDEATHMATCH = "Team Deathmatch";

	// Token: 0x0400042A RID: 1066
	public const string KEY_TEAMDEATHMATCH_WITH_ROUNDS = "Team Dethmatch with Rounds";

	// Token: 0x0400042B RID: 1067
	public const string KEY_PLANTING_BOMB = "Planting Bomb";

	// Token: 0x0400042C RID: 1068
	public const string KEY_STAGE = "Stage";

	// Token: 0x0400042D RID: 1069
	public const string KEY_YES = "Yes";

	// Token: 0x0400042E RID: 1070
	public const string KEY_NO = "No";

	// Token: 0x0400042F RID: 1071
	public const string KEY_OPEN = "Open";

	// Token: 0x04000430 RID: 1072
	public const string KEY_GOING = "Going";

	// Token: 0x04000431 RID: 1073
	public const string KEY_ENDED = "Ended";

	// Token: 0x04000432 RID: 1074
	public const string KEY_ANISOTROPIC_NAME = "AnisotropicName";

	// Token: 0x04000433 RID: 1075
	public const string KEY_ANISOTROPIC_HOVER_TEXT = "AnisotropicHoverText";

	// Token: 0x04000434 RID: 1076
	public const string KEY_ANISOTROPIC_VAL_1 = "AnisotropicVal1";

	// Token: 0x04000435 RID: 1077
	public const string KEY_ANISOTROPIC_VAL_2 = "AnisotropicVal2";

	// Token: 0x04000436 RID: 1078
	public const string KEY_ANISOTROPIC_VAL_3 = "AnisotropicVal3";
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using MarsFPSKit.UI;
using TMPro;
using UnityEngine;

// Token: 0x020000AE RID: 174
[CreateAssetMenu(menuName = "MarsFPSKit/Options/Gameplay/CrosshairColor")]
public class CrosshairColorOptions : Kit_OptionBase
{
	// Token: 0x06000400 RID: 1024 RVA: 0x00011DD9 File Offset: 0x0000FFD9
	public override string GetDisplayName()
	{
		return "Crosshair Color";
	}

	// Token: 0x06000401 RID: 1025 RVA: 0x00011DE0 File Offset: 0x0000FFE0
	public override string GetHoverText()
	{
		return LocalizationController.Instance.GetText("OptionsHoverText", this.LocalizationKey);
	}

	// Token: 0x06000402 RID: 1026 RVA: 0x00011DF7 File Offset: 0x0000FFF7
	public override OptionType GetOptionType()
	{
		return OptionType.Dropdown;
	}

	// Token: 0x06000403 RID: 1027 RVA: 0x00011DFA File Offset: 0x0000FFFA
	public override void OnDropdowChange(TextMeshProUGUI txt, int newValue)
	{
		PlayerPrefs.SetInt(this._prefsKey, newValue);
		Kit_GameSettings.CrosshairColor = this._colors[newValue];
	}

	// Token: 0x06000404 RID: 1028 RVA: 0x00011E1C File Offset: 0x0001001C
	public override void OnDropdownStart(TextMeshProUGUI txt, TMP_Dropdown dropdown)
	{
		dropdown.ClearOptions();
		dropdown.AddOptions(this.CreateOptions());
		int num = this.LoadColor();
		dropdown.value = num;
		this.OnDropdowChange(txt, num);
	}

	// Token: 0x06000405 RID: 1029 RVA: 0x00011E54 File Offset: 0x00010054
	public int LoadColor()
	{
		int @int = PlayerPrefs.GetInt(this._prefsKey, 0);
		Kit_GameSettings.CrosshairColor = this._colors[@int];
		return @int;
	}

	// Token: 0x06000406 RID: 1030 RVA: 0x00011E80 File Offset: 0x00010080
	private List<string> CreateOptions()
	{
		List<string> list = new List<string>();
		for (int i = 0; i < this._colors.Count; i++)
		{
			list.Add(LocalizationController.Instance.GetText("OptionsDropdown", this._colorsNames[i]));
		}
		return list;
	}

	// Token: 0x0400031C RID: 796
	[SerializeField]
	private List<Color> _colors;

	// Token: 0x0400031D RID: 797
	[SerializeField]
	private List<string> _colorsNames;

	// Token: 0x0400031E RID: 798
	private string _prefsKey = "CrosshairColor";
}
﻿using System;
using MarsFPSKit;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000D5 RID: 213
public class CrosshairColorView : MonoBehaviour
{
	// Token: 0x06000536 RID: 1334 RVA: 0x0001801D File Offset: 0x0001621D
	private void OnEnable()
	{
		this.SetColor(Kit_GameSettings.CrosshairColor);
		Kit_GameSettings.OnCrosshairChanged += this.SetColor;
	}

	// Token: 0x06000537 RID: 1335 RVA: 0x0001803B File Offset: 0x0001623B
	private void OnDisable()
	{
		Kit_GameSettings.OnCrosshairChanged -= this.SetColor;
	}

	// Token: 0x06000538 RID: 1336 RVA: 0x00018050 File Offset: 0x00016250
	private void SetColor(Color color)
	{
		for (int i = 0; i < this._elements.Length; i++)
		{
			this._elements[i].color = color;
		}
	}

	// Token: 0x040004A0 RID: 1184
	[SerializeField]
	private Image[] _elements;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.UI;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000AF RID: 175
[CreateAssetMenu(menuName = "MarsFPSKit/Options/Gameplay/CrosshairSize")]
public class CrosshairSizeOptions : Kit_OptionBase
{
	// Token: 0x06000408 RID: 1032 RVA: 0x00011EDE File Offset: 0x000100DE
	public override string GetDisplayName()
	{
		return "Crosshair Size";
	}

	// Token: 0x06000409 RID: 1033 RVA: 0x00011DE0 File Offset: 0x0000FFE0
	public override string GetHoverText()
	{
		return LocalizationController.Instance.GetText("OptionsHoverText", this.LocalizationKey);
	}

	// Token: 0x0600040A RID: 1034 RVA: 0x00011EE5 File Offset: 0x000100E5
	public override OptionType GetOptionType()
	{
		return OptionType.Slider;
	}

	// Token: 0x0600040B RID: 1035 RVA: 0x00011EE8 File Offset: 0x000100E8
	public override void OnSliderStart(TextMeshProUGUI txt, Slider slider)
	{
		slider.minValue = this._sizeRange.x;
		slider.maxValue = this._sizeRange.y;
		slider.SetValueWithoutNotify(this.LoadSize());
	}

	// Token: 0x0600040C RID: 1036 RVA: 0x00011F18 File Offset: 0x00010118
	public override void OnSliderChange(TextMeshProUGUI txt, float newValue)
	{
		Kit_GameSettings.CrosshairSize = newValue;
		PlayerPrefs.SetFloat(this._prefsKey, newValue);
	}

	// Token: 0x0600040D RID: 1037 RVA: 0x00011F2C File Offset: 0x0001012C
	private float LoadSize()
	{
		return Kit_GameSettings.CrosshairSize = PlayerPrefs.GetFloat(this._prefsKey, 30f);
	}

	// Token: 0x0400031F RID: 799
	[SerializeField]
	private Vector2 _sizeRange;

	// Token: 0x04000320 RID: 800
	private string _prefsKey = "CrosshairSize";
}
﻿using System;
using UnityEngine;

// Token: 0x02000051 RID: 81
public class CustomOperations
{
	// Token: 0x17000014 RID: 20
	// (get) Token: 0x060001F2 RID: 498 RVA: 0x0000A839 File Offset: 0x00008A39
	public static CustomOperations Instance
	{
		get
		{
			if (CustomOperations._instance == null)
			{
				CustomOperations._instance = new CustomOperations();
			}
			return CustomOperations._instance;
		}
	}

	// Token: 0x060001F3 RID: 499 RVA: 0x0000292A File Offset: 0x00000B2A
	private CustomOperations()
	{
	}

	// Token: 0x060001F4 RID: 500 RVA: 0x0000A854 File Offset: 0x00008A54
	public Quaternion ClampRotationAroundXAxis(Quaternion q, float min, float max)
	{
		q.x /= q.w;
		q.y /= q.w;
		q.z /= q.w;
		q.w = 1f;
		float num = 114.59156f * Mathf.Atan(q.x);
		num = Mathf.Clamp(num, min, max);
		q.x = Mathf.Tan(0.008726646f * num);
		return q;
	}

	// Token: 0x04000176 RID: 374
	private static CustomOperations _instance;
}
﻿using System;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000A2 RID: 162
public class CustomUIMask : Image
{
	// Token: 0x1700004E RID: 78
	// (get) Token: 0x060003CC RID: 972 RVA: 0x000113FE File Offset: 0x0000F5FE
	public override Material materialForRendering
	{
		get
		{
			Material material = new Material(base.materialForRendering);
			material.SetInt("_StencilComp", 6);
			return material;
		}
	}
}
﻿using System;

// Token: 0x02000027 RID: 39
public class DailyAssault : DailyTask
{
	// Token: 0x06000106 RID: 262 RVA: 0x00006671 File Offset: 0x00004871
	protected override void Localize()
	{
		this.name = string.Format(LocalizationController.Instance.GetText("DailyTasks", "Assault"), this.target);
		this.changeLanguageEvent.Invoke();
	}

	// Token: 0x06000107 RID: 263 RVA: 0x000066A8 File Offset: 0x000048A8
	public override void StartTask(StaticticsServerData staticticsServer, int index)
	{
		this.isActive = true;
		this.countInStart = staticticsServer.Account.Statistics.KillByAssault;
		StatisticDataSave.SaveData(string.Format("StartedCount{0}", index), this.countInStart);
	}

	// Token: 0x06000108 RID: 264 RVA: 0x000066E2 File Offset: 0x000048E2
	public override int UpdateCounter(StaticticsServerData staticticsServer)
	{
		this.curCount = staticticsServer.Account.Statistics.KillByAssault - this.countInStart;
		return this.curCount;
	}
}
﻿using System;

// Token: 0x02000028 RID: 40
public class DailyAssist : DailyTask
{
	// Token: 0x0600010A RID: 266 RVA: 0x0000670F File Offset: 0x0000490F
	protected override void Localize()
	{
		this.name = string.Format(LocalizationController.Instance.GetText("DailyTasks", "Assist"), this.target);
		this.changeLanguageEvent.Invoke();
	}

	// Token: 0x0600010B RID: 267 RVA: 0x00006746 File Offset: 0x00004946
	public override void StartTask(StaticticsServerData staticticsServer, int index)
	{
		this.isActive = true;
		this.countInStart = staticticsServer.Account.Statistics.Assists;
		StatisticDataSave.SaveData(string.Format("StartedCount{0}", index), this.countInStart);
	}

	// Token: 0x0600010C RID: 268 RVA: 0x00006780 File Offset: 0x00004980
	public override int UpdateCounter(StaticticsServerData staticticsServer)
	{
		this.curCount = staticticsServer.Account.Statistics.Assists - this.countInStart;
		return this.curCount;
	}
}
﻿using System;

// Token: 0x02000029 RID: 41
public class DailyBombActivate : DailyTask
{
	// Token: 0x0600010E RID: 270 RVA: 0x000067A5 File Offset: 0x000049A5
	protected override void Localize()
	{
		this.name = string.Format(LocalizationController.Instance.GetText("DailyTasks", "Instail bombs"), this.target);
		this.changeLanguageEvent.Invoke();
	}

	// Token: 0x0600010F RID: 271 RVA: 0x000067DC File Offset: 0x000049DC
	public override void StartTask(StaticticsServerData staticticsServer, int index)
	{
		this.isActive = true;
		this.countInStart = staticticsServer.Account.Statistics.InstalledBombs;
		StatisticDataSave.SaveData(string.Format("StartedCount{0}", index), this.countInStart);
	}

	// Token: 0x06000110 RID: 272 RVA: 0x00006816 File Offset: 0x00004A16
	public override int UpdateCounter(StaticticsServerData staticticsServer)
	{
		this.curCount = staticticsServer.Account.Statistics.InstalledBombs - this.countInStart;
		return this.curCount;
	}
}
﻿using System;

// Token: 0x0200002A RID: 42
public class DailyGrenadeKill : DailyTask
{
	// Token: 0x06000112 RID: 274 RVA: 0x0000683B File Offset: 0x00004A3B
	protected override void Localize()
	{
		this.name = string.Format(LocalizationController.Instance.GetText("DailyTasks", "Kill by explosion"), this.target);
		this.changeLanguageEvent.Invoke();
	}

	// Token: 0x06000113 RID: 275 RVA: 0x00006872 File Offset: 0x00004A72
	public override void StartTask(StaticticsServerData staticticsServer, int index)
	{
		this.isActive = true;
		this.countInStart = staticticsServer.Account.Statistics.KilledByExplosion;
		StatisticDataSave.SaveData(string.Format("StartedCount{0}", index), this.countInStart);
	}

	// Token: 0x06000114 RID: 276 RVA: 0x000068AC File Offset: 0x00004AAC
	public override int UpdateCounter(StaticticsServerData staticticsServer)
	{
		this.curCount = staticticsServer.Account.Statistics.KilledByExplosion - this.countInStart;
		return this.curCount;
	}
}
﻿using System;

// Token: 0x0200002B RID: 43
public class DailyHeadshot : DailyTask
{
	// Token: 0x06000116 RID: 278 RVA: 0x000068D1 File Offset: 0x00004AD1
	protected override void Localize()
	{
		this.name = string.Format(LocalizationController.Instance.GetText("DailyTasks", "Headshot"), this.target);
		this.changeLanguageEvent.Invoke();
	}

	// Token: 0x06000117 RID: 279 RVA: 0x00006908 File Offset: 0x00004B08
	public override void StartTask(StaticticsServerData staticticsServer, int index)
	{
		this.isActive = true;
		this.countInStart = staticticsServer.Account.Statistics.HeadShot;
		StatisticDataSave.SaveData(string.Format("StartedCount{0}", index), this.countInStart);
	}

	// Token: 0x06000118 RID: 280 RVA: 0x00006942 File Offset: 0x00004B42
	public override int UpdateCounter(StaticticsServerData staticticsServer)
	{
		this.curCount = staticticsServer.Account.Statistics.HeadShot - this.countInStart;
		return this.curCount;
	}
}
﻿using System;

// Token: 0x0200002C RID: 44
public class DailyMeleeKill : DailyTask
{
	// Token: 0x0600011A RID: 282 RVA: 0x00006967 File Offset: 0x00004B67
	protected override void Localize()
	{
		this.name = string.Format(LocalizationController.Instance.GetText("DailyTasks", "Melee"), this.target);
		this.changeLanguageEvent.Invoke();
	}

	// Token: 0x0600011B RID: 283 RVA: 0x0000699E File Offset: 0x00004B9E
	public override void StartTask(StaticticsServerData staticticsServer, int index)
	{
		this.isActive = true;
		this.countInStart = staticticsServer.Account.Statistics.KillByMelee;
		StatisticDataSave.SaveData(string.Format("StartedCount{0}", index), this.countInStart);
	}

	// Token: 0x0600011C RID: 284 RVA: 0x000069D8 File Offset: 0x00004BD8
	public override int UpdateCounter(StaticticsServerData staticticsServer)
	{
		this.curCount = staticticsServer.Account.Statistics.KillByMelee - this.countInStart;
		return this.curCount;
	}
}
﻿using System;

// Token: 0x0200002D RID: 45
public class DailyPistol : DailyTask
{
	// Token: 0x0600011E RID: 286 RVA: 0x000069FD File Offset: 0x00004BFD
	protected override void Localize()
	{
		this.name = string.Format(LocalizationController.Instance.GetText("DailyTasks", "Pistol"), this.target);
		this.changeLanguageEvent.Invoke();
	}

	// Token: 0x0600011F RID: 287 RVA: 0x00006A34 File Offset: 0x00004C34
	public override void StartTask(StaticticsServerData staticticsServer, int index)
	{
		this.isActive = true;
		this.countInStart = staticticsServer.Account.Statistics.KillByPistol;
		StatisticDataSave.SaveData(string.Format("StartedCount{0}", index), this.countInStart);
	}

	// Token: 0x06000120 RID: 288 RVA: 0x00006A6E File Offset: 0x00004C6E
	public override int UpdateCounter(StaticticsServerData staticticsServer)
	{
		this.curCount = staticticsServer.Account.Statistics.KillByPistol - this.countInStart;
		return this.curCount;
	}
}
﻿using System;

// Token: 0x0200002E RID: 46
public class DailyRang : DailyTask
{
	// Token: 0x06000122 RID: 290 RVA: 0x00006A93 File Offset: 0x00004C93
	protected override void Localize()
	{
		this.name = string.Format(LocalizationController.Instance.GetText("DailyTasks", "ReachLevel"), this.target);
		this.changeLanguageEvent.Invoke();
	}

	// Token: 0x06000123 RID: 291 RVA: 0x00006ACA File Offset: 0x00004CCA
	public override void StartTask(StaticticsServerData staticticsServer, int index)
	{
		this.isActive = true;
	}

	// Token: 0x06000124 RID: 292 RVA: 0x00006AD3 File Offset: 0x00004CD3
	public override int UpdateCounter(StaticticsServerData staticticsServer)
	{
		this.curCount = staticticsServer.GetCurrentLevel();
		return this.curCount;
	}
}
﻿using System;

// Token: 0x0200002F RID: 47
public class DailyShotgun : DailyTask
{
	// Token: 0x06000126 RID: 294 RVA: 0x00006AE7 File Offset: 0x00004CE7
	protected override void Localize()
	{
		this.name = string.Format(LocalizationController.Instance.GetText("DailyTasks", "Shotgun"), this.target);
		this.changeLanguageEvent.Invoke();
	}

	// Token: 0x06000127 RID: 295 RVA: 0x00006B1E File Offset: 0x00004D1E
	public override void StartTask(StaticticsServerData staticticsServer, int index)
	{
		this.isActive = true;
		this.countInStart = staticticsServer.Account.Statistics.KillByShotGun;
		StatisticDataSave.SaveData(string.Format("StartedCount{0}", index), this.countInStart);
	}

	// Token: 0x06000128 RID: 296 RVA: 0x00006B58 File Offset: 0x00004D58
	public override int UpdateCounter(StaticticsServerData staticticsServer)
	{
		this.curCount = staticticsServer.Account.Statistics.KillByShotGun - this.countInStart;
		return this.curCount;
	}
}
﻿using System;

// Token: 0x02000030 RID: 48
public class DailySniper : DailyTask
{
	// Token: 0x0600012A RID: 298 RVA: 0x00006B7D File Offset: 0x00004D7D
	protected override void Localize()
	{
		this.name = string.Format(LocalizationController.Instance.GetText("DailyTasks", "Sniper"), this.target);
		this.changeLanguageEvent.Invoke();
	}

	// Token: 0x0600012B RID: 299 RVA: 0x00006BB4 File Offset: 0x00004DB4
	public override void StartTask(StaticticsServerData staticticsServer, int index)
	{
		this.isActive = true;
		this.countInStart = staticticsServer.Account.Statistics.KillBySniper;
		StatisticDataSave.SaveData(string.Format("StartedCount{0}", index), this.countInStart);
	}

	// Token: 0x0600012C RID: 300 RVA: 0x00006BEE File Offset: 0x00004DEE
	public override int UpdateCounter(StaticticsServerData staticticsServer)
	{
		this.curCount = staticticsServer.Account.Statistics.KillBySniper - this.countInStart;
		return this.curCount;
	}
}
﻿using System;
using MarsFPSKit;
using UnityEngine;
using UnityEngine.Events;

// Token: 0x02000031 RID: 49
public class DailyTask : MonoBehaviour
{
	// Token: 0x0600012E RID: 302 RVA: 0x00006C13 File Offset: 0x00004E13
	protected void OnEnable()
	{
		LocalizationController.Instance.OnLanguageChanged += this.Localize;
		this.Localize();
	}

	// Token: 0x0600012F RID: 303 RVA: 0x00006C32 File Offset: 0x00004E32
	protected virtual void Localize()
	{
		this.name = string.Format(LocalizationController.Instance.GetText("DailyTasks", "Kill enemies"), this.target);
		this.changeLanguageEvent.Invoke();
	}

	// Token: 0x06000130 RID: 304 RVA: 0x00006C69 File Offset: 0x00004E69
	public virtual void StartTask(StaticticsServerData staticticsServer, int index)
	{
		this.isActive = true;
		this.countInStart = staticticsServer.Account.Statistics.Kills;
		StatisticDataSave.SaveData(string.Format("StartedCount{0}", index), this.countInStart);
	}

	// Token: 0x06000131 RID: 305 RVA: 0x00006CA3 File Offset: 0x00004EA3
	public virtual int UpdateCounter(StaticticsServerData staticticsServer)
	{
		this.curCount = staticticsServer.Account.Statistics.Kills - this.countInStart;
		return this.curCount;
	}

	// Token: 0x06000132 RID: 306 RVA: 0x00006CC8 File Offset: 0x00004EC8
	public void CheckStatus()
	{
		if (this.curCount >= this.target)
		{
			this.isComlpete = true;
		}
	}

	// Token: 0x06000133 RID: 307 RVA: 0x00006CDF File Offset: 0x00004EDF
	public virtual void GetReward(Kit_GameInformation gameInformation)
	{
		this.isActive = false;
		gameInformation.leveling.AddXp(this.dailyReward);
		AccountScreenController.Instance.Initialize(gameInformation);
	}

	// Token: 0x06000134 RID: 308 RVA: 0x00006D04 File Offset: 0x00004F04
	public void AbortTask()
	{
		this.curCount = 0;
		this.isActive = false;
	}

	// Token: 0x06000135 RID: 309 RVA: 0x00006D14 File Offset: 0x00004F14
	private void OnDisable()
	{
		LocalizationController.Instance.OnLanguageChanged -= this.Localize;
	}

	// Token: 0x040000A1 RID: 161
	public int originalIndex;

	// Token: 0x040000A2 RID: 162
	public new string name;

	// Token: 0x040000A3 RID: 163
	public string description;

	// Token: 0x040000A4 RID: 164
	public int dailyReward;

	// Token: 0x040000A5 RID: 165
	public int target;

	// Token: 0x040000A6 RID: 166
	public int curCount;

	// Token: 0x040000A7 RID: 167
	public int countInStart;

	// Token: 0x040000A8 RID: 168
	public bool isActive;

	// Token: 0x040000A9 RID: 169
	public bool isComlpete;

	// Token: 0x040000AA RID: 170
	[HideInInspector]
	public UnityEvent changeLanguageEvent;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using Steamworks;
using UnityEngine;
using UnityEngine.Events;

// Token: 0x02000026 RID: 38
public class DailyTasksManager : MonoBehaviour
{
	// Token: 0x060000FB RID: 251 RVA: 0x00006064 File Offset: 0x00004264
	private void OnEnable()
	{
		this._accountView = UnityEngine.Object.FindObjectOfType<AccountScreenView>();
		if (this._accountView != null)
		{
			this._accountView.accountLoadEvent.AddListener(new UnityAction(this.OnLoadAccount));
			return;
		}
		Debug.Log("account view - null");
	}

	// Token: 0x060000FC RID: 252 RVA: 0x000060B4 File Offset: 0x000042B4
	private void Init()
	{
		if (this.todayTasks.Count != this._taskPerDay)
		{
			foreach (DailyTask dailyTask in this.todayTasks)
			{
				if (dailyTask == null)
				{
					UnityEngine.Object.Destroy(dailyTask.gameObject);
					this.todayTasks.Remove(dailyTask);
				}
			}
			for (int i = 0; i < this._taskPerDay; i++)
			{
				string key = string.Format("TaskIndex{0}", i);
				string key2 = string.Format("TaskCount{0}", i);
				string key3 = string.Format("TaskComplette{0}", i);
				string key4 = string.Format("ActiveComplette{0}", i);
				string key5 = string.Format("StartedCount{0}", i);
				DailyTask dailyTask3;
				if (StatisticDataSave.LoadDataInt(key) == -999)
				{
					Debug.Log("Saved task not found");
					int num = 0;
					List<int> list = new List<int>();
					using (List<DailyTask>.Enumerator enumerator = this.todayTasks.GetEnumerator())
					{
						while (enumerator.MoveNext())
						{
							DailyTask dailyTask2 = enumerator.Current;
							list.Add(dailyTask2.originalIndex);
						}
						goto IL_1A3;
					}
					goto IL_190;
					IL_1A3:
					if (!list.Contains(num))
					{
						dailyTask3 = UnityEngine.Object.Instantiate<DailyTask>(this.allTaskList[num], this.tasksRoot, false);
						goto IL_1C9;
					}
					IL_190:
					num = UnityEngine.Random.Range(0, this.allTaskList.Count);
					goto IL_1A3;
				}
				dailyTask3 = UnityEngine.Object.Instantiate<DailyTask>(this.allTaskList[StatisticDataSave.LoadDataInt(key)], this.tasksRoot, false);
				dailyTask3.countInStart = StatisticDataSave.LoadDataInt(key5);
				dailyTask3.curCount = StatisticDataSave.LoadDataInt(key2);
				dailyTask3.isComlpete = StatisticDataSave.LoadDataBool(key3);
				dailyTask3.isActive = StatisticDataSave.LoadDataBool(key4);
				dailyTask3.GetComponent<DailyTasksView>().SetView();
				IL_1C9:
				this.todayTasks.Add(dailyTask3);
			}
		}
		string key6 = "LastUpdateTaskDate";
		this._lastUpdateTask = StatisticDataSave.LoadDateTime(key6);
		this.CheckStatusTask();
	}

	// Token: 0x060000FD RID: 253 RVA: 0x000062DC File Offset: 0x000044DC
	public void StartTask(DailyTask task)
	{
		int index = this.todayTasks.IndexOf(task);
		task.StartTask(this.statisticData, index);
		this.Save();
	}

	// Token: 0x060000FE RID: 254 RVA: 0x0000630C File Offset: 0x0000450C
	public void CheckStatusTask()
	{
		foreach (DailyTask dailyTask in this.todayTasks)
		{
			if (dailyTask.isActive)
			{
				dailyTask.UpdateCounter(this.statisticData);
				dailyTask.CheckStatus();
			}
			dailyTask.GetComponent<DailyTasksView>().SetView();
		}
	}

	// Token: 0x060000FF RID: 255 RVA: 0x00006380 File Offset: 0x00004580
	public void CompletteTask(DailyTask task)
	{
		task.GetReward(this.gameInformation);
		this.Save();
	}

	// Token: 0x06000100 RID: 256 RVA: 0x00006394 File Offset: 0x00004594
	public void AbortTask(DailyTask task)
	{
		task.AbortTask();
		this.Save();
	}

	// Token: 0x06000101 RID: 257 RVA: 0x000063A4 File Offset: 0x000045A4
	private void UpdateTaskList()
	{
		for (int i = 0; i < this._taskPerDay; i++)
		{
			if (!this.todayTasks[i].isActive)
			{
				int num = 0;
				List<int> list = new List<int>();
				using (List<DailyTask>.Enumerator enumerator = this.todayTasks.GetEnumerator())
				{
					while (enumerator.MoveNext())
					{
						DailyTask dailyTask = enumerator.Current;
						list.Add(dailyTask.originalIndex);
						Debug.Log(dailyTask.originalIndex);
					}
					goto IL_96;
				}
				goto IL_74;
				IL_96:
				if (!list.Contains(num))
				{
					UnityEngine.Object.Destroy(this.todayTasks[i].gameObject);
					DailyTask value = UnityEngine.Object.Instantiate<DailyTask>(this.allTaskList[num], this.tasksRoot, false);
					this.todayTasks[i] = value;
					goto IL_DB;
				}
				IL_74:
				num = UnityEngine.Random.Range(0, this.allTaskList.Count);
				Debug.Log(string.Concat(num));
				goto IL_96;
			}
			IL_DB:;
		}
		this._lastUpdateTask = DateTimeOffset.FromUnixTimeSeconds((long)((ulong)SteamUtils.GetServerRealTime())).UtcDateTime;
		this.Save();
	}

	// Token: 0x06000102 RID: 258 RVA: 0x000064CC File Offset: 0x000046CC
	private void Save()
	{
		for (int i = 0; i < this.todayTasks.Count; i++)
		{
			string key = string.Format("TaskIndex{0}", i);
			string key2 = string.Format("TaskCount{0}", i);
			string key3 = string.Format("TaskComplette{0}", i);
			string key4 = string.Format("ActiveComplette{0}", i);
			StatisticDataSave.SaveData(key, this.todayTasks[i].originalIndex);
			StatisticDataSave.SaveData(key2, this.todayTasks[i].curCount);
			StatisticDataSave.SaveData(key3, this.todayTasks[i].isComlpete);
			StatisticDataSave.SaveData(key4, this.todayTasks[i].isActive);
		}
		StatisticDataSave.SaveData("LastUpdateTaskDate", this._lastUpdateTask);
	}

	// Token: 0x06000103 RID: 259 RVA: 0x000065A4 File Offset: 0x000047A4
	private void OnLoadAccount()
	{
		this.Init();
		TimeSpan timeSpan = DateTimeOffset.FromUnixTimeSeconds((long)((ulong)SteamUtils.GetServerRealTime())).UtcDateTime - this._lastUpdateTask;
		Debug.Log("Last update task" + timeSpan.TotalHours);
		if (timeSpan.TotalHours > this._updateHoursInterval)
		{
			this.UpdateTaskList();
		}
	}

	// Token: 0x06000104 RID: 260 RVA: 0x00006606 File Offset: 0x00004806
	private void OnDestroy()
	{
		if (this._accountView != null)
		{
			this._accountView.accountLoadEvent.RemoveListener(new UnityAction(this.OnLoadAccount));
		}
	}

	// Token: 0x04000097 RID: 151
	public Transform tasksRoot;

	// Token: 0x04000098 RID: 152
	public List<DailyTask> todayTasks = new List<DailyTask>();

	// Token: 0x04000099 RID: 153
	public List<DailyTask> allTaskList = new List<DailyTask>();

	// Token: 0x0400009A RID: 154
	public StaticticsServerData statisticData;

	// Token: 0x0400009B RID: 155
	private AccountScreenView _accountView;

	// Token: 0x0400009C RID: 156
	[SerializeField]
	private Kit_GameInformation gameInformation;

	// Token: 0x0400009D RID: 157
	[SerializeField]
	private int _taskPerDay = 3;

	// Token: 0x0400009E RID: 158
	[SerializeField]
	private double _updateHoursInterval = 0.08299999684095383;

	// Token: 0x0400009F RID: 159
	public float accountLoadDelay = 5f;

	// Token: 0x040000A0 RID: 160
	private DateTime _lastUpdateTask;
}
﻿using System;
using TMPro;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.UI;

// Token: 0x02000034 RID: 52
public class DailyTasksView : MonoBehaviour
{
	// Token: 0x06000141 RID: 321 RVA: 0x00006F10 File Offset: 0x00005110
	private void OnEnable()
	{
		this.tasksManager = base.transform.parent.GetComponent<DailyTasksManager>();
		this._task = base.transform.GetComponent<DailyTask>();
		this._startTaskButton.onClick.AddListener(new UnityAction(this.StartTask));
		this._getRewardButton.onClick.AddListener(new UnityAction(this.ConfirmComplette));
		this._abortDailyButton.onClick.AddListener(new UnityAction(this.AbortTask));
		this.SetView();
		this._task.changeLanguageEvent.AddListener(new UnityAction(this.SetView));
	}

	// Token: 0x06000142 RID: 322 RVA: 0x00006FBA File Offset: 0x000051BA
	private void StartTask()
	{
		this.tasksManager.StartTask(this._task);
		this.SetView();
	}

	// Token: 0x06000143 RID: 323 RVA: 0x00006FD3 File Offset: 0x000051D3
	private void ConfirmComplette()
	{
		this.tasksManager.CompletteTask(this._task);
		this.SetView();
	}

	// Token: 0x06000144 RID: 324 RVA: 0x00006FEC File Offset: 0x000051EC
	private void AbortTask()
	{
		this.tasksManager.AbortTask(this._task);
		this.SetView();
	}

	// Token: 0x06000145 RID: 325 RVA: 0x00007008 File Offset: 0x00005208
	public void SetView()
	{
		this._dailyDescription.text = this._task.description;
		this._dailyName.text = this._task.name;
		this._dailyProgressText.text = string.Format("{0}/{1}", this._task.curCount, this._task.target);
		this._dailyProgressBar.localScale = new Vector3((float)this._task.curCount / (float)this._task.target, 1f, 1f);
		if (this._task.isActive)
		{
			this._dailyProgressBar.parent.gameObject.SetActive(true);
			this._startTaskButton.gameObject.SetActive(false);
			this._getRewardButton.gameObject.SetActive(false);
			this._completteImage.SetActive(false);
			this._abortDailyButton.gameObject.SetActive(true);
			if (this._task.isComlpete)
			{
				this._dailyProgressBar.parent.gameObject.SetActive(false);
				this._startTaskButton.gameObject.SetActive(false);
				this._getRewardButton.gameObject.SetActive(true);
				this._completteImage.SetActive(false);
				this._abortDailyButton.gameObject.SetActive(false);
				return;
			}
		}
		else
		{
			if (this._task.isComlpete)
			{
				this._dailyProgressBar.parent.gameObject.SetActive(false);
				this._startTaskButton.gameObject.SetActive(false);
				this._getRewardButton.gameObject.SetActive(false);
				this._completteImage.SetActive(true);
				this._abortDailyButton.gameObject.SetActive(false);
				return;
			}
			this._dailyProgressBar.parent.gameObject.SetActive(false);
			this._startTaskButton.gameObject.SetActive(true);
			this._getRewardButton.gameObject.SetActive(false);
			this._completteImage.SetActive(false);
			this._abortDailyButton.gameObject.SetActive(false);
		}
	}

	// Token: 0x06000146 RID: 326 RVA: 0x0000722C File Offset: 0x0000542C
	private void OnDisable()
	{
		this._startTaskButton.onClick.RemoveListener(new UnityAction(this.StartTask));
		this._getRewardButton.onClick.RemoveListener(new UnityAction(this.ConfirmComplette));
		this._task.changeLanguageEvent.RemoveListener(new UnityAction(this.SetView));
		this._abortDailyButton.onClick.RemoveListener(new UnityAction(this.AbortTask));
	}

	// Token: 0x040000AB RID: 171
	public DailyTasksManager tasksManager;

	// Token: 0x040000AC RID: 172
	[SerializeField]
	private DailyTask _task;

	// Token: 0x040000AD RID: 173
	[SerializeField]
	private TextMeshProUGUI _dailyName;

	// Token: 0x040000AE RID: 174
	[SerializeField]
	private TextMeshProUGUI _dailyDescription;

	// Token: 0x040000AF RID: 175
	[SerializeField]
	private TextMeshProUGUI _dailyProgressText;

	// Token: 0x040000B0 RID: 176
	[SerializeField]
	private Transform _dailyProgressBar;

	// Token: 0x040000B1 RID: 177
	[SerializeField]
	private Button _startTaskButton;

	// Token: 0x040000B2 RID: 178
	[SerializeField]
	private Button _getRewardButton;

	// Token: 0x040000B3 RID: 179
	[SerializeField]
	private Button _abortDailyButton;

	// Token: 0x040000B4 RID: 180
	[SerializeField]
	private GameObject _completteImage;
}
﻿using System;

// Token: 0x02000032 RID: 50
public class DailyWin : DailyTask
{
	// Token: 0x06000137 RID: 311 RVA: 0x00006D2D File Offset: 0x00004F2D
	protected override void Localize()
	{
		this.name = string.Format(LocalizationController.Instance.GetText("DailyTasks", "Win"), this.target);
		this.changeLanguageEvent.Invoke();
	}

	// Token: 0x06000138 RID: 312 RVA: 0x00006D64 File Offset: 0x00004F64
	public override void StartTask(StaticticsServerData staticticsServer, int index)
	{
		this.isActive = true;
		this.countInStart = staticticsServer.Account.Statistics.WinMatch;
		StatisticDataSave.SaveData(string.Format("StartedCount{0}", index), this.countInStart);
	}

	// Token: 0x06000139 RID: 313 RVA: 0x00006D9E File Offset: 0x00004F9E
	public override int UpdateCounter(StaticticsServerData staticticsServer)
	{
		this.curCount = staticticsServer.Account.Statistics.WinMatch - this.countInStart;
		return this.curCount;
	}
}
﻿using System;

// Token: 0x02000094 RID: 148
public class DataLoader
{
	// Token: 0x17000042 RID: 66
	// (get) Token: 0x06000367 RID: 871 RVA: 0x0000FF91 File Offset: 0x0000E191
	public static DataLoader Instance
	{
		get
		{
			if (DataLoader._instance == null)
			{
				DataLoader._instance = new DataLoader();
			}
			return DataLoader._instance;
		}
	}

	// Token: 0x06000368 RID: 872 RVA: 0x0000292A File Offset: 0x00000B2A
	private DataLoader()
	{
	}

	// Token: 0x06000369 RID: 873 RVA: 0x00005F78 File Offset: 0x00004178
	public void StartLoadChain(string UserPhoneNumber)
	{
	}

	// Token: 0x0600036A RID: 874 RVA: 0x00005F78 File Offset: 0x00004178
	private void LoadCarsHistories()
	{
	}

	// Token: 0x040002C2 RID: 706
	private static DataLoader _instance;
}
﻿using System;
using Newtonsoft.Json;
using UnityEngine;

// Token: 0x02000095 RID: 149
public class DataSender
{
	// Token: 0x17000043 RID: 67
	// (get) Token: 0x0600036B RID: 875 RVA: 0x0000FFA9 File Offset: 0x0000E1A9
	public static DataSender Instance
	{
		get
		{
			if (DataSender._instance == null)
			{
				DataSender._instance = new DataSender();
			}
			return DataSender._instance;
		}
	}

	// Token: 0x0600036C RID: 876 RVA: 0x0000292A File Offset: 0x00000B2A
	private DataSender()
	{
	}

	// Token: 0x0600036D RID: 877 RVA: 0x0000FFC1 File Offset: 0x0000E1C1
	public void Initialize()
	{
		this._requester = UnityEngine.Object.FindObjectOfType<Requester>();
	}

	// Token: 0x0600036E RID: 878 RVA: 0x0000FFD0 File Offset: 0x0000E1D0
	public void SetNewTournament(string tournament, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("data", tournament);
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/set_tournament.php", wwwform, callback);
	}

	// Token: 0x0600036F RID: 879 RVA: 0x00010004 File Offset: 0x0000E204
	public void GetTournamentsList(ulong steamID, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("steamID", steamID.ToString());
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/get_tournaments.php", wwwform, callback);
	}

	// Token: 0x06000370 RID: 880 RVA: 0x0001003C File Offset: 0x0000E23C
	public void GetTournamentById(int tournamentId, string membersJson, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("id", tournamentId);
		wwwform.AddField("member", membersJson);
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/get_tournament_by_id.php", wwwform, callback);
	}

	// Token: 0x06000371 RID: 881 RVA: 0x0001007C File Offset: 0x0000E27C
	public void SetPlayerInCell(int team, int cell, string member, int tournamentId, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("team", team);
		wwwform.AddField("cell", cell);
		wwwform.AddField("member", member);
		wwwform.AddField("tournamentId", tournamentId);
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/set_player_in_cell.php", wwwform, callback);
	}

	// Token: 0x06000372 RID: 882 RVA: 0x000100D4 File Offset: 0x0000E2D4
	public void GetTournamentParticipationData(int tournamentId, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("id", tournamentId);
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/get_tournament_participation_data.php", wwwform, callback);
	}

	// Token: 0x06000373 RID: 883 RVA: 0x00010108 File Offset: 0x0000E308
	public void CheckTournamentCode(int tournamentId, string member, string code, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("tournamentId", tournamentId);
		wwwform.AddField("member", member);
		wwwform.AddField("code", code);
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/check_tournament_code.php", wwwform, callback);
	}

	// Token: 0x06000374 RID: 884 RVA: 0x00010154 File Offset: 0x0000E354
	public void GetMatch(int tournamentId, string member, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("tournamentId", tournamentId);
		wwwform.AddField("member", member);
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/get_tournament_match.php", wwwform, callback);
	}

	// Token: 0x06000375 RID: 885 RVA: 0x00010194 File Offset: 0x0000E394
	public void SendMatchEndToServer(int tournamentId, string matchJson, int winTeam, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("tournamentId", tournamentId);
		wwwform.AddField("match", matchJson);
		wwwform.AddField("winTeam", winTeam);
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/end_tournament_match.php", wwwform, callback);
	}

	// Token: 0x06000376 RID: 886 RVA: 0x000101E0 File Offset: 0x0000E3E0
	public void SetAccount(string accountJson, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("data", accountJson);
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/set_account.php", wwwform, callback);
	}

	// Token: 0x06000377 RID: 887 RVA: 0x00010214 File Offset: 0x0000E414
	public void GetAccount(ulong steamID, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("steamID", steamID.ToString());
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/get_account.php", wwwform, callback);
	}

	// Token: 0x06000378 RID: 888 RVA: 0x0001024C File Offset: 0x0000E44C
	public void GetStatistics(ulong steamId, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("steamID", steamId.ToString());
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/get_account.php", wwwform, callback);
	}

	// Token: 0x06000379 RID: 889 RVA: 0x00010284 File Offset: 0x0000E484
	public void SetPlayerStatistics(string accountJson, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("data", accountJson);
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/set_player_statistics.php", wwwform, callback);
	}

	// Token: 0x0600037A RID: 890 RVA: 0x000102B8 File Offset: 0x0000E4B8
	public void TestWebHooks(TournamentModel model)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("Tournament Name", model.Name);
		wwwform.AddField("Tournament ID", model.ID);
		wwwform.AddField("Tournament is Ended", model.IsEnded.ToString());
		wwwform.AddField("Tournament Ticket Price", model.TicketPrice.ToString());
		this._requester.GetRequestText("https://hook.integromat.com/9vp8vk280bdul2sin5wlavd8pgsgn45w", wwwform, delegate(string str)
		{
			Debug.Log("From Web Hook: " + str);
		});
	}

	// Token: 0x0600037B RID: 891 RVA: 0x0001034C File Offset: 0x0000E54C
	public void SetMatchStarted(string roomName, int tournamentId)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("roomName", roomName);
		wwwform.AddField("tournamentId", tournamentId);
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/set_match_started.php", wwwform, delegate(string str)
		{
		});
	}

	// Token: 0x0600037C RID: 892 RVA: 0x000103A8 File Offset: 0x0000E5A8
	public void AddSteamItem(ulong steamId, int itemDefid, Action<string> callback)
	{
		string value = JsonConvert.SerializeObject(itemDefid);
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("steamID", steamId.ToString());
		wwwform.AddField("itemDefId", value);
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/add_steam_item.php", wwwform, callback);
	}

	// Token: 0x0600037D RID: 893 RVA: 0x000103F8 File Offset: 0x0000E5F8
	public void AddSteamItem(ulong steamId, int[] itemDefid, Action<string> callback)
	{
		string value = JsonConvert.SerializeObject(itemDefid);
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("steamID", steamId.ToString());
		wwwform.AddField("itemDefId", value);
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/add_steam_item.php", wwwform, callback);
	}

	// Token: 0x0600037E RID: 894 RVA: 0x00010444 File Offset: 0x0000E644
	public void GetBattlePass(ulong steamId, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("steamID", steamId.ToString());
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/get_battle_pass.php", wwwform, callback);
	}

	// Token: 0x0600037F RID: 895 RVA: 0x0001047C File Offset: 0x0000E67C
	public void SetBattlePass(string jsonBattlePass, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("data", jsonBattlePass);
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/set_battle_pass.php", wwwform, callback);
	}

	// Token: 0x06000380 RID: 896 RVA: 0x000104B0 File Offset: 0x0000E6B0
	public void SetAccountBattlePass(ulong steamId, string jsonBattlePass, Action<string> callback)
	{
		WWWForm wwwform = new WWWForm();
		wwwform.AddField("data", jsonBattlePass);
		wwwform.AddField("steamID", steamId.ToString());
		this._requester.GetRequestText("https://xgame-studio.online/Scripts/set_account_battle_pass.php", wwwform, callback);
	}

	// Token: 0x040002C3 RID: 707
	private Requester _requester;

	// Token: 0x040002C4 RID: 708
	private static DataSender _instance;
}
﻿using System;
using UnityEngine;

// Token: 0x020000A3 RID: 163
public class DebugLogger : MonoBehaviour
{
	// Token: 0x060003CE RID: 974 RVA: 0x0001141F File Offset: 0x0000F61F
	private void OnEnable()
	{
		Application.logMessageReceived += this.Log;
	}

	// Token: 0x060003CF RID: 975 RVA: 0x00011432 File Offset: 0x0000F632
	private void OnDisable()
	{
		Application.logMessageReceived -= this.Log;
	}

	// Token: 0x060003D0 RID: 976 RVA: 0x00011448 File Offset: 0x0000F648
	public void Log(string logString, string stackTrace, LogType type)
	{
		this.output = logString;
		this.stack = stackTrace;
		DebugLogger.myLog = this.output + "\n" + DebugLogger.myLog;
		if (DebugLogger.myLog.Length > 5000)
		{
			DebugLogger.myLog = DebugLogger.myLog.Substring(0, 4000);
		}
	}

	// Token: 0x060003D1 RID: 977 RVA: 0x000114A3 File Offset: 0x0000F6A3
	private void OnGUI()
	{
		DebugLogger.myLog = GUI.TextArea(new Rect(10f, 10f, (float)(Screen.width - 10), (float)(Screen.height - 10)), DebugLogger.myLog);
	}

	// Token: 0x040002FF RID: 767
	private static string myLog = "";

	// Token: 0x04000300 RID: 768
	private string output;

	// Token: 0x04000301 RID: 769
	private string stack;
}
﻿using System;
using System.Collections;
using UnityEngine;

// Token: 0x0200001D RID: 29
public class DecalDestroyer : MonoBehaviour
{
	// Token: 0x060000DB RID: 219 RVA: 0x0000594C File Offset: 0x00003B4C
	private IEnumerator Start()
	{
		yield return new WaitForSeconds(this.lifeTime);
		UnityEngine.Object.Destroy(base.gameObject);
		yield break;
	}

	// Token: 0x04000067 RID: 103
	public float lifeTime = 5f;
}
﻿using System;
using UnityEngine;
using UnityEngine.EventSystems;

// Token: 0x020000D0 RID: 208
public class DragView : MonoBehaviour, IDragHandler, IEventSystemHandler, IPointerClickHandler
{
	// Token: 0x1400000B RID: 11
	// (add) Token: 0x06000510 RID: 1296 RVA: 0x0001770C File Offset: 0x0001590C
	// (remove) Token: 0x06000511 RID: 1297 RVA: 0x00017744 File Offset: 0x00015944
	public event Action<Vector2> OnMosueDrag = delegate(Vector2 <p0>)
	{
	};

	// Token: 0x1400000C RID: 12
	// (add) Token: 0x06000512 RID: 1298 RVA: 0x0001777C File Offset: 0x0001597C
	// (remove) Token: 0x06000513 RID: 1299 RVA: 0x000177B4 File Offset: 0x000159B4
	public event Action OnMouseClick = delegate()
	{
	};

	// Token: 0x06000514 RID: 1300 RVA: 0x000177E9 File Offset: 0x000159E9
	public void OnDrag(PointerEventData eventData)
	{
		this.OnMosueDrag(eventData.delta);
	}

	// Token: 0x06000515 RID: 1301 RVA: 0x000177FC File Offset: 0x000159FC
	public void OnPointerClick(PointerEventData eventData)
	{
		this.OnMouseClick();
	}
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Events;

// Token: 0x0200011B RID: 283
public class EasyFileSaveExtension
{
	// Token: 0x0600075C RID: 1884 RVA: 0x00022C74 File Offset: 0x00020E74
	public void Start()
	{
		this.AddExtension("BoxCollider", new UnityAction(this.BoxColliderExtension), new string[]
		{
			"centerX",
			"centerY",
			"centerZ",
			"sizeX",
			"sizeY",
			"sizeZ",
			"enabled",
			"isTrigger",
			"contactOffset"
		});
	}

	// Token: 0x0600075D RID: 1885 RVA: 0x00022CE8 File Offset: 0x00020EE8
	private void BoxColliderExtension()
	{
		BoxCollider boxCollider = (BoxCollider)this.GetData("BoxCollider");
		this.SetParameters("BoxCollider", new object[]
		{
			boxCollider.center.x,
			boxCollider.center.y,
			boxCollider.center.z,
			boxCollider.size.x,
			boxCollider.size.y,
			boxCollider.size.z,
			boxCollider.enabled,
			boxCollider.isTrigger,
			boxCollider.contactOffset
		});
	}

	// Token: 0x0600075E RID: 1886 RVA: 0x00022DB4 File Offset: 0x00020FB4
	private void AddExtension(string name, UnityAction callBack, string[] map)
	{
		if (!this.extensions.ContainsKey(name))
		{
			this.extensions.Add(name, callBack);
			this.data.Add(name, null);
			this.pars.Add(name, null);
			this.mapping.Add(name, new List<string>(map));
			return;
		}
		Debug.LogWarning("An extension with name '" + name + "' already exists.");
	}

	// Token: 0x0600075F RID: 1887 RVA: 0x00022E1E File Offset: 0x0002101E
	private object GetData(string extensionName)
	{
		if (this.data.ContainsKey(extensionName))
		{
			return this.data[extensionName];
		}
		return null;
	}

	// Token: 0x06000760 RID: 1888 RVA: 0x00022E3C File Offset: 0x0002103C
	private void SetParameters(string extensionName, params object[] parameters)
	{
		this.pars[extensionName] = new List<object>(parameters);
	}

	// Token: 0x040006A3 RID: 1699
	public Dictionary<string, UnityAction> extensions = new Dictionary<string, UnityAction>();

	// Token: 0x040006A4 RID: 1700
	public Dictionary<string, object> data = new Dictionary<string, object>();

	// Token: 0x040006A5 RID: 1701
	public Dictionary<string, List<object>> pars = new Dictionary<string, List<object>>();

	// Token: 0x040006A6 RID: 1702
	public Dictionary<string, List<string>> mapping = new Dictionary<string, List<string>>();
}
﻿using System;
using DG.Tweening;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000035 RID: 53
public class EndRoundUI : MonoBehaviour
{
	// Token: 0x06000148 RID: 328 RVA: 0x000072A9 File Offset: 0x000054A9
	public void PlaySequence(int roundResult)
	{
		switch (roundResult)
		{
		case -1:
			this.DefeatSequence();
			return;
		case 0:
			this.TieSequence();
			return;
		case 1:
			this.WinSequence();
			return;
		default:
			return;
		}
	}

	// Token: 0x06000149 RID: 329 RVA: 0x000072D4 File Offset: 0x000054D4
	private void WinSequence()
	{
		this.endRoundSequence = DOTween.Sequence();
		this.winCupImage.gameObject.SetActive(true);
		this.endRoundText.text = LocalizationController.Instance.GetText("HUD", "Victory");
		this.endRoundSequence.Append(this.winCupImage.parent.DOLocalMoveY(-400f, this.cupMoveDownDuration, false)).Join(this.winCupImage.parent.DOScale(this.cupMoveDownScale, this.cupMoveDownDuration)).Join(this.winCupImage.GetComponent<Image>().DOColor(Color.yellow, this.cupMoveDownDuration)).Append(this.winCupImage.parent.GetComponent<Image>().DOColor(Color.red, 1f)).Join(this.endRoundText.DOColor(Color.yellow, 1f)).AppendInterval(1f);
		this.endRoundSequence.OnComplete(new TweenCallback(this.WinSequenceComplette));
	}

	// Token: 0x0600014A RID: 330 RVA: 0x000073E8 File Offset: 0x000055E8
	private void WinSequenceComplette()
	{
		this.winCupImage.gameObject.SetActive(false);
		this.defeatCupImage[0].gameObject.SetActive(false);
		this.defeatCupImage[1].gameObject.SetActive(false);
		this.winCupImage.parent.DOLocalMove(new Vector2(0f, 200f), 0f, false);
		this.winCupImage.parent.DOScale(Vector2.one, 0f);
		this.defeatCupImage[1].DOLocalMove(new Vector2(0f, 57.3f), 0f, false);
		this.defeatCupImage[0].DOLocalMove(new Vector2(0f, 57.3f), 0f, false);
		this.defeatCupImage[1].GetComponent<Image>().DOColor(Color.white, 0f);
		this.defeatCupImage[0].GetComponent<Image>().DOColor(Color.white, 0f);
		this.defeatCupImage[0].parent.GetComponent<Image>().DOColor(Color.clear, 0f);
		this.endRoundText.color = Color.clear;
		this.endRoundText.text = "";
		this.endRoundSequence.Kill(false);
	}

	// Token: 0x0600014B RID: 331 RVA: 0x00007550 File Offset: 0x00005750
	private void DefeatSequence()
	{
		this.endRoundSequence = DOTween.Sequence();
		this.endRoundText.text = LocalizationController.Instance.GetText("HUD", "Defeat");
		this.defeatCupImage[0].gameObject.SetActive(true);
		this.defeatCupImage[1].gameObject.SetActive(true);
		this.endRoundSequence.Append(this.defeatCupImage[0].parent.DOLocalMoveY(-400f, this.cupMoveDownDuration, false)).Join(this.defeatCupImage[0].parent.DOScale(this.cupMoveDownScale, this.cupMoveDownDuration)).Join(this.defeatCupImage[0].GetComponent<Image>().DOColor(Color.black, this.cupMoveDownDuration)).Join(this.defeatCupImage[1].GetComponent<Image>().DOColor(Color.black, this.cupMoveDownDuration)).Append(this.defeatCupImage[0].parent.GetComponent<Image>().DOColor(Color.black, 1f)).Join(this.defeatCupImage[0].DOLocalMove(new Vector2((float)(-(float)Screen.width), (float)(-(float)Screen.height - 100)), this.crashCupDuration, false)).Join(this.defeatCupImage[1].DOLocalMove(new Vector2((float)Screen.width, (float)(-(float)Screen.height - 100)), this.crashCupDuration, false)).Join(this.endRoundText.DOColor(Color.red, 1f));
		this.endRoundSequence.OnComplete(new TweenCallback(this.WinSequenceComplette));
	}

	// Token: 0x0600014C RID: 332 RVA: 0x00007700 File Offset: 0x00005900
	private void TieSequence()
	{
		this.endRoundSequence = DOTween.Sequence();
		this.winCupImage.gameObject.SetActive(true);
		this.endRoundText.text = LocalizationController.Instance.GetText("HUD", "Tie");
		this.endRoundSequence.Append(this.winCupImage.parent.DOLocalMoveY(-400f, this.cupMoveDownDuration, false)).Join(this.winCupImage.parent.DOScale(this.cupMoveDownScale, this.cupMoveDownDuration)).Join(this.winCupImage.GetComponent<Image>().DOColor(Color.gray, this.cupMoveDownDuration)).Append(this.winCupImage.parent.GetComponent<Image>().DOColor(Color.black, 1f)).Join(this.endRoundText.DOColor(Color.gray, 1f)).AppendInterval(1f);
		this.endRoundSequence.OnComplete(new TweenCallback(this.WinSequenceComplette));
	}

	// Token: 0x040000B5 RID: 181
	[SerializeField]
	private RectTransform winCupImage;

	// Token: 0x040000B6 RID: 182
	[SerializeField]
	private RectTransform[] defeatCupImage = new RectTransform[4];

	// Token: 0x040000B7 RID: 183
	[SerializeField]
	private float cupMoveDownDuration;

	// Token: 0x040000B8 RID: 184
	[SerializeField]
	private float crashCupDuration;

	// Token: 0x040000B9 RID: 185
	[SerializeField]
	private float cupMoveDownScale;

	// Token: 0x040000BA RID: 186
	[SerializeField]
	private TMP_Text endRoundText;

	// Token: 0x040000BB RID: 187
	private Sequence endRoundSequence;
}
﻿using System;
using System.Collections;
using TMPro;
using UnityEngine;

// Token: 0x0200000B RID: 11
public class EnvMapAnimator : MonoBehaviour
{
	// Token: 0x0600005E RID: 94 RVA: 0x000030BA File Offset: 0x000012BA
	private void Awake()
	{
		this.m_textMeshPro = base.GetComponent<TMP_Text>();
		this.m_material = this.m_textMeshPro.fontSharedMaterial;
	}

	// Token: 0x0600005F RID: 95 RVA: 0x000030D9 File Offset: 0x000012D9
	private IEnumerator Start()
	{
		Matrix4x4 matrix = default(Matrix4x4);
		for (;;)
		{
			matrix.SetTRS(Vector3.zero, Quaternion.Euler(Time.time * this.RotationSpeeds.x, Time.time * this.RotationSpeeds.y, Time.time * this.RotationSpeeds.z), Vector3.one);
			this.m_material.SetMatrix("_EnvMatrix", matrix);
			yield return null;
		}
		yield break;
	}

	// Token: 0x04000016 RID: 22
	public Vector3 RotationSpeeds;

	// Token: 0x04000017 RID: 23
	private TMP_Text m_textMeshPro;

	// Token: 0x04000018 RID: 24
	private Material m_material;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.Weapons;

// Token: 0x020000A4 RID: 164
public static class ExtentionMethods
{
	// Token: 0x060003D4 RID: 980 RVA: 0x000114E4 File Offset: 0x0000F6E4
	public static int GetWeaponID(this Kit_GameInformation game, Kit_WeaponBase weapon)
	{
		int result = -1;
		Kit_WeaponBase[] allWeapons = game.allWeapons;
		for (int i = 0; i < allWeapons.Length; i++)
		{
			if (weapon == allWeapons[i])
			{
				result = i;
				break;
			}
		}
		return result;
	}

	// Token: 0x060003D5 RID: 981 RVA: 0x00011518 File Offset: 0x0000F718
	public static int GetWeaponCategoryID(this Kit_GameInformation game, string category)
	{
		int result = -1;
		string[] allWeaponCategories = game.allWeaponCategories;
		for (int i = 0; i < allWeaponCategories.Length; i++)
		{
			if (category == allWeaponCategories[i])
			{
				result = i;
				break;
			}
		}
		return result;
	}

	// Token: 0x060003D6 RID: 982 RVA: 0x0001154C File Offset: 0x0000F74C
	public static void SetupAutoSpawnAndBotManager(this Kit_IngameMain main, bool mode)
	{
		main.autoSpawnSystem.IsStandartMode = mode;
		main.IsStandartGameMode = mode;
	}
}
﻿using System;
using System.Collections;
using UnityEngine;

// Token: 0x0200001E RID: 30
public class ExtinguishableFire : MonoBehaviour
{
	// Token: 0x060000DD RID: 221 RVA: 0x0000596E File Offset: 0x00003B6E
	private void Start()
	{
		this.m_isExtinguished = true;
		this.smokeParticleSystem.Stop();
		this.fireParticleSystem.Stop();
		base.StartCoroutine(this.StartingFire());
	}

	// Token: 0x060000DE RID: 222 RVA: 0x0000599A File Offset: 0x00003B9A
	public void Extinguish()
	{
		if (this.m_isExtinguished)
		{
			return;
		}
		this.m_isExtinguished = true;
		base.StartCoroutine(this.Extinguishing());
	}

	// Token: 0x060000DF RID: 223 RVA: 0x000059B9 File Offset: 0x00003BB9
	private IEnumerator Extinguishing()
	{
		this.fireParticleSystem.Stop();
		this.smokeParticleSystem.time = 0f;
		this.smokeParticleSystem.Play();
		for (float elapsedTime = 0f; elapsedTime < 2f; elapsedTime += Time.deltaTime)
		{
			float d = Mathf.Max(0f, 1f - elapsedTime / 2f);
			this.fireParticleSystem.transform.localScale = Vector3.one * d;
			yield return null;
		}
		yield return new WaitForSeconds(2f);
		this.smokeParticleSystem.Stop();
		this.fireParticleSystem.transform.localScale = Vector3.one;
		yield return new WaitForSeconds(4f);
		base.StartCoroutine(this.StartingFire());
		yield break;
	}

	// Token: 0x060000E0 RID: 224 RVA: 0x000059C8 File Offset: 0x00003BC8
	private IEnumerator StartingFire()
	{
		this.smokeParticleSystem.Stop();
		this.fireParticleSystem.time = 0f;
		this.fireParticleSystem.Play();
		for (float elapsedTime = 0f; elapsedTime < 2f; elapsedTime += Time.deltaTime)
		{
			float d = Mathf.Min(1f, elapsedTime / 2f);
			this.fireParticleSystem.transform.localScale = Vector3.one * d;
			yield return null;
		}
		this.fireParticleSystem.transform.localScale = Vector3.one;
		this.m_isExtinguished = false;
		yield break;
	}

	// Token: 0x04000068 RID: 104
	public ParticleSystem fireParticleSystem;

	// Token: 0x04000069 RID: 105
	public ParticleSystem smokeParticleSystem;

	// Token: 0x0400006A RID: 106
	protected bool m_isExtinguished;

	// Token: 0x0400006B RID: 107
	private const float m_FireStartingTime = 2f;
}
﻿using System;
using TMPro;
using UnityEngine;

// Token: 0x020000A5 RID: 165
public class FPSCounter : MonoBehaviour
{
	// Token: 0x060003D7 RID: 983 RVA: 0x00011564 File Offset: 0x0000F764
	private void Update()
	{
		this.fpsAccunulator++;
		if (Time.realtimeSinceStartup > this.fpsNextPeriod)
		{
			this.currentfps = (int)((float)this.fpsAccunulator / this.FpsMeasuredPeriod);
			this.fpsAccunulator = 0;
			this.fpsNextPeriod += this.FpsMeasuredPeriod;
			this._fpsText.text = this.currentfps.ToString();
		}
	}

	// Token: 0x04000302 RID: 770
	[SerializeField]
	private TextMeshProUGUI _fpsText;

	// Token: 0x04000303 RID: 771
	private float FpsMeasuredPeriod = 0.5f;

	// Token: 0x04000304 RID: 772
	private int fpsAccunulator;

	// Token: 0x04000305 RID: 773
	private float fpsNextPeriod;

	// Token: 0x04000306 RID: 774
	private int currentfps;
}
﻿using System;
using ExitGames.Client.Photon;
using Photon.Pun;
using Photon.Realtime;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000D2 RID: 210
public class FriendsGroupView : MonoBehaviourPunCallbacks
{
	// Token: 0x06000521 RID: 1313 RVA: 0x00017A47 File Offset: 0x00015C47
	public override void OnEnable()
	{
		base.OnEnable();
		this._createGroupButton.onClick.AddListener(delegate()
		{
			this.CreateRoom();
		});
	}

	// Token: 0x06000522 RID: 1314 RVA: 0x00017A6B File Offset: 0x00015C6B
	public override void OnDisable()
	{
		base.OnDisable();
		this._createGroupButton.onClick.RemoveAllListeners();
	}

	// Token: 0x06000523 RID: 1315 RVA: 0x00017A84 File Offset: 0x00015C84
	private void CreateRoom()
	{
		RoomOptions roomOptions = new RoomOptions();
		roomOptions.MaxPlayers = 10;
		roomOptions.CustomRoomProperties = new Hashtable();
		roomOptions.CustomRoomProperties.Add("lobby", true);
		roomOptions.CustomRoomProperties.Add("map", 0);
		roomOptions.CustomRoomProperties.Add("gameMode", 0);
		roomOptions.CustomRoomProperties.Add("duration", 0);
		roomOptions.CustomRoomProperties.Add("ping", 0);
		roomOptions.CustomRoomProperties.Add("afk", 0);
		roomOptions.CustomRoomProperties.Add("bots", false);
		roomOptions.CustomRoomProperties.Add("password", "");
		roomOptions.CustomRoomProperties.Add("playerNeeded", 0);
		roomOptions.CustomRoomPropertiesForLobby = new string[]
		{
			"lobby",
			"map",
			"gameMode",
			"duration",
			"bots",
			"password"
		};
		PhotonNetwork.OfflineMode = false;
		PhotonNetwork.CreateRoom(PhotonNetwork.LocalPlayer.NickName + "'s Lobby", roomOptions, null, null);
	}

	// Token: 0x04000487 RID: 1159
	[SerializeField]
	private Button _createGroupButton;

	// Token: 0x04000488 RID: 1160
	[SerializeField]
	private TextMeshProUGUI _createGroupText;
}
﻿using System;
using System.Collections.Generic;
using Photon.Realtime;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000D3 RID: 211
public class FriendsPanelView : MonoBehaviour
{
	// Token: 0x06000526 RID: 1318 RVA: 0x00017BD8 File Offset: 0x00015DD8
	private void OnEnable()
	{
		this._addFreindButton.onClick.AddListener(delegate()
		{
			this._onAddFriendButtonClick(this._friendInputField.text);
		});
	}

	// Token: 0x06000527 RID: 1319 RVA: 0x00017BF6 File Offset: 0x00015DF6
	private void OnDisable()
	{
		this._addFreindButton.onClick.RemoveAllListeners();
	}

	// Token: 0x06000528 RID: 1320 RVA: 0x00017C08 File Offset: 0x00015E08
	public void ViewInit(MyFriendsManager manager, Action<string> onAddButtonClick)
	{
		this._controller = manager;
		this._onAddFriendButtonClick = onAddButtonClick;
	}

	// Token: 0x06000529 RID: 1321 RVA: 0x00017C18 File Offset: 0x00015E18
	public void UpdateFriendsList(List<FriendInfo> friendList)
	{
		this.ClearView();
		for (int i = 0; i < friendList.Count; i++)
		{
			FriendView friendView = UnityEngine.Object.Instantiate<FriendView>(this._friendViewPrefub, this._content);
			friendView.SetView(friendList[i].UserId, this.DefineColor(friendList[i]), friendList[i].IsInRoom);
			int index = i;
			friendView.OnJoinButtonClick += delegate()
			{
				this._controller.JoinRoom(friendList[index].Room);
			};
			friendView.OnRemoveButtonClick += delegate()
			{
				this._controller.RemoveFriend(friendList[index].UserId);
			};
			RectTransform component = friendView.GetComponent<RectTransform>();
			component.anchoredPosition = new Vector2(component.anchoredPosition.x, (float)(-(float)i) * component.sizeDelta.y);
			this._friendsViews.Add(friendView);
		}
		this._content.sizeDelta = new Vector2(this._content.sizeDelta.x, (float)friendList.Count * this._friendViewPrefub.GetComponent<RectTransform>().sizeDelta.y);
	}

	// Token: 0x0600052A RID: 1322 RVA: 0x00017D6C File Offset: 0x00015F6C
	private void ClearView()
	{
		if (this._friendsViews != null)
		{
			for (int i = 0; i < this._friendsViews.Count; i++)
			{
				UnityEngine.Object.Destroy(this._friendsViews[i].gameObject);
			}
		}
		this._friendsViews = new List<FriendView>();
	}

	// Token: 0x0600052B RID: 1323 RVA: 0x00017DB8 File Offset: 0x00015FB8
	private Color DefineColor(FriendInfo info)
	{
		Color result = this._offlineColor;
		if (info.IsOnline)
		{
			result = this._onlineNotJoinableColor;
			if (info.IsInRoom)
			{
				result = this._onlineJoinableColor;
			}
		}
		return result;
	}

	// Token: 0x04000489 RID: 1161
	[SerializeField]
	private RectTransform _content;

	// Token: 0x0400048A RID: 1162
	[SerializeField]
	private FriendView _friendViewPrefub;

	// Token: 0x0400048B RID: 1163
	[SerializeField]
	private TMP_InputField _friendInputField;

	// Token: 0x0400048C RID: 1164
	[SerializeField]
	private Button _addFreindButton;

	// Token: 0x0400048D RID: 1165
	[SerializeField]
	private Color _onlineJoinableColor;

	// Token: 0x0400048E RID: 1166
	[SerializeField]
	private Color _onlineNotJoinableColor;

	// Token: 0x0400048F RID: 1167
	[SerializeField]
	private Color _offlineColor;

	// Token: 0x04000490 RID: 1168
	private List<FriendView> _friendsViews;

	// Token: 0x04000491 RID: 1169
	private MyFriendsManager _controller;

	// Token: 0x04000492 RID: 1170
	private Action<string> _onAddFriendButtonClick = delegate(string <p0>)
	{
	};
}
﻿using System;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000D1 RID: 209
public class FriendView : MonoBehaviour
{
	// Token: 0x1400000D RID: 13
	// (add) Token: 0x06000517 RID: 1303 RVA: 0x0001786C File Offset: 0x00015A6C
	// (remove) Token: 0x06000518 RID: 1304 RVA: 0x000178A4 File Offset: 0x00015AA4
	public event Action OnJoinButtonClick = delegate()
	{
	};

	// Token: 0x1400000E RID: 14
	// (add) Token: 0x06000519 RID: 1305 RVA: 0x000178DC File Offset: 0x00015ADC
	// (remove) Token: 0x0600051A RID: 1306 RVA: 0x00017914 File Offset: 0x00015B14
	public event Action OnRemoveButtonClick = delegate()
	{
	};

	// Token: 0x0600051B RID: 1307 RVA: 0x00017949 File Offset: 0x00015B49
	private void OnEnable()
	{
		this._joinButton.onClick.AddListener(delegate()
		{
			this.OnJoinButtonClick();
		});
		this._removeButton.onClick.AddListener(delegate()
		{
			this.OnRemoveButtonClick();
		});
	}

	// Token: 0x0600051C RID: 1308 RVA: 0x00017983 File Offset: 0x00015B83
	private void OnDisable()
	{
		this._joinButton.onClick.RemoveAllListeners();
		this._removeButton.onClick.RemoveAllListeners();
	}

	// Token: 0x0600051D RID: 1309 RVA: 0x000179A5 File Offset: 0x00015BA5
	public void SetView(string name, Color indicatorColor, bool isInRoom)
	{
		this._friendNameText.text = name;
		this._onlineIndicator.color = indicatorColor;
		this._joinButton.gameObject.SetActive(isInRoom);
	}

	// Token: 0x04000483 RID: 1155
	[SerializeField]
	private Image _onlineIndicator;

	// Token: 0x04000484 RID: 1156
	[SerializeField]
	private TextMeshProUGUI _friendNameText;

	// Token: 0x04000485 RID: 1157
	[SerializeField]
	private Button _joinButton;

	// Token: 0x04000486 RID: 1158
	[SerializeField]
	private Button _removeButton;
}
﻿using System;
using MarsFPSKit;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000A6 RID: 166
public class FullMap : BaseMenuView
{
	// Token: 0x060003D9 RID: 985 RVA: 0x000115E4 File Offset: 0x0000F7E4
	private void Update()
	{
		this.SyncAlpha(Kit_GameSettings.MapTransparent);
	}

	// Token: 0x060003DA RID: 986 RVA: 0x000115F1 File Offset: 0x0000F7F1
	public override void MenuInit(ISwitchableMenu menuController)
	{
		base.MenuInit(menuController);
		this.SyncAlpha(Kit_GameSettings.MapTransparent);
	}

	// Token: 0x060003DB RID: 987 RVA: 0x00011605 File Offset: 0x0000F805
	public override void ShowMenu(bool isAnimated)
	{
		base.ShowMenu(isAnimated);
	}

	// Token: 0x060003DC RID: 988 RVA: 0x0001160E File Offset: 0x0000F80E
	public override void HideMenu(bool isAnimated)
	{
		base.HideMenu(isAnimated);
	}

	// Token: 0x060003DD RID: 989 RVA: 0x00011618 File Offset: 0x0000F818
	private void SyncAlpha(float alpha)
	{
		this._mapImage.color = new Color(this._mapImage.color.r, this._mapImage.color.g, this._mapImage.color.b, alpha);
	}

	// Token: 0x04000307 RID: 775
	[SerializeField]
	private GameObject _fullMap;

	// Token: 0x04000308 RID: 776
	[SerializeField]
	private RawImage _mapImage;

	// Token: 0x04000309 RID: 777
	private KeyCode _mapKey = KeyCode.M;

	// Token: 0x0400030A RID: 778
	private float _startAlpha = 0.5f;
}
﻿using System;
using TMPro;
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.UI;

// Token: 0x020000D4 RID: 212
public class GameModeCardView : MonoBehaviour, IPointerEnterHandler, IEventSystemHandler, IPointerExitHandler
{
	// Token: 0x0600052E RID: 1326 RVA: 0x00017E30 File Offset: 0x00016030
	private void Start()
	{
		this._lerpProcess1 = new LerpProcess();
		this._lerpProcess2 = new LerpProcess();
	}

	// Token: 0x0600052F RID: 1327 RVA: 0x00017E48 File Offset: 0x00016048
	private void SelectCard()
	{
		this._lerpProcess1.StartProcess(delegate(float t)
		{
			this._cardBackgroundImage.color = Color.Lerp(this._deselectedImageColor, this._selectedImageColor, t);
			this._cardHeadText.color = Color.Lerp(this._deselectTextColor, this._selectedTextColor, t);
			this._cardDiscriotionText.color = Color.Lerp(this._deselectTextColor, this._selectedTextColor, t);
			this._cardTransform.localScale = Vector3.Lerp(this._deselectedSize, this._selectedSize, t);
		}, this._time);
	}

	// Token: 0x06000530 RID: 1328 RVA: 0x00017E67 File Offset: 0x00016067
	private void DeselectCard()
	{
		this._lerpProcess2.StartProcess(delegate(float t)
		{
			this._cardBackgroundImage.color = Color.Lerp(this._selectedImageColor, this._deselectedImageColor, t);
			this._cardHeadText.color = Color.Lerp(this._selectedTextColor, this._deselectTextColor, t);
			this._cardDiscriotionText.color = Color.Lerp(this._selectedTextColor, this._deselectTextColor, t);
			this._cardTransform.localScale = Vector3.Lerp(this._selectedSize, this._deselectedSize, t);
		}, this._time);
	}

	// Token: 0x06000531 RID: 1329 RVA: 0x00017E86 File Offset: 0x00016086
	public void OnPointerEnter(PointerEventData eventData)
	{
		this.SelectCard();
	}

	// Token: 0x06000532 RID: 1330 RVA: 0x00017E8E File Offset: 0x0001608E
	public void OnPointerExit(PointerEventData eventData)
	{
		this.DeselectCard();
	}

	// Token: 0x04000493 RID: 1171
	[SerializeField]
	private RectTransform _cardTransform;

	// Token: 0x04000494 RID: 1172
	[SerializeField]
	private Image _cardBackgroundImage;

	// Token: 0x04000495 RID: 1173
	[SerializeField]
	private TextMeshProUGUI _cardHeadText;

	// Token: 0x04000496 RID: 1174
	[SerializeField]
	private TextMeshProUGUI _cardDiscriotionText;

	// Token: 0x04000497 RID: 1175
	[SerializeField]
	private Color _selectedImageColor = Color.white;

	// Token: 0x04000498 RID: 1176
	[SerializeField]
	private Color _selectedTextColor = Color.red;

	// Token: 0x04000499 RID: 1177
	private Vector3 _selectedSize = new Vector3(1.1f, 1.1f, 1.1f);

	// Token: 0x0400049A RID: 1178
	[SerializeField]
	private Color _deselectedImageColor = new Color(0.2169f, 0.2169f, 0.2169f);

	// Token: 0x0400049B RID: 1179
	[SerializeField]
	private Color _deselectTextColor = Color.white;

	// Token: 0x0400049C RID: 1180
	private Vector3 _deselectedSize = Vector3.one;

	// Token: 0x0400049D RID: 1181
	private LerpProcess _lerpProcess1;

	// Token: 0x0400049E RID: 1182
	private LerpProcess _lerpProcess2;

	// Token: 0x0400049F RID: 1183
	private float _time = 0.1f;
}
﻿using System;
using UnityEngine;

// Token: 0x02000016 RID: 22
public static class GameObjectExtension
{
	// Token: 0x06000092 RID: 146 RVA: 0x00004ADF File Offset: 0x00002CDF
	public static void SetActiveOptimized(this GameObject go, bool active)
	{
		if (active)
		{
			if (!go.activeSelf)
			{
				go.SetActive(true);
				return;
			}
		}
		else if (go.activeSelf)
		{
			go.SetActive(false);
		}
	}
}
﻿using System;
using MarsFPSKit;
using TMPro;
using UnityEngine;

// Token: 0x020000D9 RID: 217
public class GameSettingsView : MonoBehaviour
{
	// Token: 0x06000543 RID: 1347 RVA: 0x00018489 File Offset: 0x00016689
	private void OnEnable()
	{
		LocalizationController.Instance.OnLanguageChanged += this.UpdateAllSettingsViews;
	}

	// Token: 0x06000544 RID: 1348 RVA: 0x000184A1 File Offset: 0x000166A1
	private void OnDisable()
	{
		LocalizationController.Instance.OnLanguageChanged -= this.UpdateAllSettingsViews;
	}

	// Token: 0x06000545 RID: 1349 RVA: 0x000184BC File Offset: 0x000166BC
	public void ViewInit(HostGameScreenController hostGameScreenController, Kit_GameInformation game)
	{
		this._hostGameScreenController = hostGameScreenController;
		this._game = game;
		this._gameModeView.ViewInit(delegate
		{
			this.ChangeGameMode(-1);
		}, delegate
		{
			this.ChangeGameMode(1);
		});
		this._mapView.ViewInit(delegate
		{
			this.ChangeMap(-1);
		}, delegate
		{
			this.ChangeMap(1);
		});
		this._durationView.ViewInit(delegate
		{
			this.ChangeDuration(-1);
		}, delegate
		{
			this.ChangeDuration(1);
		});
		this._playersLimitView.ViewInit(delegate
		{
			this.ChangePlayerLimit(-1);
		}, delegate
		{
			this.ChangePlayerLimit(1);
		});
		this._playersNeededView.ViewInit(delegate
		{
			this.ChangePlayerNeeded(-1);
		}, delegate
		{
			this.ChangePlayerNeeded(1);
		});
		this._pingLimitView.ViewInit(delegate
		{
			this.ChangePingLimit(-1);
		}, delegate
		{
			this.ChangePingLimit(1);
		});
		this._afkLimitView.ViewInit(delegate
		{
			this.ChangeAFKLimit(-1);
		}, delegate
		{
			this.ChangeAFKLimit(1);
		});
		this._botsView.ViewInit(delegate
		{
			this.ChangeBots(-1);
		}, delegate
		{
			this.ChangeBots(1);
		});
		this._connectivityView.ViewInit(delegate
		{
			this.ChangeOnlineMode(-1);
		}, delegate
		{
			this.ChangeOnlineMode(1);
		});
		this._roundCountView.ViewInit(delegate
		{
			this.ChangeRoundCount(-1);
		}, delegate
		{
			this.ChangeRoundCount(1);
		});
		this._nameField.text = this._hostGameScreenController.HostGameData.NameFieldText;
		this._nameField.onEndEdit.AddListener(delegate(string str)
		{
			this._hostGameScreenController.SetName(str);
		});
		this._passwordField.onEndEdit.AddListener(delegate(string str)
		{
			this._hostGameScreenController.SetPassword(str);
		});
		this.UpdateAllSettingsViews();
		Kit_PvP_GameModeBase kit_PvP_GameModeBase = this._game.allPvpGameModes[this._hostGameScreenController.HostGameData.GameMode];
		this._mapViewer.ChangeMapImage(kit_PvP_GameModeBase.traditionalMaps[this._hostGameScreenController.HostGameData.Map].mapPicture);
	}

	// Token: 0x06000546 RID: 1350 RVA: 0x000186D4 File Offset: 0x000168D4
	private void UpdateAllSettingsViews()
	{
		Kit_PvP_GameModeBase kit_PvP_GameModeBase = this._game.allPvpGameModes[this._hostGameScreenController.HostGameData.GameMode];
		this._gameModeView.UpdateValue(LocalizationController.Instance.GetText("GameModes", kit_PvP_GameModeBase.gameModeName));
		this._gameModeDiscriptionText.text = LocalizationController.Instance.GetText("GameModesDiscriptions", kit_PvP_GameModeBase.gameModeName);
		this._mapView.UpdateValue(kit_PvP_GameModeBase.traditionalMaps[this._hostGameScreenController.HostGameData.Map].mapName);
		if (kit_PvP_GameModeBase.traditionalDurations[this._hostGameScreenController.HostGameData.Duration] != 60)
		{
			this._durationView.UpdateValue(string.Format("{0} {1}", kit_PvP_GameModeBase.traditionalDurations[this._hostGameScreenController.HostGameData.Duration] / 60, LocalizationController.Instance.GetText("DefaultValues", "minutes")));
		}
		else
		{
			this._durationView.UpdateValue(string.Format("{0} {1}", kit_PvP_GameModeBase.traditionalDurations[this._hostGameScreenController.HostGameData.Duration] / 60, LocalizationController.Instance.GetText("DefaultValues", "minutes")));
		}
		byte b = kit_PvP_GameModeBase.traditionalPlayerLimits[this._hostGameScreenController.HostGameData.PlayerLimit];
		if (b > 1 && b < 5)
		{
			this._playersLimitView.UpdateValue(string.Format("{0} {1}", b, LocalizationController.Instance.GetText("DefaultValues", "Players1")));
		}
		else if (b > 5)
		{
			this._playersLimitView.UpdateValue(string.Format("{0} {1}", b, LocalizationController.Instance.GetText("DefaultValues", "Players2")));
		}
		else
		{
			this._playersLimitView.UpdateValue(string.Format("{0} {1}", kit_PvP_GameModeBase.traditionalPlayerLimits[this._hostGameScreenController.HostGameData.PlayerLimit], LocalizationController.Instance.GetText("DefaultValues", "Player")));
		}
		int num = kit_PvP_GameModeBase.traditionalPlayerNeeded[this._hostGameScreenController.HostGameData.PlayerNeeded];
		if (num > 1 && num < 5)
		{
			this._playersNeededView.UpdateValue(string.Format("{0} {1}", num, LocalizationController.Instance.GetText("DefaultValues", "Players1")));
		}
		else if (num > 5)
		{
			this._playersNeededView.UpdateValue(string.Format("{0} {1}", num, LocalizationController.Instance.GetText("DefaultValues", "Players2")));
		}
		else
		{
			this._playersNeededView.UpdateValue(string.Format("{0} {1}", kit_PvP_GameModeBase.traditionalPlayerNeeded[this._hostGameScreenController.HostGameData.PlayerNeeded], LocalizationController.Instance.GetText("DefaultValues", "Player")));
		}
		if (kit_PvP_GameModeBase.traditionalPingLimits[this._hostGameScreenController.HostGameData.PingLimit] > 0)
		{
			this._pingLimitView.UpdateValue(kit_PvP_GameModeBase.traditionalPingLimits[this._hostGameScreenController.HostGameData.PingLimit].ToString() + " " + LocalizationController.Instance.GetText("DefaultValues", "ms"));
		}
		else
		{
			this._pingLimitView.UpdateValue(LocalizationController.Instance.GetText("DefaultValues", "Disabled"));
		}
		if (kit_PvP_GameModeBase.traditionalAfkLimits[this._hostGameScreenController.HostGameData.AFKLimit] > 0)
		{
			if (kit_PvP_GameModeBase.traditionalAfkLimits[this._hostGameScreenController.HostGameData.AFKLimit] != 1)
			{
				this._afkLimitView.UpdateValue(string.Format("{0} {1}", kit_PvP_GameModeBase.traditionalAfkLimits[this._hostGameScreenController.HostGameData.AFKLimit], LocalizationController.Instance.GetText("DefaultValues", "seconds")));
			}
			else
			{
				this._afkLimitView.UpdateValue(string.Format("{0} {1}", kit_PvP_GameModeBase.traditionalAfkLimits[this._hostGameScreenController.HostGameData.AFKLimit], LocalizationController.Instance.GetText("DefaultValues", "seconds")));
			}
		}
		else
		{
			this._afkLimitView.UpdateValue(LocalizationController.Instance.GetText("DefaultValues", "Disabled"));
		}
		if (this._hostGameScreenController.HostGameData.BotsMode == 0)
		{
			this._botsView.UpdateValue(LocalizationController.Instance.GetText("DefaultValues", "Disabled"));
		}
		else
		{
			this._botsView.UpdateValue(LocalizationController.Instance.GetText("DefaultValues", "Enabled"));
		}
		if (this._hostGameScreenController.HostGameData.OnlineMode == 0)
		{
			this._connectivityView.UpdateValue(LocalizationController.Instance.GetText("DefaultValues", "Online"));
		}
		else
		{
			this._connectivityView.UpdateValue(LocalizationController.Instance.GetText("DefaultValues", "Offline"));
		}
		if (kit_PvP_GameModeBase is IRounds)
		{
			IRounds rounds = (IRounds)kit_PvP_GameModeBase;
			this._roundCountView.gameObject.SetActive(true);
			this._roundCountView.UpdateValue(rounds.Rounds[this._hostGameScreenController.HostGameData.RoundCount].ToString());
			return;
		}
		this._roundCountView.gameObject.SetActive(false);
	}

	// Token: 0x06000547 RID: 1351 RVA: 0x00018C11 File Offset: 0x00016E11
	private void ChangeGameMode(int value)
	{
		this._hostGameScreenController.ChangeGameMode(value);
		this.UpdateAllSettingsViews();
	}

	// Token: 0x06000548 RID: 1352 RVA: 0x00018C28 File Offset: 0x00016E28
	private void ChangeMap(int value)
	{
		this._hostGameScreenController.ChangeMap(value);
		this.UpdateAllSettingsViews();
		Kit_PvP_GameModeBase kit_PvP_GameModeBase = this._game.allPvpGameModes[this._hostGameScreenController.HostGameData.GameMode];
		this._mapViewer.ChangeMapImage(kit_PvP_GameModeBase.traditionalMaps[this._hostGameScreenController.HostGameData.Map].mapPicture);
	}

	// Token: 0x06000549 RID: 1353 RVA: 0x00018C8B File Offset: 0x00016E8B
	private void ChangeDuration(int value)
	{
		this._hostGameScreenController.ChangeDuration(value);
		this.UpdateAllSettingsViews();
	}

	// Token: 0x0600054A RID: 1354 RVA: 0x00018C9F File Offset: 0x00016E9F
	private void ChangePlayerLimit(int value)
	{
		this._hostGameScreenController.ChangePlayerLimit(value);
		this.UpdateAllSettingsViews();
	}

	// Token: 0x0600054B RID: 1355 RVA: 0x00018CB3 File Offset: 0x00016EB3
	private void ChangePlayerNeeded(int value)
	{
		this._hostGameScreenController.ChangePlayerNeeded(value);
		this.UpdateAllSettingsViews();
	}

	// Token: 0x0600054C RID: 1356 RVA: 0x00018CC7 File Offset: 0x00016EC7
	private void ChangePingLimit(int value)
	{
		this._hostGameScreenController.ChangePingLimit(value);
		this.UpdateAllSettingsViews();
	}

	// Token: 0x0600054D RID: 1357 RVA: 0x00018CDB File Offset: 0x00016EDB
	private void ChangeAFKLimit(int value)
	{
		this._hostGameScreenController.ChangeAFKLimit(value);
		this.UpdateAllSettingsViews();
	}

	// Token: 0x0600054E RID: 1358 RVA: 0x00018CEF File Offset: 0x00016EEF
	private void ChangeBots(int value)
	{
		this._hostGameScreenController.ChangeBots(value);
		this.UpdateAllSettingsViews();
	}

	// Token: 0x0600054F RID: 1359 RVA: 0x00018D03 File Offset: 0x00016F03
	private void ChangeOnlineMode(int value)
	{
		this._hostGameScreenController.ChangeOnlineMode(value);
		this.UpdateAllSettingsViews();
	}

	// Token: 0x06000550 RID: 1360 RVA: 0x00018D17 File Offset: 0x00016F17
	private void ChangeRoundCount(int value)
	{
		this._hostGameScreenController.ChangeRoundCount(value);
		this.UpdateAllSettingsViews();
	}

	// Token: 0x040004A9 RID: 1193
	[SerializeField]
	private TMP_InputField _nameField;

	// Token: 0x040004AA RID: 1194
	[SerializeField]
	private TMP_InputField _passwordField;

	// Token: 0x040004AB RID: 1195
	[SerializeField]
	private HostSwitchableSettingView _gameModeView;

	// Token: 0x040004AC RID: 1196
	[SerializeField]
	private HostSwitchableSettingView _mapView;

	// Token: 0x040004AD RID: 1197
	[SerializeField]
	private HostSwitchableSettingView _durationView;

	// Token: 0x040004AE RID: 1198
	[SerializeField]
	private HostSwitchableSettingView _playersLimitView;

	// Token: 0x040004AF RID: 1199
	[SerializeField]
	private HostSwitchableSettingView _playersNeededView;

	// Token: 0x040004B0 RID: 1200
	[SerializeField]
	private HostSwitchableSettingView _pingLimitView;

	// Token: 0x040004B1 RID: 1201
	[SerializeField]
	private HostSwitchableSettingView _afkLimitView;

	// Token: 0x040004B2 RID: 1202
	[SerializeField]
	private HostSwitchableSettingView _botsView;

	// Token: 0x040004B3 RID: 1203
	[SerializeField]
	private HostSwitchableSettingView _connectivityView;

	// Token: 0x040004B4 RID: 1204
	[SerializeField]
	private HostSwitchableSettingView _roundCountView;

	// Token: 0x040004B5 RID: 1205
	[SerializeField]
	private MapViewer _mapViewer;

	// Token: 0x040004B6 RID: 1206
	[SerializeField]
	private TextMeshProUGUI _gameModeDiscriptionText;

	// Token: 0x040004B7 RID: 1207
	private HostGameScreenController _hostGameScreenController;

	// Token: 0x040004B8 RID: 1208
	private Kit_GameInformation _game;
}
﻿using System;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000D6 RID: 214
public class GrenadeCrosshairView : MonoBehaviour
{
	// Token: 0x0600053A RID: 1338 RVA: 0x0001807E File Offset: 0x0001627E
	public void HideCrosshair()
	{
		if (base.gameObject.activeInHierarchy)
		{
			base.gameObject.SetActive(false);
		}
	}

	// Token: 0x0600053B RID: 1339 RVA: 0x0001809C File Offset: 0x0001629C
	public void ShowCrosshair(bool isCloseThrow)
	{
		if (!base.gameObject.activeInHierarchy)
		{
			base.gameObject.SetActive(true);
		}
		this._crosshairUpperPart.color = (isCloseThrow ? this._deselectedColor : this._selectedColor);
		this._crosshairDownPart.color = (isCloseThrow ? this._selectedColor : this._deselectedColor);
	}

	// Token: 0x040004A1 RID: 1185
	[SerializeField]
	private Image _crosshairUpperPart;

	// Token: 0x040004A2 RID: 1186
	[SerializeField]
	private Image _crosshairDownPart;

	// Token: 0x040004A3 RID: 1187
	[SerializeField]
	private Color _selectedColor;

	// Token: 0x040004A4 RID: 1188
	[SerializeField]
	private Color _deselectedColor;
}
﻿using System;

// Token: 0x02000036 RID: 54
public class GrenadeKill : Achiwments
{
	// Token: 0x0600014E RID: 334 RVA: 0x00007825 File Offset: 0x00005A25
	protected override void SaveStats()
	{
		this.killProcess.main.gameInformation.statistics.OnKillByExplosion(this.killProcess.main);
	}
}
﻿using System;
using UnityEngine;

// Token: 0x0200001F RID: 31
public class GunAim : MonoBehaviour
{
	// Token: 0x060000E2 RID: 226 RVA: 0x000059D7 File Offset: 0x00003BD7
	private void Start()
	{
		this.parentCamera = base.GetComponentInParent<Camera>();
	}

	// Token: 0x060000E3 RID: 227 RVA: 0x000059E8 File Offset: 0x00003BE8
	private void Update()
	{
		float x = Input.mousePosition.x;
		float y = Input.mousePosition.y;
		if (x <= (float)this.borderLeft || x >= (float)(Screen.width - this.borderRight) || y <= (float)this.borderBottom || y >= (float)(Screen.height - this.borderTop))
		{
			this.isOutOfBounds = true;
		}
		else
		{
			this.isOutOfBounds = false;
		}
		if (!this.isOutOfBounds)
		{
			base.transform.LookAt(this.parentCamera.ScreenToWorldPoint(new Vector3(x, y, 5f)));
		}
	}

	// Token: 0x060000E4 RID: 228 RVA: 0x00005A79 File Offset: 0x00003C79
	public bool GetIsOutOfBounds()
	{
		return this.isOutOfBounds;
	}

	// Token: 0x0400006C RID: 108
	public int borderLeft;

	// Token: 0x0400006D RID: 109
	public int borderRight;

	// Token: 0x0400006E RID: 110
	public int borderTop;

	// Token: 0x0400006F RID: 111
	public int borderBottom;

	// Token: 0x04000070 RID: 112
	private Camera parentCamera;

	// Token: 0x04000071 RID: 113
	private bool isOutOfBounds;
}
﻿using System;
using UnityEngine;

// Token: 0x02000020 RID: 32
public class GunShoot : MonoBehaviour
{
	// Token: 0x060000E6 RID: 230 RVA: 0x00005A81 File Offset: 0x00003C81
	private void Start()
	{
		this.anim = base.GetComponent<Animator>();
		this.gunAim = base.GetComponentInParent<GunAim>();
	}

	// Token: 0x060000E7 RID: 231 RVA: 0x00005A9C File Offset: 0x00003C9C
	private void Update()
	{
		if (Input.GetButtonDown("Fire1") && Time.time > this.nextFire && !this.gunAim.GetIsOutOfBounds())
		{
			this.nextFire = Time.time + this.fireRate;
			this.muzzleFlash.Play();
			this.cartridgeEjection.Play();
			this.anim.SetTrigger("Fire");
			RaycastHit hit;
			if (Physics.Raycast(this.gunEnd.position, this.gunEnd.forward, out hit, this.weaponRange))
			{
				this.HandleHit(hit);
			}
		}
	}

	// Token: 0x060000E8 RID: 232 RVA: 0x00005B34 File Offset: 0x00003D34
	private void HandleHit(RaycastHit hit)
	{
		if (hit.collider.sharedMaterial != null)
		{
			string name = hit.collider.sharedMaterial.name;
			uint num = <PrivateImplementationDetails>.ComputeStringHash(name);
			if (num <= 1044434307U)
			{
				if (num <= 329707512U)
				{
					if (num != 81868168U)
					{
						if (num != 329707512U)
						{
							return;
						}
						if (!(name == "WaterFilledExtinguish"))
						{
							return;
						}
						this.SpawnDecal(hit, this.waterLeakExtinguishEffect);
						this.SpawnDecal(hit, this.metalHitEffect);
					}
					else
					{
						if (!(name == "Wood"))
						{
							return;
						}
						this.SpawnDecal(hit, this.woodHitEffect);
						return;
					}
				}
				else if (num != 970575400U)
				{
					if (num != 1044434307U)
					{
						return;
					}
					if (!(name == "Sand"))
					{
						return;
					}
					this.SpawnDecal(hit, this.sandHitEffect);
					return;
				}
				else
				{
					if (!(name == "WaterFilled"))
					{
						return;
					}
					this.SpawnDecal(hit, this.waterLeakEffect);
					this.SpawnDecal(hit, this.metalHitEffect);
					return;
				}
			}
			else if (num <= 2840670588U)
			{
				if (num != 1842662042U)
				{
					if (num != 2840670588U)
					{
						return;
					}
					if (!(name == "Metal"))
					{
						return;
					}
					this.SpawnDecal(hit, this.metalHitEffect);
					return;
				}
				else
				{
					if (!(name == "Stone"))
					{
						return;
					}
					this.SpawnDecal(hit, this.stoneHitEffect);
					return;
				}
			}
			else if (num != 3966976176U)
			{
				if (num != 4022181330U)
				{
					return;
				}
				if (!(name == "Meat"))
				{
					return;
				}
				this.SpawnDecal(hit, this.fleshHitEffects[UnityEngine.Random.Range(0, this.fleshHitEffects.Length)]);
				return;
			}
			else
			{
				if (!(name == "Character"))
				{
					return;
				}
				this.SpawnDecal(hit, this.fleshHitEffects[UnityEngine.Random.Range(0, this.fleshHitEffects.Length)]);
				return;
			}
		}
	}

	// Token: 0x060000E9 RID: 233 RVA: 0x00005CED File Offset: 0x00003EED
	private void SpawnDecal(RaycastHit hit, GameObject prefab)
	{
		UnityEngine.Object.Instantiate<GameObject>(prefab, hit.point, Quaternion.LookRotation(hit.normal)).transform.SetParent(hit.collider.transform);
	}

	// Token: 0x04000072 RID: 114
	public float fireRate = 0.25f;

	// Token: 0x04000073 RID: 115
	public float weaponRange = 20f;

	// Token: 0x04000074 RID: 116
	public Transform gunEnd;

	// Token: 0x04000075 RID: 117
	public ParticleSystem muzzleFlash;

	// Token: 0x04000076 RID: 118
	public ParticleSystem cartridgeEjection;

	// Token: 0x04000077 RID: 119
	public GameObject metalHitEffect;

	// Token: 0x04000078 RID: 120
	public GameObject sandHitEffect;

	// Token: 0x04000079 RID: 121
	public GameObject stoneHitEffect;

	// Token: 0x0400007A RID: 122
	public GameObject waterLeakEffect;

	// Token: 0x0400007B RID: 123
	public GameObject waterLeakExtinguishEffect;

	// Token: 0x0400007C RID: 124
	public GameObject[] fleshHitEffects;

	// Token: 0x0400007D RID: 125
	public GameObject woodHitEffect;

	// Token: 0x0400007E RID: 126
	private float nextFire;

	// Token: 0x0400007F RID: 127
	private Animator anim;

	// Token: 0x04000080 RID: 128
	private GunAim gunAim;
}
﻿using System;
using UnityEngine;

// Token: 0x02000037 RID: 55
public class HeadShotKill : Achiwments
{
	// Token: 0x06000150 RID: 336 RVA: 0x0000784C File Offset: 0x00005A4C
	protected override void SaveStats()
	{
		this.killProcess.main.gameInformation.statistics.OnHeadShot(this.killProcess.main);
		this.sfx_source.PlayOneShot(this.sfx);
	}

	// Token: 0x040000BC RID: 188
	[SerializeField]
	private float _flashBangDuration;

	// Token: 0x040000BD RID: 189
	public AudioClip sfx;

	// Token: 0x040000BE RID: 190
	public AudioSource sfx_source;
}
﻿using System;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000107 RID: 263
public class HighlightMenuButtonView : MonoBehaviour
{
	// Token: 0x060006B2 RID: 1714 RVA: 0x0001F938 File Offset: 0x0001DB38
	public void SetOn(bool on)
	{
		if (on)
		{
			this._background.color = this._highlightBackgroundColor;
			this._labalText.color = this._highlightTextColor;
			this._markImage.gameObject.SetActive(true);
			return;
		}
		this._background.color = this._defaultBackgroundColor;
		this._labalText.color = this._defaultTextColor;
		this._markImage.gameObject.SetActive(false);
	}

	// Token: 0x040005EE RID: 1518
	[SerializeField]
	private Image _background;

	// Token: 0x040005EF RID: 1519
	[SerializeField]
	private TextMeshProUGUI _labalText;

	// Token: 0x040005F0 RID: 1520
	[SerializeField]
	private Image _markImage;

	// Token: 0x040005F1 RID: 1521
	[SerializeField]
	private Color _defaultBackgroundColor;

	// Token: 0x040005F2 RID: 1522
	[SerializeField]
	private Color _highlightBackgroundColor;

	// Token: 0x040005F3 RID: 1523
	[SerializeField]
	private Color _defaultTextColor;

	// Token: 0x040005F4 RID: 1524
	[SerializeField]
	private Color _highlightTextColor;
}
﻿using System;
using UnityEngine;

// Token: 0x020000D8 RID: 216
public class HintableObject : MonoBehaviour
{
	// Token: 0x040004A8 RID: 1192
	public string Message;
}
﻿using System;
using System.Collections;
using System.Collections.Generic;
using ExitGames.Client.Photon;
using MarsFPSKit;
using Photon.Pun;
using Photon.Realtime;
using UnityEngine;
using UnityEngine.EventSystems;

// Token: 0x02000052 RID: 82
public class HostGameController : IMatchmakingCallbacks
{
	// Token: 0x17000015 RID: 21
	// (get) Token: 0x060001F5 RID: 501 RVA: 0x0000A8CF File Offset: 0x00008ACF
	public static HostGameController Instance
	{
		get
		{
			if (HostGameController._instance == null)
			{
				HostGameController._instance = new HostGameController();
			}
			return HostGameController._instance;
		}
	}

	// Token: 0x060001F6 RID: 502 RVA: 0x0000A8E7 File Offset: 0x00008AE7
	private HostGameController()
	{
		this._game = Resources.Load<Kit_GameInformation>("Game");
		PhotonNetwork.AddCallbackTarget(this);
	}

	// Token: 0x060001F7 RID: 503 RVA: 0x0000A905 File Offset: 0x00008B05
	public void Initialize()
	{
		this._sceneDriver = UnityEngine.Object.FindObjectOfType<SceneDriver>();
		this._eventSystem = UnityEngine.Object.FindObjectOfType<EventSystem>();
		this._sceneDriver.OnUpdate += this.Loging;
	}

	// Token: 0x060001F8 RID: 504 RVA: 0x0000A934 File Offset: 0x00008B34
	public void StartSession(HostGameData hostGameData)
	{
		this._hostGameData = hostGameData;
		this._sceneDriver.StartCoroutine(this.StartSessionRoutine());
	}

	// Token: 0x060001F9 RID: 505 RVA: 0x0000A94F File Offset: 0x00008B4F
	public IEnumerator StartSessionRoutine()
	{
		Kit_GameSettings.currentNetworkingMode = KitNetworkingMode.Traditional;
		if (this._hostGameData.OnlineMode == 0)
		{
			if (PhotonNetwork.IsConnected)
			{
				if (!this._hostGameData.NameFieldText.IsNullOrWhiteSpace())
				{
					RoomOptions roomOptions = new RoomOptions();
					roomOptions.IsVisible = true;
					roomOptions.IsOpen = true;
					roomOptions.MaxPlayers = this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPlayerLimits[this._hostGameData.PlayerLimit];
					roomOptions.CustomRoomProperties = new ExitGames.Client.Photon.Hashtable();
					roomOptions.CustomRoomProperties.Add("lobby", false);
					roomOptions.CustomRoomProperties.Add("map", this._hostGameData.Map);
					roomOptions.CustomRoomProperties.Add("gameMode", this._hostGameData.GameMode);
					roomOptions.CustomRoomProperties.Add("duration", this._hostGameData.Duration);
					roomOptions.CustomRoomProperties.Add("ping", this._hostGameData.PingLimit);
					roomOptions.CustomRoomProperties.Add("afk", this._hostGameData.AFKLimit);
					roomOptions.CustomRoomProperties.Add("bots", this._hostGameData.BotsMode == 1);
					roomOptions.CustomRoomProperties.Add("password", this._hostGameData.PasswordFieldText);
					roomOptions.CustomRoomProperties.Add("playerNeeded", this._hostGameData.PlayerNeeded);
					roomOptions.CustomRoomProperties.Add("roundCount", this._hostGameData.RoundCount);
					roomOptions.CustomRoomPropertiesForLobby = new string[]
					{
						"lobby",
						"map",
						"gameMode",
						"duration",
						"bots",
						"password"
					};
					PhotonNetwork.OfflineMode = false;
					if (PhotonNetwork.CreateRoom(this._hostGameData.NameFieldText, roomOptions, null, null))
					{
					}
				}
			}
			else
			{
				RegionController.Instance.GameStartedNotConnected();
			}
		}
		else if (!this._hostGameData.NameFieldText.IsNullOrWhiteSpace())
		{
			if (PhotonNetwork.IsConnected)
			{
				PhotonNetwork.Disconnect();
			}
			while (PhotonNetwork.IsConnected)
			{
				yield return null;
			}
			PhotonNetwork.OfflineMode = true;
			RoomOptions roomOptions2 = new RoomOptions();
			roomOptions2.IsVisible = true;
			roomOptions2.IsOpen = true;
			roomOptions2.MaxPlayers = this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPlayerLimits[this._hostGameData.PlayerLimit];
			roomOptions2.CustomRoomProperties = new ExitGames.Client.Photon.Hashtable();
			roomOptions2.CustomRoomProperties.Add("lobby", false);
			roomOptions2.CustomRoomProperties.Add("map", this._hostGameData.Map);
			roomOptions2.CustomRoomProperties.Add("gameMode", this._hostGameData.GameMode);
			roomOptions2.CustomRoomProperties.Add("duration", this._hostGameData.Duration);
			roomOptions2.CustomRoomProperties.Add("ping", this._hostGameData.PingLimit);
			roomOptions2.CustomRoomProperties.Add("afk", this._hostGameData.AFKLimit);
			roomOptions2.CustomRoomProperties.Add("bots", this._hostGameData.BotsMode == 1);
			roomOptions2.CustomRoomProperties.Add("password", this._hostGameData.PasswordFieldText);
			roomOptions2.CustomRoomProperties.Add("playerNeeded", this._hostGameData.PlayerNeeded);
			roomOptions2.CustomRoomProperties.Add("roundCount", this._hostGameData.RoundCount);
			roomOptions2.CustomRoomPropertiesForLobby = new string[]
			{
				"lobby",
				"map",
				"gameMode",
				"duration",
				"bots",
				"password"
			};
			if (PhotonNetwork.CreateRoom(this._hostGameData.NameFieldText, roomOptions2, null, SteamFriendsController.Instance.GetExpectedUsers()))
			{
				SteamFriendsController.Instance.OnMasterClientJoinRoom(this._hostGameData.NameFieldText);
			}
		}
		yield break;
	}

	// Token: 0x060001FA RID: 506 RVA: 0x0000A95E File Offset: 0x00008B5E
	public void Loging()
	{
		if (this._lastPhotonState != PhotonNetwork.NetworkClientState)
		{
			Debug.Log(string.Format("Photon state change from: {0} to: {1}", this._lastPhotonState, PhotonNetwork.NetworkClientState));
			this._lastPhotonState = PhotonNetwork.NetworkClientState;
		}
	}

	// Token: 0x060001FB RID: 507 RVA: 0x0000A99C File Offset: 0x00008B9C
	public void StartTournament(HostGameData hostGameData, string[] expectedUsers, string roomName, byte maxPlayersCount)
	{
		this._hostGameData = hostGameData;
		Kit_GameSettings.currentNetworkingMode = KitNetworkingMode.Traditional;
		if (PhotonNetwork.IsConnected)
		{
			if (!roomName.IsNullOrWhiteSpace())
			{
				RoomOptions roomOptions = new RoomOptions();
				roomOptions.IsVisible = false;
				roomOptions.IsOpen = true;
				roomOptions.MaxPlayers = maxPlayersCount;
				roomOptions.CustomRoomProperties = new ExitGames.Client.Photon.Hashtable();
				roomOptions.CustomRoomProperties.Add("lobby", false);
				roomOptions.CustomRoomProperties.Add("map", this._hostGameData.Map);
				roomOptions.CustomRoomProperties.Add("gameMode", this._hostGameData.GameMode);
				roomOptions.CustomRoomProperties.Add("duration", this._hostGameData.Duration);
				roomOptions.CustomRoomProperties.Add("ping", this._hostGameData.PingLimit);
				roomOptions.CustomRoomProperties.Add("afk", this._hostGameData.AFKLimit);
				roomOptions.CustomRoomProperties.Add("bots", this._hostGameData.BotsMode == 1);
				roomOptions.CustomRoomProperties.Add("password", this._hostGameData.PasswordFieldText);
				roomOptions.CustomRoomProperties.Add("playerNeeded", this._hostGameData.PlayerNeeded);
				roomOptions.CustomRoomProperties.Add("roundCount", this._hostGameData.RoundCount);
				roomOptions.CustomRoomPropertiesForLobby = new string[]
				{
					"lobby",
					"map",
					"gameMode",
					"duration",
					"bots",
					"password"
				};
				PhotonNetwork.OfflineMode = false;
				PhotonNetwork.JoinOrCreateRoom(roomName, roomOptions, null, expectedUsers);
				return;
			}
		}
		else
		{
			RegionController.Instance.GameStartedNotConnected();
		}
	}

	// Token: 0x060001FC RID: 508 RVA: 0x0000AB7F File Offset: 0x00008D7F
	public void JoinRandomRoom()
	{
		PhotonNetwork.JoinRandomRoom();
	}

	// Token: 0x060001FD RID: 509 RVA: 0x0000AB88 File Offset: 0x00008D88
	private HostGameData GetRandomHostGameData()
	{
		string nameFieldText = string.Format("Room {0}", UnityEngine.Random.Range(0, 1000));
		int num = UnityEngine.Random.Range(0, this._game.allPvpGameModes.Length);
		int map = UnityEngine.Random.Range(0, this._game.allPvpGameModes[num].traditionalMaps.Length);
		int playerLimit = UnityEngine.Random.Range(0, this._game.allPvpGameModes[num].traditionalPlayerLimits.Length);
		int duration = UnityEngine.Random.Range(0, this._game.allPvpGameModes[num].traditionalDurations.Length);
		int roundCount = 0;
		if (this._game.allPvpGameModes[num] is IRounds)
		{
			IRounds rounds = (IRounds)this._game.allPvpGameModes[num];
			roundCount = UnityEngine.Random.Range(0, rounds.Rounds.Length);
		}
		this._hostGameData = new HostGameData
		{
			NameFieldText = nameFieldText,
			GameMode = num,
			Map = map,
			BotsMode = 1,
			OnlineMode = 0,
			PlayerLimit = playerLimit,
			PlayerNeeded = 0,
			Duration = duration,
			AFKLimit = 0,
			PingLimit = 0,
			RoundCount = roundCount
		};
		return this._hostGameData;
	}

	// Token: 0x060001FE RID: 510 RVA: 0x00005F78 File Offset: 0x00004178
	public void OnFriendListUpdate(List<FriendInfo> friendList)
	{
	}

	// Token: 0x060001FF RID: 511 RVA: 0x0000ACB4 File Offset: 0x00008EB4
	public void OnCreatedRoom()
	{
		ExitGames.Client.Photon.Hashtable customProperties = PhotonNetwork.CurrentRoom.CustomProperties;
		if (!(bool)customProperties["lobby"])
		{
			int num = (int)customProperties["map"];
			if (this._eventSystem)
			{
				this._eventSystem.enabled = false;
			}
			Kit_SceneSyncer.instance.LoadScene(this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalMaps[num].sceneName);
			if (TournamentsController.Instance.IsItTournament)
			{
				TournamentsController.Instance.SetMatchStarted();
			}
		}
	}

	// Token: 0x06000200 RID: 512 RVA: 0x00005F78 File Offset: 0x00004178
	public void OnCreateRoomFailed(short returnCode, string message)
	{
	}

	// Token: 0x06000201 RID: 513 RVA: 0x00005F78 File Offset: 0x00004178
	public void OnJoinedRoom()
	{
	}

	// Token: 0x06000202 RID: 514 RVA: 0x00005F78 File Offset: 0x00004178
	public void OnJoinRoomFailed(short returnCode, string message)
	{
	}

	// Token: 0x06000203 RID: 515 RVA: 0x00005F78 File Offset: 0x00004178
	public void OnJoinRandomFailed(short returnCode, string message)
	{
	}

	// Token: 0x06000204 RID: 516 RVA: 0x00005F78 File Offset: 0x00004178
	public void OnLeftRoom()
	{
	}

	// Token: 0x04000177 RID: 375
	private static HostGameController _instance;

	// Token: 0x04000178 RID: 376
	private EventSystem _eventSystem;

	// Token: 0x04000179 RID: 377
	private Kit_GameInformation _game;

	// Token: 0x0400017A RID: 378
	private SceneDriver _sceneDriver;

	// Token: 0x0400017B RID: 379
	private ClientState _lastPhotonState;

	// Token: 0x0400017C RID: 380
	private HostGameData _hostGameData;
}
﻿using System;

// Token: 0x0200007F RID: 127
[Serializable]
public class HostGameData
{
	// Token: 0x04000266 RID: 614
	public string NameFieldText;

	// Token: 0x04000267 RID: 615
	public string PasswordFieldText = "";

	// Token: 0x04000268 RID: 616
	public int GameMode;

	// Token: 0x04000269 RID: 617
	public int Map;

	// Token: 0x0400026A RID: 618
	public int Duration;

	// Token: 0x0400026B RID: 619
	public int PlayerLimit;

	// Token: 0x0400026C RID: 620
	public int PlayerNeeded;

	// Token: 0x0400026D RID: 621
	public int PingLimit;

	// Token: 0x0400026E RID: 622
	public int AFKLimit;

	// Token: 0x0400026F RID: 623
	public int BotsMode;

	// Token: 0x04000270 RID: 624
	public int OnlineMode = 1;

	// Token: 0x04000271 RID: 625
	public int RoundCount;
}
﻿using System;
using System.Collections.Generic;
using System.Linq;
using MarsFPSKit;
using UnityEngine;

// Token: 0x02000053 RID: 83
public class HostGameScreenController
{
	// Token: 0x17000016 RID: 22
	// (get) Token: 0x06000205 RID: 517 RVA: 0x0000AD4B File Offset: 0x00008F4B
	public HostGameData HostGameData
	{
		get
		{
			return this._hostGameData;
		}
	}

	// Token: 0x06000206 RID: 518 RVA: 0x0000AD54 File Offset: 0x00008F54
	public HostGameScreenController(Kit_GameInformation game, List<int> gameModeFilter, bool randomRoomName = false)
	{
		this._game = game;
		this._hostGameData = new HostGameData();
		if (randomRoomName)
		{
			this._hostGameData.NameFieldText = string.Format("Room ({0})", UnityEngine.Random.Range(0, 1000));
		}
		this._gameModeFilter = gameModeFilter;
		this._hostGameData.GameMode = gameModeFilter.First<int>();
	}

	// Token: 0x06000207 RID: 519 RVA: 0x0000ADB9 File Offset: 0x00008FB9
	public void SetName(string name)
	{
		this._hostGameData.NameFieldText = name;
	}

	// Token: 0x06000208 RID: 520 RVA: 0x0000ADC7 File Offset: 0x00008FC7
	public void SetPassword(string password)
	{
		this._hostGameData.PasswordFieldText = password;
	}

	// Token: 0x06000209 RID: 521 RVA: 0x0000ADD8 File Offset: 0x00008FD8
	public void ChangeGameMode(int value)
	{
		int gameMode = this._hostGameData.GameMode;
		this._hostGameData.GameMode += value;
		int count = this._gameModeFilter.Count;
		if (!this._gameModeFilter.Contains(this._hostGameData.GameMode))
		{
			if (this._hostGameData.GameMode > this._gameModeFilter.Last<int>())
			{
				this._hostGameData.GameMode = this._gameModeFilter.First<int>();
			}
			else if (this._hostGameData.GameMode < this._gameModeFilter.First<int>())
			{
				this._hostGameData.GameMode = this._gameModeFilter.Last<int>();
			}
		}
		int num = this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalAfkLimits.Length;
		this._hostGameData.AFKLimit = ((this._hostGameData.AFKLimit >= num) ? (num - 1) : this._hostGameData.AFKLimit);
		num = this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalDurations.Length;
		this._hostGameData.Duration = ((this._hostGameData.Duration >= num) ? (num - 1) : this._hostGameData.Duration);
		num = this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPingLimits.Length;
		this._hostGameData.PingLimit = ((this._hostGameData.PingLimit >= num) ? (num - 1) : this._hostGameData.PingLimit);
		num = this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPlayerLimits.Length;
		this._hostGameData.PlayerLimit = ((this._hostGameData.PlayerLimit >= num) ? (num - 1) : this._hostGameData.PlayerLimit);
		num = this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPlayerNeeded.Length;
		this._hostGameData.PlayerNeeded = ((this._hostGameData.PlayerNeeded >= count) ? (num - 1) : this._hostGameData.PlayerNeeded);
		if (this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalMaps.Contains(this._game.allPvpGameModes[gameMode].traditionalMaps[this._hostGameData.Map]))
		{
			this._hostGameData.Map = Array.IndexOf<Kit_MapInformation>(this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalMaps, this._game.allPvpGameModes[gameMode].traditionalMaps[this._hostGameData.Map]);
			if (this._hostGameData.Map < 0)
			{
				this._hostGameData.Map = 0;
				return;
			}
		}
		else
		{
			this._hostGameData.Map = 0;
		}
	}

	// Token: 0x0600020A RID: 522 RVA: 0x0000B0A0 File Offset: 0x000092A0
	public void ChangeMap(int value)
	{
		this._hostGameData.Map += value;
		int num = this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalMaps.Length;
		if (this._hostGameData.Map >= num)
		{
			this._hostGameData.Map = 0;
			return;
		}
		if (this._hostGameData.Map < 0)
		{
			this._hostGameData.Map = num - 1;
		}
	}

	// Token: 0x0600020B RID: 523 RVA: 0x0000B118 File Offset: 0x00009318
	public void ChangeDuration(int value)
	{
		this._hostGameData.Duration += value;
		int num = this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalDurations.Length;
		if (this._hostGameData.Duration >= num)
		{
			this._hostGameData.Duration = 0;
			return;
		}
		if (this._hostGameData.Duration < 0)
		{
			this._hostGameData.Duration = num - 1;
		}
	}

	// Token: 0x0600020C RID: 524 RVA: 0x0000B190 File Offset: 0x00009390
	public void ChangePlayerLimit(int value)
	{
		this._hostGameData.PlayerLimit += value;
		int num = this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPlayerLimits.Length;
		if (this._hostGameData.PlayerLimit >= num)
		{
			this._hostGameData.PlayerLimit = 0;
		}
		else if (this._hostGameData.PlayerLimit < 0)
		{
			this._hostGameData.PlayerLimit = num - 1;
		}
		while (this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPlayerNeeded[this._hostGameData.PlayerNeeded] > (int)this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPlayerLimits[this._hostGameData.PlayerLimit] && this._hostGameData.PlayerNeeded > 0)
		{
			this._hostGameData.PlayerNeeded--;
		}
	}

	// Token: 0x0600020D RID: 525 RVA: 0x0000B27C File Offset: 0x0000947C
	public void ChangePlayerNeeded(int value)
	{
		this._hostGameData.PlayerNeeded += value;
		int num = this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPlayerNeeded.Length;
		if (this._hostGameData.PlayerNeeded >= num)
		{
			this._hostGameData.PlayerNeeded = 0;
		}
		else if (this._hostGameData.PlayerNeeded < 0)
		{
			this._hostGameData.PlayerNeeded = num - 1;
		}
		if (this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPlayerNeeded[this._hostGameData.PlayerNeeded] > (int)this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPlayerLimits[this._hostGameData.PlayerLimit])
		{
			this._hostGameData.PlayerNeeded = 0;
		}
		while (this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPlayerNeeded[this._hostGameData.PlayerNeeded] > (int)this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPlayerLimits[this._hostGameData.PlayerLimit] && this._hostGameData.PlayerNeeded > 0)
		{
			this._hostGameData.PlayerNeeded--;
		}
	}

	// Token: 0x0600020E RID: 526 RVA: 0x0000B3C8 File Offset: 0x000095C8
	public void ChangePingLimit(int value)
	{
		this._hostGameData.PingLimit += value;
		int num = this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalPingLimits.Length;
		if (this._hostGameData.PingLimit >= num)
		{
			this._hostGameData.PingLimit = 0;
			return;
		}
		if (this._hostGameData.PingLimit < 0)
		{
			this._hostGameData.PingLimit = num - 1;
		}
	}

	// Token: 0x0600020F RID: 527 RVA: 0x0000B440 File Offset: 0x00009640
	public void ChangeAFKLimit(int value)
	{
		this._hostGameData.AFKLimit += value;
		int num = this._game.allPvpGameModes[this._hostGameData.GameMode].traditionalAfkLimits.Length;
		if (this._hostGameData.AFKLimit >= num)
		{
			this._hostGameData.AFKLimit = 0;
			return;
		}
		if (this._hostGameData.AFKLimit < 0)
		{
			this._hostGameData.AFKLimit = num - 1;
		}
	}

	// Token: 0x06000210 RID: 528 RVA: 0x0000B4B8 File Offset: 0x000096B8
	public void ChangeBots(int value)
	{
		this._hostGameData.BotsMode += value;
		int num = 2;
		if (this._hostGameData.BotsMode >= num)
		{
			this._hostGameData.BotsMode = 0;
			return;
		}
		if (this._hostGameData.BotsMode < 0)
		{
			this._hostGameData.BotsMode = num - 1;
		}
	}

	// Token: 0x06000211 RID: 529 RVA: 0x0000B514 File Offset: 0x00009714
	public void ChangeOnlineMode(int value)
	{
		this._hostGameData.OnlineMode += value;
		int num = 2;
		if (this._hostGameData.OnlineMode >= num)
		{
			this._hostGameData.OnlineMode = 0;
			return;
		}
		if (this._hostGameData.OnlineMode < 0)
		{
			this._hostGameData.OnlineMode = num - 1;
		}
	}

	// Token: 0x06000212 RID: 530 RVA: 0x0000B570 File Offset: 0x00009770
	public void ChangeRoundCount(int value)
	{
		this._hostGameData.RoundCount += value;
		IRounds rounds = (IRounds)this._game.allPvpGameModes[this._hostGameData.GameMode];
		if (rounds != null)
		{
			int num = rounds.Rounds.Length;
			if (this._hostGameData.RoundCount >= num)
			{
				this._hostGameData.RoundCount = 0;
				return;
			}
			if (this._hostGameData.RoundCount < 0)
			{
				this._hostGameData.RoundCount = num - 1;
			}
		}
	}

	// Token: 0x0400017D RID: 381
	private Kit_GameInformation _game;

	// Token: 0x0400017E RID: 382
	private HostGameData _hostGameData;

	// Token: 0x0400017F RID: 383
	private List<int> _gameModeFilter;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000DA RID: 218
public class HostGameScreenView : BaseMenuView
{
	// Token: 0x06000568 RID: 1384 RVA: 0x00018DFB File Offset: 0x00016FFB
	private void OnEnable()
	{
		this._backButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(this._previousMenu);
		});
		this._createRoomButton.onClick.AddListener(delegate()
		{
			HostGameController.Instance.StartSession(this._hostGameScreenController.HostGameData);
		});
	}

	// Token: 0x06000569 RID: 1385 RVA: 0x00018E35 File Offset: 0x00017035
	private void OnDisable()
	{
		this._backButton.onClick.RemoveAllListeners();
		this._createRoomButton.onClick.RemoveAllListeners();
	}

	// Token: 0x0600056A RID: 1386 RVA: 0x00018E58 File Offset: 0x00017058
	public override void MenuInit(ISwitchableMenu menuController)
	{
		base.MenuInit(menuController);
		this._game = Resources.Load<Kit_GameInformation>("Game");
		if (this._hostGameScreenController == null)
		{
			this._hostGameScreenController = new HostGameScreenController(this._game, this.GetAllGaemModes(), true);
		}
		this._gameSettingsView.ViewInit(this._hostGameScreenController, this._game);
	}

	// Token: 0x0600056B RID: 1387 RVA: 0x00018EB4 File Offset: 0x000170B4
	public List<int> GetAllGaemModes()
	{
		List<int> list = new List<int>();
		Kit_PvP_GameModeBase[] allPvpGameModes = this._game.allPvpGameModes;
		for (int i = 0; i < allPvpGameModes.Length; i++)
		{
			list.Add(i);
		}
		return list;
	}

	// Token: 0x040004B9 RID: 1209
	[SerializeField]
	private Button _backButton;

	// Token: 0x040004BA RID: 1210
	[SerializeField]
	private Button _createRoomButton;

	// Token: 0x040004BB RID: 1211
	[SerializeField]
	private GameSettingsView _gameSettingsView;

	// Token: 0x040004BC RID: 1212
	private Kit_GameInformation _game;

	// Token: 0x040004BD RID: 1213
	private HostGameScreenController _hostGameScreenController;
}
﻿using System;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000DB RID: 219
public class HostSwitchableSettingView : MonoBehaviour
{
	// Token: 0x0600056F RID: 1391 RVA: 0x00018F00 File Offset: 0x00017100
	private void OnEnable()
	{
		this._leftButton.onClick.AddListener(delegate()
		{
			this._onLeftButtonClick();
		});
		this._rightButton.onClick.AddListener(delegate()
		{
			this._onRightButtonClick();
		});
	}

	// Token: 0x06000570 RID: 1392 RVA: 0x00018F3A File Offset: 0x0001713A
	private void OnDisable()
	{
		this._leftButton.onClick.RemoveAllListeners();
		this._rightButton.onClick.RemoveAllListeners();
	}

	// Token: 0x06000571 RID: 1393 RVA: 0x00018F5C File Offset: 0x0001715C
	public void ViewInit(Action onLetButtonClick, Action onRightButtonClick)
	{
		this._onLeftButtonClick = onLetButtonClick;
		this._onRightButtonClick = onRightButtonClick;
	}

	// Token: 0x06000572 RID: 1394 RVA: 0x00018F6C File Offset: 0x0001716C
	public void UpdateValue(string valueText)
	{
		this._settingsValueText.text = valueText;
	}

	// Token: 0x040004BE RID: 1214
	[SerializeField]
	private Button _leftButton;

	// Token: 0x040004BF RID: 1215
	[SerializeField]
	private TextMeshProUGUI _settingsValueText;

	// Token: 0x040004C0 RID: 1216
	[SerializeField]
	private Button _rightButton;

	// Token: 0x040004C1 RID: 1217
	private Action _onLeftButtonClick = delegate()
	{
	};

	// Token: 0x040004C2 RID: 1218
	private Action _onRightButtonClick = delegate()
	{
	};
}
﻿using System;

// Token: 0x02000075 RID: 117
internal interface IAccount
{
	// Token: 0x1700002F RID: 47
	// (get) Token: 0x0600032D RID: 813
	// (set) Token: 0x0600032E RID: 814
	AccountModel Account { get; set; }
}
﻿using System;

// Token: 0x02000076 RID: 118
public interface IActiveObject
{
	// Token: 0x17000030 RID: 48
	// (get) Token: 0x0600032F RID: 815
	bool IsActive { get; }
}
﻿using System;

// Token: 0x02000077 RID: 119
public interface IChangableSIdeHUD
{
	// Token: 0x14000008 RID: 8
	// (add) Token: 0x06000330 RID: 816
	// (remove) Token: 0x06000331 RID: 817
	event Action OnChangeSides;
}
﻿using System;

// Token: 0x02000078 RID: 120
public interface IChangableSpawn
{
	// Token: 0x06000332 RID: 818
	void ChangeTeamSpawnConditions();
}
﻿using System;
using MarsFPSKit;
using Steamworks;
using UnityEngine;

// Token: 0x02000054 RID: 84
public class InGameMenuController : ISwitchableMenu
{
	// Token: 0x17000017 RID: 23
	// (get) Token: 0x06000213 RID: 531 RVA: 0x0000B5F0 File Offset: 0x000097F0
	public MainMenuData MainMenuData { get; } = new MainMenuData();

	// Token: 0x06000214 RID: 532 RVA: 0x0000B5F8 File Offset: 0x000097F8
	public InGameMenuController(BaseMenuView[] menues)
	{
		this._menues = menues;
		this._main = UnityEngine.Object.FindObjectOfType<Kit_IngameMain>();
		this._waitProcess = new WaitProcess();
		this._waitLockCursor = new WaitProcess();
		this.SetInit();
	}

	// Token: 0x06000215 RID: 533 RVA: 0x0000B65C File Offset: 0x0000985C
	public void OnEscapeButtonClick()
	{
		if (this._currentMenu)
		{
			if (this._currentMenu.IsActive)
			{
				if (this._currentMenu.MenuType == MenuType.ChooseTeam)
				{
					return;
				}
				this._currentMenu.HideMenu(true);
				BaseMenuView baseMenuView = this.FindMenuByMenuType(this._currentMenu.PreviousMenu);
				if (baseMenuView)
				{
					baseMenuView.ShowMenu(true);
					this._currentMenu = baseMenuView;
				}
			}
			else
			{
				this.SwitchMenu(MenuType.Pause);
			}
		}
		else
		{
			this.SwitchMenu(MenuType.Pause);
		}
		this.MouseLocking();
	}

	// Token: 0x06000216 RID: 534 RVA: 0x0000B6DF File Offset: 0x000098DF
	private void SetInit()
	{
		this._game = Resources.Load<Kit_GameInformation>("Game");
		DataSender.Instance.Initialize();
		this.InitializeAllMenues();
		this.HideAllMenus();
		this.SetStartMenu();
	}

	// Token: 0x06000217 RID: 535 RVA: 0x0000B710 File Offset: 0x00009910
	private void InitializeAllMenues()
	{
		for (int i = 0; i < this._menues.Length; i++)
		{
			this._menues[i].MenuInit(this);
		}
	}

	// Token: 0x06000218 RID: 536 RVA: 0x0000B740 File Offset: 0x00009940
	private void HideAllMenus()
	{
		for (int i = 0; i < this._menues.Length; i++)
		{
			this._menues[i].HideMenu(false);
		}
	}

	// Token: 0x06000219 RID: 537 RVA: 0x0000B770 File Offset: 0x00009970
	private void SetStartMenu()
	{
		if (TournamentsController.Instance.IsItTournament)
		{
			this._waitProcess.StartProcess(delegate
			{
				TournamentMatch match = TournamentsController.Instance.Match;
				int playerTeam = this.GetPlayerTeam(match);
				this._main.JoinTeam(playerTeam);
				this._currentMenu = this.FindMenuByMenuType(MenuType.Pause);
				this.MainMenuData.IsTeamChangeble = false;
				MarsScreen.lockCursor = true;
			}, this._wait);
		}
		else
		{
			this._currentMenu = this.FindMenuByMenuType(MenuType.ChooseTeam);
			this._currentMenu.ShowMenu(false);
			this.MainMenuData.IsTeamChangeble = true;
		}
		if (this._main.currentGameModeBehaviour is IRounds)
		{
			this.MainMenuData.IsTeamChangeble = false;
			return;
		}
		this.MainMenuData.IsTeamChangeble = true;
	}

	// Token: 0x0600021A RID: 538 RVA: 0x0000B7FC File Offset: 0x000099FC
	private int GetPlayerTeam(TournamentMatch match)
	{
		TournamentTeam[] teams = match.Teams;
		ulong steamID = SteamUser.GetSteamID().m_SteamID;
		for (int i = 0; i < teams.Length; i++)
		{
			for (int j = 0; j < teams[i].Players.Length; j++)
			{
				if (teams[i].Players[j] == steamID)
				{
					return i;
				}
			}
		}
		return -1;
	}

	// Token: 0x0600021B RID: 539 RVA: 0x0000B850 File Offset: 0x00009A50
	private BaseMenuView FindMenuByMenuType(MenuType menuType)
	{
		BaseMenuView result = null;
		for (int i = 0; i < this._menues.Length; i++)
		{
			if (this._menues[i].MenuType == menuType)
			{
				result = this._menues[i];
				break;
			}
		}
		return result;
	}

	// Token: 0x0600021C RID: 540 RVA: 0x0000B890 File Offset: 0x00009A90
	private void MouseLocking()
	{
		if (this._currentMenu && this._currentMenu.IsActive)
		{
			if (this._currentMenu.MenuType == MenuType.Map)
			{
				MarsScreen.IsChangeble = true;
				MarsScreen.lockCursor = true;
				((Kit_SimpleChat)this._main.chat).CloseChat();
				return;
			}
			MarsScreen.lockCursor = false;
			MarsScreen.IsChangeble = false;
			return;
		}
		else
		{
			if (this._main.currentVictoryScreen || this._main.spectatorManager.IsCurrentlySpectating(this._main))
			{
				MarsScreen.lockCursor = false;
				return;
			}
			this._waitLockCursor.StartProcess(delegate
			{
				MarsScreen.IsChangeble = true;
				MarsScreen.lockCursor = true;
			}, this._waitLockCursorTime);
			return;
		}
	}

	// Token: 0x0600021D RID: 541 RVA: 0x0000B958 File Offset: 0x00009B58
	public void SwitchMenu(MenuType menu)
	{
		BaseMenuView baseMenuView = this.FindMenuByMenuType(menu);
		if (this._currentMenu)
		{
			if (baseMenuView == this._currentMenu)
			{
				if (this._currentMenu.IsActive)
				{
					this._currentMenu.HideMenu(true);
				}
				else
				{
					this._currentMenu.ShowMenu(true);
				}
			}
			else
			{
				if (this._currentMenu.IsActive)
				{
					this._currentMenu.HideMenu(true);
				}
				baseMenuView.ShowMenu(true);
				this._currentMenu = baseMenuView;
			}
		}
		else
		{
			baseMenuView.ShowMenu(true);
			this._currentMenu = baseMenuView;
		}
		this.MouseLocking();
	}

	// Token: 0x0600021E RID: 542 RVA: 0x0000B9F0 File Offset: 0x00009BF0
	public void SwitchToPreviousMenu()
	{
		MenuType previousMenu = this._currentMenu.PreviousMenu;
		if (previousMenu == MenuType.Hide)
		{
			this._currentMenu.HideMenu(true);
			return;
		}
		this.SwitchMenu(previousMenu);
	}

	// Token: 0x0600021F RID: 543 RVA: 0x0000BA22 File Offset: 0x00009C22
	public void HideCurrentMenu()
	{
		BaseMenuView currentMenu = this._currentMenu;
		if (currentMenu != null)
		{
			currentMenu.HideMenu(false);
		}
		this.MouseLocking();
	}

	// Token: 0x04000180 RID: 384
	private BaseMenuView[] _menues;

	// Token: 0x04000181 RID: 385
	private BaseMenuView _currentMenu;

	// Token: 0x04000182 RID: 386
	private Kit_GameInformation _game;

	// Token: 0x04000183 RID: 387
	private Kit_IngameMain _main;

	// Token: 0x04000184 RID: 388
	private WaitProcess _waitProcess;

	// Token: 0x04000185 RID: 389
	private float _wait = 3f;

	// Token: 0x04000186 RID: 390
	private WaitProcess _waitLockCursor;

	// Token: 0x04000187 RID: 391
	private float _waitLockCursorTime = 0.01f;
}
﻿using System;
using UnityEngine;

// Token: 0x02000055 RID: 85
public class InGameMenuMain : MonoBehaviour
{
	// Token: 0x17000018 RID: 24
	// (get) Token: 0x06000221 RID: 545 RVA: 0x0000BA87 File Offset: 0x00009C87
	public InGameMenuController InGameMenuController
	{
		get
		{
			return this._inGameMenuController;
		}
	}

	// Token: 0x06000222 RID: 546 RVA: 0x0000BA8F File Offset: 0x00009C8F
	private void Update()
	{
		this.Input();
	}

	// Token: 0x06000223 RID: 547 RVA: 0x0000BA97 File Offset: 0x00009C97
	public void Initialize()
	{
		this._inGameMenuController = new InGameMenuController(this._menues);
		this._switchableMenu = this._inGameMenuController;
	}

	// Token: 0x06000224 RID: 548 RVA: 0x0000BAB6 File Offset: 0x00009CB6
	public void ShowWeaponChooseScreen()
	{
		this._inGameMenuController.SwitchMenu(MenuType.Weapons);
	}

	// Token: 0x06000225 RID: 549 RVA: 0x0000BAC4 File Offset: 0x00009CC4
	public void ShowOptionsScreen()
	{
		this._inGameMenuController.SwitchMenu(MenuType.Options);
	}

	// Token: 0x06000226 RID: 550 RVA: 0x0000BAD4 File Offset: 0x00009CD4
	private void Input()
	{
		if (UnityEngine.Input.GetKeyDown(KeyCode.Escape))
		{
			this._inGameMenuController.OnEscapeButtonClick();
		}
		if (this._mapKeyClicked != InputSettingsController.Instance.GetKeyInput(InputActions.Map))
		{
			if (this._mapKeyClicked)
			{
				this._inGameMenuController.SwitchMenu(MenuType.Map);
			}
			this._mapKeyClicked = InputSettingsController.Instance.GetKeyInput(InputActions.Map);
		}
	}

	// Token: 0x04000189 RID: 393
	[SerializeField]
	private BaseMenuView[] _menues;

	// Token: 0x0400018A RID: 394
	private InGameMenuController _inGameMenuController;

	// Token: 0x0400018B RID: 395
	private ISwitchableMenu _switchableMenu;

	// Token: 0x0400018C RID: 396
	private bool _mapKeyClicked;
}
﻿using System;

// Token: 0x02000072 RID: 114
public enum InputActions
{
	// Token: 0x04000236 RID: 566
	Forward,
	// Token: 0x04000237 RID: 567
	Left,
	// Token: 0x04000238 RID: 568
	Back,
	// Token: 0x04000239 RID: 569
	Right,
	// Token: 0x0400023A RID: 570
	Run,
	// Token: 0x0400023B RID: 571
	Crouch,
	// Token: 0x0400023C RID: 572
	Jump,
	// Token: 0x0400023D RID: 573
	Fire,
	// Token: 0x0400023E RID: 574
	Aim,
	// Token: 0x0400023F RID: 575
	Reload,
	// Token: 0x04000240 RID: 576
	QuickLethal,
	// Token: 0x04000241 RID: 577
	QuickNonLethal,
	// Token: 0x04000242 RID: 578
	QuickStrike,
	// Token: 0x04000243 RID: 579
	LeanLeft,
	// Token: 0x04000244 RID: 580
	LeanRight,
	// Token: 0x04000245 RID: 581
	DropBomb,
	// Token: 0x04000246 RID: 582
	ActivateGrenade,
	// Token: 0x04000247 RID: 583
	Map,
	// Token: 0x04000248 RID: 584
	DropWeapon,
	// Token: 0x04000249 RID: 585
	ChangePerspective,
	// Token: 0x0400024A RID: 586
	FlashLight,
	// Token: 0x0400024B RID: 587
	Laser,
	// Token: 0x0400024C RID: 588
	WeaponOne,
	// Token: 0x0400024D RID: 589
	WeaponTwo,
	// Token: 0x0400024E RID: 590
	LethalGrenade,
	// Token: 0x0400024F RID: 591
	NonLethalGrenade,
	// Token: 0x04000250 RID: 592
	Melee,
	// Token: 0x04000251 RID: 593
	None
}
﻿using System;
using System.Collections.Generic;
using Newtonsoft.Json;
using UnityEngine;

// Token: 0x02000073 RID: 115
public class InputSettingsController
{
	// Token: 0x1700002D RID: 45
	// (get) Token: 0x0600031C RID: 796 RVA: 0x0000F888 File Offset: 0x0000DA88
	public static InputSettingsController Instance
	{
		get
		{
			if (InputSettingsController._instance == null)
			{
				InputSettingsController._instance = new InputSettingsController();
			}
			return InputSettingsController._instance;
		}
	}

	// Token: 0x1700002E RID: 46
	// (get) Token: 0x0600031D RID: 797 RVA: 0x0000F8A0 File Offset: 0x0000DAA0
	// (set) Token: 0x0600031E RID: 798 RVA: 0x0000F8A8 File Offset: 0x0000DAA8
	public bool IsWriting { get; set; }

	// Token: 0x0600031F RID: 799 RVA: 0x0000F8B1 File Offset: 0x0000DAB1
	private InputSettingsController()
	{
		this.Load();
	}

	// Token: 0x06000320 RID: 800 RVA: 0x0000F8CA File Offset: 0x0000DACA
	public bool GetKeyInput(InputActions inputAction)
	{
		return Input.GetKey(this._inputSettings[inputAction]);
	}

	// Token: 0x06000321 RID: 801 RVA: 0x0000F8E0 File Offset: 0x0000DAE0
	public float GetAxisInput(string axis)
	{
		float result = 0f;
		if (axis == "Horizontal")
		{
			if (Input.GetKey(this._inputSettings[InputActions.Right]))
			{
				result = 1f;
			}
			if (Input.GetKey(this._inputSettings[InputActions.Left]))
			{
				result = -1f;
			}
		}
		else if (axis == "Vertical")
		{
			if (Input.GetKey(this._inputSettings[InputActions.Forward]))
			{
				result = 1f;
			}
			if (Input.GetKey(this._inputSettings[InputActions.Back]))
			{
				result = -1f;
			}
		}
		return result;
	}

	// Token: 0x06000322 RID: 802 RVA: 0x0000F974 File Offset: 0x0000DB74
	public string GetKeyString(InputActions action)
	{
		return this._inputSettings[action].ToString();
	}

	// Token: 0x06000323 RID: 803 RVA: 0x0000F99B File Offset: 0x0000DB9B
	public void SetInput(InputActions action, KeyCode keyCode)
	{
		this._inputSettings[action] = keyCode;
		this.IsWriting = false;
		this.Save();
	}

	// Token: 0x06000324 RID: 804 RVA: 0x0000F9B8 File Offset: 0x0000DBB8
	public void Save()
	{
		string value = JsonConvert.SerializeObject(this._inputSettings);
		PlayerPrefs.SetString(this._inputSettingsKey, value);
		PlayerPrefs.Save();
	}

	// Token: 0x06000325 RID: 805 RVA: 0x0000F9E4 File Offset: 0x0000DBE4
	private void Load()
	{
		if (PlayerPrefs.HasKey(this._inputSettingsKey))
		{
			string @string = PlayerPrefs.GetString(this._inputSettingsKey);
			this._inputSettings = JsonConvert.DeserializeObject<Dictionary<InputActions, KeyCode>>(@string);
			return;
		}
		this.SetDefaultSettings();
	}

	// Token: 0x06000326 RID: 806 RVA: 0x0000FA20 File Offset: 0x0000DC20
	private void SetDefaultSettings()
	{
		Dictionary<InputActions, KeyCode> dictionary = new Dictionary<InputActions, KeyCode>();
		dictionary[InputActions.Forward] = KeyCode.W;
		dictionary[InputActions.Left] = KeyCode.A;
		dictionary[InputActions.Back] = KeyCode.S;
		dictionary[InputActions.Right] = KeyCode.D;
		dictionary[InputActions.Run] = KeyCode.LeftShift;
		dictionary[InputActions.Crouch] = KeyCode.LeftControl;
		dictionary[InputActions.Jump] = KeyCode.Space;
		dictionary[InputActions.Fire] = KeyCode.Mouse0;
		dictionary[InputActions.Aim] = KeyCode.Mouse1;
		dictionary[InputActions.Reload] = KeyCode.R;
		dictionary[InputActions.QuickLethal] = KeyCode.G;
		dictionary[InputActions.QuickNonLethal] = KeyCode.H;
		dictionary[InputActions.QuickStrike] = KeyCode.V;
		dictionary[InputActions.LeanLeft] = KeyCode.Q;
		dictionary[InputActions.LeanRight] = KeyCode.E;
		dictionary[InputActions.Map] = KeyCode.M;
		dictionary[InputActions.DropBomb] = KeyCode.X;
		dictionary[InputActions.ActivateGrenade] = KeyCode.R;
		dictionary[InputActions.DropWeapon] = KeyCode.F;
		dictionary[InputActions.ChangePerspective] = KeyCode.P;
		dictionary[InputActions.FlashLight] = KeyCode.Z;
		dictionary[InputActions.Laser] = KeyCode.T;
		dictionary[InputActions.WeaponOne] = KeyCode.Alpha1;
		dictionary[InputActions.WeaponTwo] = KeyCode.Alpha2;
		dictionary[InputActions.Melee] = KeyCode.Alpha3;
		dictionary[InputActions.LethalGrenade] = KeyCode.Alpha4;
		dictionary[InputActions.NonLethalGrenade] = KeyCode.Alpha5;
		dictionary[InputActions.None] = KeyCode.None;
		this._inputSettings = dictionary;
	}

	// Token: 0x04000252 RID: 594
	private static InputSettingsController _instance;

	// Token: 0x04000253 RID: 595
	private Dictionary<InputActions, KeyCode> _inputSettings;

	// Token: 0x04000254 RID: 596
	private string _inputSettingsKey = "InputSettings";
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x020000F7 RID: 247
public class IntArrayRandomizer : MonoBehaviour
{
	// Token: 0x06000632 RID: 1586 RVA: 0x0001D317 File Offset: 0x0001B517
	private void Start()
	{
		this.RandomizeArray();
	}

	// Token: 0x06000633 RID: 1587 RVA: 0x0001D320 File Offset: 0x0001B520
	private void RandomizeArray()
	{
		List<int> list = new List<int>();
		foreach (Item3D item3D in this._item3DBank.Items)
		{
			if (!this._exceptions.Contains(item3D.ID))
			{
				list.Add(item3D.ID);
			}
		}
		int count = list.Count;
		List<int> list2 = new List<int>(count);
		for (int i = 0; i < count; i++)
		{
			int item = list[UnityEngine.Random.Range(0, list.Count)];
			list2.Add(item);
			list.Remove(item);
		}
		for (int j = 0; j < list2.Count; j++)
		{
			Debug.Log(string.Format("{0}: {1}", j, list2[j]));
		}
	}

	// Token: 0x04000563 RID: 1379
	[SerializeField]
	private Items3DBank _item3DBank;

	// Token: 0x04000564 RID: 1380
	[SerializeField]
	private List<int> _exceptions;
}
﻿using System;
using UnityEngine.Events;

// Token: 0x02000039 RID: 57
[Serializable]
public class IntEvent : UnityEvent<int>
{
}
﻿using System;

// Token: 0x02000079 RID: 121
public interface IRoundHUD
{
	// Token: 0x14000009 RID: 9
	// (add) Token: 0x06000333 RID: 819
	// (remove) Token: 0x06000334 RID: 820
	event Action OnRoundEnd;
}
﻿using System;

// Token: 0x0200007A RID: 122
public interface IRounds
{
	// Token: 0x17000031 RID: 49
	// (get) Token: 0x06000335 RID: 821
	int[] Rounds { get; }
}
﻿using System;

// Token: 0x0200007B RID: 123
public interface ISwitchableMenu
{
	// Token: 0x06000336 RID: 822
	void SwitchMenu(MenuType menu);

	// Token: 0x06000337 RID: 823
	void SwitchToPreviousMenu();

	// Token: 0x06000338 RID: 824
	void HideCurrentMenu();

	// Token: 0x17000032 RID: 50
	// (get) Token: 0x06000339 RID: 825
	MainMenuData MainMenuData { get; }
}
﻿using System;

// Token: 0x0200007C RID: 124
public interface ITeamGameMode
{
	// Token: 0x17000033 RID: 51
	// (get) Token: 0x0600033A RID: 826
	int[] TeamPoints { get; }
}
﻿using System;
using UnityEngine;

// Token: 0x02000080 RID: 128
[Serializable]
public class Item3D
{
	// Token: 0x04000272 RID: 626
	public string Name;

	// Token: 0x04000273 RID: 627
	public int ID;

	// Token: 0x04000274 RID: 628
	public GameObject Item;
}
﻿using System;
using System.Collections.Generic;
using DG.Tweening;
using UnityEngine;
using UnityEngine.Events;

// Token: 0x020000C6 RID: 198
public class ItemBoxTween : MonoBehaviour
{
	// Token: 0x060004CD RID: 1229 RVA: 0x000167DB File Offset: 0x000149DB
	public void SeItemList(List<int> openItems)
	{
		this._openItemList = openItems;
		this._showCardIteration = 0;
		this.OpenBox();
	}

	// Token: 0x060004CE RID: 1230 RVA: 0x000167F4 File Offset: 0x000149F4
	public void OpenBox()
	{
		this._openBoxSequence = DOTween.Sequence();
		this._openBoxSequence.AppendCallback(delegate
		{
			this._cardAudio.PlayOneShot(this._openBoxSFX);
		}).Append(this._boxCap.DOLocalRotate(this._openBoxTargetAngle, this._mainDurationMultiplier * this._openBoxDuration, RotateMode.Fast));
		this._openBoxSequence.OnComplete(new TweenCallback(this.ShowCard));
	}

	// Token: 0x060004CF RID: 1231 RVA: 0x00016860 File Offset: 0x00014A60
	private void ShowCard()
	{
		this._openBoxSequence.Kill(false);
		ItemCardImage itemCardImage = this._itemsCards.GetItemCardImage(this._openItemList[this._showCardIteration]);
		this._contentSprite.sprite = itemCardImage.ItemImage;
		this._backGroundSprite.sprite = this._itemsRarity.GetRarityContent(itemCardImage.Rarity).Background;
		this._ratitySprite.sprite = this._itemsRarity.GetRarityContent(itemCardImage.Rarity).RarityImage;
		this._showCardSequence = DOTween.Sequence();
		this._showCardSequence.Append(this._itemCard.DOMove(this._itemCardUpPosition.position, this._mainDurationMultiplier * this._itemCardUpDuration, false)).Append(this._itemCard.DOMove(this._itemCardKickPosition.position, this._mainDurationMultiplier * this._itemCardKickDuration, false)).AppendCallback(delegate
		{
			this._cardAudio.PlayOneShot(this._explosionSFX);
			this._kickCardEvent.Invoke();
		}).AppendInterval(this._kickIntervalDuration).Append(this._itemCard.DOScale(Vector3.zero, 0f)).Append(this._curveItemCard.DOScale(this._curveItemCardStartScale, 0f)).Append(this._curveItemCard.DOLocalRotate(this._curveCardEndAngle, this._curveCardRotateDuration, RotateMode.LocalAxisAdd)).Join(this._itemCard.DOLocalRotate(this._itemCardEndAngle, this._curveCardRotateDuration, RotateMode.LocalAxisAdd)).Append(this._itemCard.DOScale(this._itemCardStartScale, 0f)).Join(this._curveItemCard.DOScale(Vector3.zero, 0f)).AppendCallback(delegate
		{
			this._cardAudio.PlayOneShot(this._explosionSFX);
			UnityEngine.Object.Instantiate<GameObject>(this._explositionPrefab, this._explositionPosition.position, Quaternion.identity);
			this._kickCardEvent.Invoke();
		});
		this._showCardSequence.OnComplete(delegate
		{
			this._showCardSequence.Kill(false);
			this.readyInput = true;
		});
	}

	// Token: 0x060004D0 RID: 1232 RVA: 0x00016A38 File Offset: 0x00014C38
	private void HideCard()
	{
		this._hideCardSequence = DOTween.Sequence();
		this._hideCardSequence.AppendCallback(delegate
		{
			this._cardAudio.PlayOneShot(this._flyCardSFX);
		}).Append(this._itemCard.DOMove(this._itemCardDownPosition.position, this._itemCardDownDuration, false)).Append(this._itemCard.DOLocalRotate(this._itemCardEndAngle, this._curveCardRotateDuration, RotateMode.LocalAxisAdd));
		this._hideCardSequence.OnComplete(new TweenCallback(this.EndSequence));
	}

	// Token: 0x060004D1 RID: 1233 RVA: 0x00016AC0 File Offset: 0x00014CC0
	private void EndSequence()
	{
		this._showCardIteration++;
		if (this._showCardIteration < this._openItemList.Count)
		{
			this.ShowCard();
			return;
		}
		this._boxCap.DOLocalRotate(this._openBoxStartAngle, this._openBoxDuration * this._mainDurationMultiplier, RotateMode.Fast);
		this._EndSequenceEvent.Invoke();
	}

	// Token: 0x060004D2 RID: 1234 RVA: 0x00016B20 File Offset: 0x00014D20
	private void Update()
	{
		if (this.readyInput && Input.anyKey)
		{
			this.readyInput = false;
			this.HideCard();
		}
	}

	// Token: 0x040003CC RID: 972
	[SerializeField]
	private BigItemIcon _itemIcon;

	// Token: 0x040003CD RID: 973
	[SerializeField]
	private SpriteRenderer _contentSprite;

	// Token: 0x040003CE RID: 974
	[SerializeField]
	private SpriteRenderer _backGroundSprite;

	// Token: 0x040003CF RID: 975
	[SerializeField]
	private SpriteRenderer _ratitySprite;

	// Token: 0x040003D0 RID: 976
	[SerializeField]
	private GameObject _explositionPrefab;

	// Token: 0x040003D1 RID: 977
	[SerializeField]
	private Transform _boxCap;

	// Token: 0x040003D2 RID: 978
	[SerializeField]
	private Transform _itemCard;

	// Token: 0x040003D3 RID: 979
	[SerializeField]
	private Transform _curveItemCard;

	// Token: 0x040003D4 RID: 980
	[SerializeField]
	private ItemsCardImagesBank _itemsCards;

	// Token: 0x040003D5 RID: 981
	[SerializeField]
	private RarityContentBank _itemsRarity;

	// Token: 0x040003D6 RID: 982
	[Header("Durations")]
	[SerializeField]
	private float _mainDurationMultiplier;

	// Token: 0x040003D7 RID: 983
	[SerializeField]
	private float _openBoxDuration;

	// Token: 0x040003D8 RID: 984
	[SerializeField]
	private float _itemCardUpDuration;

	// Token: 0x040003D9 RID: 985
	[SerializeField]
	private float _itemCardDownDuration;

	// Token: 0x040003DA RID: 986
	[SerializeField]
	private float _itemCardKickDuration;

	// Token: 0x040003DB RID: 987
	[SerializeField]
	private float _kickIntervalDuration;

	// Token: 0x040003DC RID: 988
	[SerializeField]
	private float _curveCardRotateDuration;

	// Token: 0x040003DD RID: 989
	[SerializeField]
	private float _openBoxIntervalDuration;

	// Token: 0x040003DE RID: 990
	[Header("Key Angles")]
	[SerializeField]
	private Vector3 _openBoxStartAngle;

	// Token: 0x040003DF RID: 991
	[SerializeField]
	private Vector3 _openBoxTargetAngle;

	// Token: 0x040003E0 RID: 992
	[SerializeField]
	private Vector3 _itemCardEndAngle;

	// Token: 0x040003E1 RID: 993
	[SerializeField]
	private Vector3 _curveCardEndAngle;

	// Token: 0x040003E2 RID: 994
	[Header("Key Positions")]
	[SerializeField]
	private Transform _explositionPosition;

	// Token: 0x040003E3 RID: 995
	[SerializeField]
	private Transform _itemCardUpPosition;

	// Token: 0x040003E4 RID: 996
	[SerializeField]
	private Transform _itemCardDownPosition;

	// Token: 0x040003E5 RID: 997
	[SerializeField]
	private Transform _itemCardKickPosition;

	// Token: 0x040003E6 RID: 998
	[Header("Key Scales")]
	[SerializeField]
	private Vector3 _itemCardStartScale;

	// Token: 0x040003E7 RID: 999
	[SerializeField]
	private Vector3 _curveItemCardStartScale;

	// Token: 0x040003E8 RID: 1000
	[Header("Audio")]
	[SerializeField]
	private AudioSource _cardAudio;

	// Token: 0x040003E9 RID: 1001
	[SerializeField]
	private AudioClip _explosionSFX;

	// Token: 0x040003EA RID: 1002
	[SerializeField]
	private AudioClip _flyCardSFX;

	// Token: 0x040003EB RID: 1003
	[SerializeField]
	private AudioClip _openBoxSFX;

	// Token: 0x040003EC RID: 1004
	private bool readyInput;

	// Token: 0x040003ED RID: 1005
	private List<int> _openItemList = new List<int>();

	// Token: 0x040003EE RID: 1006
	private int _showCardIteration;

	// Token: 0x040003EF RID: 1007
	[HideInInspector]
	public UnityEvent _kickCardEvent = new UnityEvent();

	// Token: 0x040003F0 RID: 1008
	[HideInInspector]
	public UnityEvent _EndSequenceEvent = new UnityEvent();

	// Token: 0x040003F1 RID: 1009
	private Sequence _openBoxSequence;

	// Token: 0x040003F2 RID: 1010
	private Sequence _showCardSequence;

	// Token: 0x040003F3 RID: 1011
	private Sequence _hideCardSequence;
}
﻿using System;
using UnityEngine;

// Token: 0x02000081 RID: 129
[Serializable]
public class ItemCardImage
{
	// Token: 0x04000275 RID: 629
	public string Name;

	// Token: 0x04000276 RID: 630
	public int ID;

	// Token: 0x04000277 RID: 631
	public Sprite ItemImage;

	// Token: 0x04000278 RID: 632
	public RarityType Rarity;
}
﻿using System;
using UnityEngine;

// Token: 0x0200009B RID: 155
[Serializable]
public class ItemIcon
{
	// Token: 0x040002CE RID: 718
	public int id;

	// Token: 0x040002CF RID: 719
	public Sprite icon;

	// Token: 0x040002D0 RID: 720
	public Sprite smallIcon;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x02000082 RID: 130
[CreateAssetMenu(menuName = "Banks/Items3DBank")]
public class Items3DBank : ScriptableObject
{
	// Token: 0x04000279 RID: 633
	public List<Item3D> Items;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x02000083 RID: 131
[CreateAssetMenu(menuName = "Banks/ItemsCardsImagesBank")]
public class ItemsCardImagesBank : ScriptableObject
{
	// Token: 0x06000346 RID: 838 RVA: 0x0000FE64 File Offset: 0x0000E064
	public ItemCardImage GetItemCardImage(int id)
	{
		for (int i = 0; i < this.CardImages.Count; i++)
		{
			if (this.CardImages[i].ID == id)
			{
				return this.CardImages[i];
			}
		}
		return null;
	}

	// Token: 0x0400027A RID: 634
	public List<ItemCardImage> CardImages;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using UnityEngine;

// Token: 0x020000C0 RID: 192
public class ItemTaskManager : MonoBehaviour
{
	// Token: 0x060004B4 RID: 1204 RVA: 0x00015E1C File Offset: 0x0001401C
	public TaskInfo GetTaskInfo(int id)
	{
		TaskInfo taskInfo = new TaskInfo();
		foreach (TaskData taskData in this.data.taskList)
		{
			if (taskData.itemId == id)
			{
				if (this.staticticsServerData.Account != null)
				{
					taskInfo.curValue = taskData.task.UpdateCounter(this.staticticsServerData);
				}
				taskInfo.description = taskData.task.name;
				taskInfo.targetValue = taskData.task.target;
				break;
			}
		}
		return taskInfo;
	}

	// Token: 0x060004B5 RID: 1205 RVA: 0x00015EC8 File Offset: 0x000140C8
	public int[] GetCompletteTask()
	{
		List<int> list = new List<int>();
		foreach (TaskData taskData in this.data.taskList)
		{
			TaskInfo taskInfo = this.GetTaskInfo(taskData.itemId);
			if (taskInfo.curValue >= taskInfo.targetValue && !StoreController.Instance.openItems.Contains(taskData.itemId))
			{
				list.Add(taskData.itemId);
			}
		}
		int[] array = new int[list.Count];
		list.CopyTo(array);
		return array;
	}

	// Token: 0x04000398 RID: 920
	[SerializeField]
	private ItemTasks data;

	// Token: 0x04000399 RID: 921
	[SerializeField]
	private StaticticsServerData staticticsServerData;

	// Token: 0x0400039A RID: 922
	[SerializeField]
	private Kit_LevelingBase leveling;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x020000C2 RID: 194
[CreateAssetMenu(menuName = "MarsFPSKit/ItemTaskData")]
public class ItemTasks : ScriptableObject
{
	// Token: 0x0400039D RID: 925
	public List<TaskData> taskList;
}
﻿using System;
using System.Collections;
using System.Collections.Generic;
using DG.Tweening;
using ExitGames.Client.Photon;
using MarsFPSKit;
using Photon.Pun;
using Photon.Realtime;
using TMPro;
using UnityEngine;
using UnityEngine.Events;

// Token: 0x02000038 RID: 56
public class KillProcess : MonoBehaviourPunCallbacks
{
	// Token: 0x06000152 RID: 338 RVA: 0x00007884 File Offset: 0x00005A84
	public int AddScore(int score)
	{
		if (this.main.gameInformation.leveling)
		{
			this.main.gameInformation.leveling.AddXp(this.main, score);
		}
		this.main.pointsUI.DisplayPoints(score, PointType.Kill);
		this.scoreCounter += score;
		PhotonNetwork.RaiseEvent(Kit_EventIDs.scoreChangeEvent, null, new RaiseEventOptions
		{
			Receivers = ReceiverGroup.All
		}, SendOptions.SendReliable);
		return this.scoreCounter;
	}

	// Token: 0x06000153 RID: 339 RVA: 0x00007908 File Offset: 0x00005B08
	public void OnPhotonEvent(Kit_IngameMain main, byte evCode, object content, int senderId)
	{
		if (evCode == Kit_EventIDs.scoreChangeEvent)
		{
			ExitGames.Client.Photon.Hashtable customProperties = PhotonNetwork.LocalPlayer.CustomProperties;
			int num = (int)customProperties["score"];
			num = this.scoreCounter;
			customProperties["score"] = num;
			PhotonNetwork.LocalPlayer.SetCustomProperties(customProperties, null, null);
		}
	}

	// Token: 0x06000154 RID: 340 RVA: 0x00007960 File Offset: 0x00005B60
	public void KillProcessing(bool botKiller, int killer, bool botKilled, int killed, int gun, int ragdollId)
	{
		if (!botKilled && killed == PhotonNetwork.LocalPlayer.ActorNumber)
		{
			this.DeathLocalPlayer(botKiller, killer, ragdollId);
		}
		if (!botKiller && killer == PhotonNetwork.LocalPlayer.ActorNumber && (botKilled || killed != PhotonNetwork.LocalPlayer.ActorNumber))
		{
			if (ragdollId == 10)
			{
				this.ReplaceKillFx(this.killFxList[0]);
			}
			else if (this._killCounter < 1)
			{
				this.AddScore(this.main.gameInformation.pointsPerKill);
			}
			switch (gun)
			{
			case 0:
				this.main.gameInformation.statistics.OnAssaultKill(this.main);
				break;
			case 1:
				this.main.gameInformation.statistics.OnAssaultKill(this.main);
				break;
			case 2:
				this.main.gameInformation.statistics.OnAssaultKill(this.main);
				break;
			case 3:
				this.main.gameInformation.statistics.OnAssaultKill(this.main);
				break;
			case 4:
				this.main.gameInformation.statistics.OnPistolKill(this.main);
				break;
			case 5:
				this.main.gameInformation.statistics.OnPistolKill(this.main);
				break;
			case 6:
				this.main.gameInformation.statistics.OnShotGunKill(this.main);
				break;
			case 7:
				this.main.gameInformation.statistics.OnSniperKill(this.main);
				break;
			case 8:
				this.main.gameInformation.statistics.OnSniperKill(this.main);
				break;
			case 9:
				this.main.gameInformation.statistics.OnMeleeKill(this.main);
				break;
			case 10:
				this.main.gameInformation.statistics.OnMeleeKill(this.main);
				break;
			case 11:
				this.ReplaceKillFx(this.killFxList[1]);
				break;
			}
			this._killCounter++;
			this._killTime = this._killTimer;
		}
	}

	// Token: 0x06000155 RID: 341 RVA: 0x00007BA8 File Offset: 0x00005DA8
	private void ReplaceKillFx(Achiwments curFx)
	{
		if (this.currentKillFx != null)
		{
			this.currentKillFx.EndSequence();
		}
		this.currentKillFx = curFx;
		this.currentKillFx.PlaySequence();
		this.currentKillFx.PlaySequence(this._killCounter);
	}

	// Token: 0x06000156 RID: 342 RVA: 0x00007BE8 File Offset: 0x00005DE8
	private void DeathLocalPlayer(bool botKiller, int killer, int ragdollId)
	{
		if (botKiller)
		{
			string name = this.main.currentBotManager.GetBotWithID(killer).name;
			this.PlaySequence(name, ragdollId);
			return;
		}
		for (int i = 0; i < PhotonNetwork.PlayerList.Length; i++)
		{
			if (PhotonNetwork.PlayerList[i].ActorNumber == killer)
			{
				Player player = PhotonNetwork.PlayerList[i];
				this.PlaySequence(player.NickName, ragdollId);
				return;
			}
		}
	}

	// Token: 0x06000157 RID: 343 RVA: 0x00007C54 File Offset: 0x00005E54
	private void PlaySequence(string murderName, int ragdollId)
	{
		this._bullet.parent.gameObject.SetActive(true);
		this._deathText.text = murderName;
		this._deathText.gameObject.SetActive(true);
		if (ragdollId == 10)
		{
			this._skull.SetActive(true);
			this._bullet.gameObject.SetActive(true);
			this._skullFragment.transform.parent.gameObject.SetActive(true);
			this._headShotDeath = DOTween.Sequence();
			this._headShotDeath.Append(this._bullet.DOLocalMoveX(0f, this._bulletDuration, true));
			this._headShotDeath.OnComplete(new TweenCallback(this.HitOnSkull));
			return;
		}
		base.StartCoroutine(this.Timer());
	}

	// Token: 0x06000158 RID: 344 RVA: 0x00007D25 File Offset: 0x00005F25
	private void HitOnSkull()
	{
		this._headShotDeath.Kill(false);
		this._skull.SetActive(false);
		this._skullFragment.Play();
		base.StartCoroutine(this.Timer());
	}

	// Token: 0x06000159 RID: 345 RVA: 0x00007D57 File Offset: 0x00005F57
	private IEnumerator Timer()
	{
		yield return new WaitForSeconds(this._endDelayDuration);
		this.DisableDeathUI();
		yield break;
	}

	// Token: 0x0600015A RID: 346 RVA: 0x00007D68 File Offset: 0x00005F68
	private void DisableDeathUI()
	{
		this._bullet.DOLocalMoveX((float)(-(float)Screen.width / 2 - 100), 0f, false);
		this._deathText.gameObject.SetActive(false);
		this._bullet.parent.gameObject.SetActive(false);
		this._skullFragment.Stop();
		this._skull.SetActive(false);
		this._bullet.gameObject.SetActive(false);
		this._skullFragment.transform.parent.gameObject.SetActive(false);
	}

	// Token: 0x0600015B RID: 347 RVA: 0x00007E00 File Offset: 0x00006000
	private void Update()
	{
		if (this._killTime > 0f)
		{
			this._killTime -= 0.1f;
			return;
		}
		if (this._killCounter > 1)
		{
			this.ReplaceKillFx(this.killFxList[2]);
			this._killCounter = 0;
			return;
		}
		this._killCounter = 0;
	}

	// Token: 0x040000BF RID: 191
	public Kit_IngameMain main;

	// Token: 0x040000C0 RID: 192
	[HideInInspector]
	public int scoreCounter;

	// Token: 0x040000C1 RID: 193
	[HideInInspector]
	public UnityEvent headShotEvent;

	// Token: 0x040000C2 RID: 194
	[HideInInspector]
	public UnityEvent grenadeKillEvent;

	// Token: 0x040000C3 RID: 195
	[HideInInspector]
	public IntEvent comboKillEvent;

	// Token: 0x040000C4 RID: 196
	[SerializeField]
	private TMP_Text _deathText;

	// Token: 0x040000C5 RID: 197
	[SerializeField]
	private RectTransform _bullet;

	// Token: 0x040000C6 RID: 198
	[SerializeField]
	private ParticleSystem _skullFragment;

	// Token: 0x040000C7 RID: 199
	[SerializeField]
	private GameObject _skull;

	// Token: 0x040000C8 RID: 200
	[SerializeField]
	private float _bulletDuration;

	// Token: 0x040000C9 RID: 201
	[SerializeField]
	private float _endDelayDuration;

	// Token: 0x040000CA RID: 202
	[SerializeField]
	private float _killTimer;

	// Token: 0x040000CB RID: 203
	[SerializeField]
	private List<Achiwments> killFxList = new List<Achiwments>();

	// Token: 0x040000CC RID: 204
	public Achiwments currentKillFx;

	// Token: 0x040000CD RID: 205
	private Sequence _headShotDeath;

	// Token: 0x040000CE RID: 206
	private int _killCounter;

	// Token: 0x040000CF RID: 207
	private float _killTime;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x02000084 RID: 132
[Serializable]
public class LanguageGroup
{
	// Token: 0x17000036 RID: 54
	// (get) Token: 0x06000348 RID: 840 RVA: 0x0000FEA9 File Offset: 0x0000E0A9
	public string Name
	{
		get
		{
			return this._name;
		}
	}

	// Token: 0x17000037 RID: 55
	// (get) Token: 0x06000349 RID: 841 RVA: 0x0000FEB1 File Offset: 0x0000E0B1
	public List<LanguageKey> Keyes
	{
		get
		{
			return this._keyes;
		}
	}

	// Token: 0x0400027B RID: 635
	[SerializeField]
	private string _name;

	// Token: 0x0400027C RID: 636
	[SerializeField]
	private List<LanguageKey> _keyes;
}
﻿using System;
using UnityEngine;

// Token: 0x02000085 RID: 133
[Serializable]
public class LanguageKey
{
	// Token: 0x17000038 RID: 56
	// (get) Token: 0x0600034B RID: 843 RVA: 0x0000FEB9 File Offset: 0x0000E0B9
	public string Name
	{
		get
		{
			return this._name;
		}
	}

	// Token: 0x17000039 RID: 57
	// (get) Token: 0x0600034C RID: 844 RVA: 0x0000FEC1 File Offset: 0x0000E0C1
	public string Text
	{
		get
		{
			return this._text;
		}
	}

	// Token: 0x0400027D RID: 637
	[SerializeField]
	private string _name;

	// Token: 0x0400027E RID: 638
	[SerializeField]
	[TextArea]
	private string _text;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x02000086 RID: 134
[CreateAssetMenu(fileName = "Language", menuName = "LanguageModel")]
public class LanguageModel : ScriptableObject
{
	// Token: 0x1700003A RID: 58
	// (get) Token: 0x0600034E RID: 846 RVA: 0x0000FEC9 File Offset: 0x0000E0C9
	public List<LanguageGroup> Groups
	{
		get
		{
			return this._groups;
		}
	}

	// Token: 0x0400027F RID: 639
	[SerializeField]
	private List<LanguageGroup> _groups;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit.UI;
using TMPro;
using UnityEngine;

// Token: 0x020000B0 RID: 176
[CreateAssetMenu(menuName = "MarsFPSKit/Options/Audio/Language")]
public class LanguageOption : Kit_OptionBase
{
	// Token: 0x0600040F RID: 1039 RVA: 0x00011F57 File Offset: 0x00010157
	public override string GetDisplayName()
	{
		return "Language";
	}

	// Token: 0x06000410 RID: 1040 RVA: 0x00011DE0 File Offset: 0x0000FFE0
	public override string GetHoverText()
	{
		return LocalizationController.Instance.GetText("OptionsHoverText", this.LocalizationKey);
	}

	// Token: 0x06000411 RID: 1041 RVA: 0x00011DF7 File Offset: 0x0000FFF7
	public override OptionType GetOptionType()
	{
		return OptionType.Dropdown;
	}

	// Token: 0x06000412 RID: 1042 RVA: 0x00011F5E File Offset: 0x0001015E
	public override void OnDropdowChange(TextMeshProUGUI txt, int newValue)
	{
		LocalizationController.Instance.ChangeLanguage(newValue);
	}

	// Token: 0x06000413 RID: 1043 RVA: 0x00011F6C File Offset: 0x0001016C
	public override void OnDropdownStart(TextMeshProUGUI txt, TMP_Dropdown dropdown)
	{
		dropdown.ClearOptions();
		dropdown.AddOptions(this._languages);
		int currentLanguage = LocalizationController.Instance.CurrentLanguage;
		dropdown.value = currentLanguage;
		this.OnDropdowChange(txt, currentLanguage);
	}

	// Token: 0x04000321 RID: 801
	[SerializeField]
	private List<string> _languages = new List<string>
	{
		"Русский",
		"English"
	};
}
﻿using System;

// Token: 0x0200006D RID: 109
public enum LanguageType
{
	// Token: 0x04000207 RID: 519
	Russian,
	// Token: 0x04000208 RID: 520
	English
}
﻿using System;
using UnityEngine;

// Token: 0x02000056 RID: 86
public class LerpProcess
{
	// Token: 0x17000019 RID: 25
	// (get) Token: 0x06000228 RID: 552 RVA: 0x0000BB30 File Offset: 0x00009D30
	public bool IsBusy
	{
		get
		{
			return this._isBusy;
		}
	}

	// Token: 0x06000229 RID: 553 RVA: 0x0000BB38 File Offset: 0x00009D38
	public LerpProcess()
	{
		this._sceneDriver = UnityEngine.Object.FindObjectOfType<SceneDriver>();
	}

	// Token: 0x0600022A RID: 554 RVA: 0x0000BB4C File Offset: 0x00009D4C
	public void StartProcess(Action<float> action, float time)
	{
		if (!this._isBusy)
		{
			this._action = action;
			this._time = time;
			this._speed = 1f / time;
			this._currentTime = 0f;
			this._sceneDriver.OnUpdate += this.ExecuteProcess;
			this._isBusy = true;
		}
	}

	// Token: 0x0600022B RID: 555 RVA: 0x0000BBA5 File Offset: 0x00009DA5
	private void ExecuteProcess()
	{
		if (this._currentTime < 1f)
		{
			this._action(this._currentTime);
			this._currentTime += Time.deltaTime * this._speed;
			return;
		}
		this.EndProcess();
	}

	// Token: 0x0600022C RID: 556 RVA: 0x0000BBE5 File Offset: 0x00009DE5
	public void EndProcess()
	{
		Action<float> action = this._action;
		if (action != null)
		{
			action(1f);
		}
		this._sceneDriver.OnUpdate -= this.ExecuteProcess;
		this._isBusy = false;
	}

	// Token: 0x0400018D RID: 397
	private SceneDriver _sceneDriver;

	// Token: 0x0400018E RID: 398
	private Action<float> _action;

	// Token: 0x0400018F RID: 399
	private float _currentTime;

	// Token: 0x04000190 RID: 400
	private float _time;

	// Token: 0x04000191 RID: 401
	private float _speed;

	// Token: 0x04000192 RID: 402
	private bool _isBusy;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.UI;
using UnityEngine;

// Token: 0x02000057 RID: 87
[CreateAssetMenu(menuName = "MarsFPSKit/Leveling/ServerData based")]
public class LevelingServerData : Kit_LevelingBase, IAccount
{
	// Token: 0x0600022D RID: 557 RVA: 0x0000BC1B File Offset: 0x00009E1B
	public override void AddXp(Kit_IngameMain main, int xp)
	{
		this._accountModel.Statistics.CurrentXp += xp;
		this.RecalculateLevelWithLevelUp(main);
		base.CallEventAddXp(xp);
	}

	// Token: 0x0600022E RID: 558 RVA: 0x0000BC43 File Offset: 0x00009E43
	public override void AddXp(int xp)
	{
		this._accountModel.Statistics.CurrentXp += xp;
		this.RecalculateLevelWithLevelUp();
		base.CallEventAddXp(xp);
	}

	// Token: 0x0600022F RID: 559 RVA: 0x0000BC6A File Offset: 0x00009E6A
	public override int GetLevel()
	{
		if (this._accountModel != null)
		{
			return this._accountModel.Statistics.CurrentLevel;
		}
		return 0;
	}

	// Token: 0x06000230 RID: 560 RVA: 0x0000BC86 File Offset: 0x00009E86
	public override int GetMaxLevel()
	{
		return this.maxLevel;
	}

	// Token: 0x06000231 RID: 561 RVA: 0x0000BC90 File Offset: 0x00009E90
	public override float GetPercentageToNextLevel()
	{
		if (this._accountModel.Statistics.CurrentLevel >= this.maxLevel)
		{
			return 1f;
		}
		return (float)this._accountModel.Statistics.CurrentXp / (float)this.xpNeeded[Mathf.Clamp(this._accountModel.Statistics.CurrentLevel - 1, 0, this.xpNeeded.Length - 1)];
	}

	// Token: 0x06000232 RID: 562 RVA: 0x00005F78 File Offset: 0x00004178
	public override void Initialize(Kit_MenuManager menu)
	{
	}

	// Token: 0x06000233 RID: 563 RVA: 0x0000BCF8 File Offset: 0x00009EF8
	public override void Initialize()
	{
		this.xpNeeded = new int[this.maxLevel - 1];
		for (int i = 0; i < this.maxLevel - 1; i++)
		{
			this.xpNeeded[i] = Mathf.RoundToInt(Mathf.Lerp((float)this.xpNeededForLevelTwo, (float)this.xpNeededForMaxLevel, (float)i / (float)(this.maxLevel - 2)));
		}
	}

	// Token: 0x06000234 RID: 564 RVA: 0x00005F78 File Offset: 0x00004178
	public override void Save()
	{
	}

	// Token: 0x06000235 RID: 565 RVA: 0x0000BD58 File Offset: 0x00009F58
	public int GetExpToNextLevel()
	{
		int num = Mathf.Clamp(this._accountModel.Statistics.CurrentLevel - 1, 0, this.maxLevel - 1);
		return this.xpNeeded[num];
	}

	// Token: 0x06000236 RID: 566 RVA: 0x0000BD90 File Offset: 0x00009F90
	private void RecalculateLevelWithLevelUp(Kit_IngameMain main)
	{
		int num = 0;
		int num2 = 0;
		while (num2 < this.xpNeeded.Length && this._accountModel.Statistics.CurrentXp >= this.xpNeeded[num2])
		{
			num++;
			num2++;
		}
		if (num + 1 > this._accountModel.Statistics.CurrentLevel && main.levelingUi)
		{
			main.levelingUi.DisplayLevelUp(num + 1);
		}
		this._accountModel.Statistics.CurrentLevel = num + 1;
		this.Save();
	}

	// Token: 0x06000237 RID: 567 RVA: 0x0000BE1C File Offset: 0x0000A01C
	private void RecalculateLevelWithLevelUp()
	{
		int num = 0;
		int num2 = 0;
		while (num2 < this.xpNeeded.Length && this._accountModel.Statistics.CurrentXp >= this.xpNeeded[num2])
		{
			num++;
			num2++;
		}
		this._accountModel.Statistics.CurrentLevel = num + 1;
		this.Save();
	}

	// Token: 0x06000238 RID: 568 RVA: 0x0000BE78 File Offset: 0x0000A078
	private void RecalculateLevel()
	{
		int num = 0;
		int num2 = 0;
		while (num2 < this.xpNeeded.Length && this._accountModel.Statistics.CurrentXp > this.xpNeeded[num2])
		{
			num++;
			num2++;
		}
		if (num + 1 > this._accountModel.Statistics.CurrentLevel)
		{
			this.Save();
		}
		this._accountModel.Statistics.CurrentLevel = num + 1;
	}

	// Token: 0x1700001A RID: 26
	// (get) Token: 0x06000239 RID: 569 RVA: 0x0000BEE6 File Offset: 0x0000A0E6
	// (set) Token: 0x0600023A RID: 570 RVA: 0x0000BEEE File Offset: 0x0000A0EE
	public AccountModel Account
	{
		get
		{
			return this._accountModel;
		}
		set
		{
			this._accountModel = value;
		}
	}

	// Token: 0x04000193 RID: 403
	public int maxLevel;

	// Token: 0x04000194 RID: 404
	public int xpNeededForLevelTwo = 500;

	// Token: 0x04000195 RID: 405
	public int xpNeededForMaxLevel = 20000;

	// Token: 0x04000196 RID: 406
	public int[] xpNeeded;

	// Token: 0x04000197 RID: 407
	private AccountModel _accountModel;
}
﻿using System;
using UnityEngine;

// Token: 0x0200003B RID: 59
public class LightCurves : MonoBehaviour
{
	// Token: 0x06000160 RID: 352 RVA: 0x00007E83 File Offset: 0x00006083
	private void Awake()
	{
		this.lightSource = base.GetComponent<Light>();
		this.lightSource.intensity = this.LightCurve.Evaluate(0f);
	}

	// Token: 0x06000161 RID: 353 RVA: 0x00007EAC File Offset: 0x000060AC
	private void OnEnable()
	{
		this.startTime = Time.time;
		this.canUpdate = true;
	}

	// Token: 0x06000162 RID: 354 RVA: 0x00007EC0 File Offset: 0x000060C0
	private void Update()
	{
		float num = Time.time - this.startTime;
		if (this.canUpdate)
		{
			float intensity = this.LightCurve.Evaluate(num / this.GraphTimeMultiplier) * this.GraphIntensityMultiplier;
			this.lightSource.intensity = intensity;
		}
		if (num >= this.GraphTimeMultiplier)
		{
			if (this.IsLoop)
			{
				this.startTime = Time.time;
				return;
			}
			this.canUpdate = false;
		}
	}

	// Token: 0x040000D0 RID: 208
	public AnimationCurve LightCurve = AnimationCurve.EaseInOut(0f, 0f, 1f, 1f);

	// Token: 0x040000D1 RID: 209
	public float GraphTimeMultiplier = 1f;

	// Token: 0x040000D2 RID: 210
	public float GraphIntensityMultiplier = 1f;

	// Token: 0x040000D3 RID: 211
	public bool IsLoop;

	// Token: 0x040000D4 RID: 212
	[HideInInspector]
	public bool canUpdate;

	// Token: 0x040000D5 RID: 213
	private float startTime;

	// Token: 0x040000D6 RID: 214
	private Light lightSource;
}
﻿using System;
using TMPro;
using UnityEngine;

// Token: 0x020000DE RID: 222
public class LoaclizationLabalsView : MonoBehaviour
{
	// Token: 0x06000580 RID: 1408 RVA: 0x000190FF File Offset: 0x000172FF
	public void SetView()
	{
		this._textView.text = LocalizationController.Instance.GetText(this._group, this._key);
	}

	// Token: 0x040004CA RID: 1226
	[SerializeField]
	private string _group;

	// Token: 0x040004CB RID: 1227
	[SerializeField]
	private string _key;

	// Token: 0x040004CC RID: 1228
	[SerializeField]
	private TextMeshProUGUI _textView;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using UnityEngine;

// Token: 0x02000058 RID: 88
public class LoadoutService
{
	// Token: 0x1700001B RID: 27
	// (get) Token: 0x0600023C RID: 572 RVA: 0x0000BF15 File Offset: 0x0000A115
	public static LoadoutService Instance
	{
		get
		{
			if (LoadoutService._instance == null)
			{
				LoadoutService._instance = new LoadoutService();
			}
			return LoadoutService._instance;
		}
	}

	// Token: 0x1700001C RID: 28
	// (get) Token: 0x0600023D RID: 573 RVA: 0x0000BF2D File Offset: 0x0000A12D
	// (set) Token: 0x0600023E RID: 574 RVA: 0x0000BF35 File Offset: 0x0000A135
	public int CurrentSelectedteamForPlayerModel { get; private set; }

	// Token: 0x1700001D RID: 29
	// (get) Token: 0x0600023F RID: 575 RVA: 0x0000BF3E File Offset: 0x0000A13E
	// (set) Token: 0x06000240 RID: 576 RVA: 0x0000BF46 File Offset: 0x0000A146
	public int CurrentSelectedWeaponCategory { get; private set; }

	// Token: 0x1700001E RID: 30
	// (get) Token: 0x06000241 RID: 577 RVA: 0x0000BF4F File Offset: 0x0000A14F
	public Kit_GameInformation Game
	{
		get
		{
			return this._game;
		}
	}

	// Token: 0x06000242 RID: 578 RVA: 0x0000BF57 File Offset: 0x0000A157
	private LoadoutService()
	{
		this._game = Resources.Load<Kit_GameInformation>("Game");
	}

	// Token: 0x06000243 RID: 579 RVA: 0x0000BF78 File Offset: 0x0000A178
	public void Initialize()
	{
		this._allLoadouts = new Loadout[this._amountOfLoadouts];
		for (int i = 0; i < this._allLoadouts.Length; i++)
		{
			int num = i;
			this._allLoadouts[num] = new Loadout();
			this._allLoadouts[num].loadoutWeapons = new LoadoutWeapon[this._game.allWeaponCategories.Length];
			for (int j = 0; j < this._allLoadouts[num].loadoutWeapons.Length; j++)
			{
				int num2 = j;
				this._allLoadouts[num].loadoutWeapons[num2] = new LoadoutWeapon();
				this._allLoadouts[num].loadoutWeapons[num2].goesToSlot = num2;
				this._allLoadouts[num].loadoutWeapons[num2].weaponID = this._game.defaultWeaponsInSlot[num2];
				Kit_WeaponRenderer component = this._game.allWeapons[this._allLoadouts[num].loadoutWeapons[num2].weaponID].firstPersonPrefab.GetComponent<Kit_WeaponRenderer>();
				if (component)
				{
					this._allLoadouts[num].loadoutWeapons[num2].attachments = new int[component.attachmentSlots.Length];
				}
				else
				{
					this._allLoadouts[num].loadoutWeapons[num2].attachments = new int[0];
				}
			}
			this._allLoadouts[num].teamLoadout = new TeamLoadout[this._game.allPvpTeams.Length];
			for (int k = 0; k < this._allLoadouts[num].teamLoadout.Length; k++)
			{
				int num3 = k;
				this._allLoadouts[num].teamLoadout[num3] = new TeamLoadout();
				this._allLoadouts[num].teamLoadout[num3].playerModelID = this._game.allPvpTeams[num3].playerModelDefault;
				this._allLoadouts[num].teamLoadout[num3].playerModelCustomizations = new int[this._game.allPvpTeams[num3].playerModels[this._allLoadouts[num].teamLoadout[num3].playerModelID].prefab.GetComponent<Kit_ThirdPersonPlayerModel>().customizationSlots.Length];
			}
		}
		this.Load();
	}

	// Token: 0x06000244 RID: 580 RVA: 0x0000C19C File Offset: 0x0000A39C
	public void Load()
	{
		for (int i = 0; i < this._allLoadouts.Length; i++)
		{
			int num = i;
			if (PlayerPrefs.HasKey("loadout_" + num + "_weapons"))
			{
				PlayerPrefs.GetInt("loadout_" + num + "_weapons", 0);
				if (this._game.allWeaponCategories.Length == this._allLoadouts[num].loadoutWeapons.Length)
				{
					for (int j = 0; j < this._allLoadouts[num].loadoutWeapons.Length; j++)
					{
						int num2 = j;
						int @int = PlayerPrefs.GetInt(string.Concat(new object[]
						{
							"loadout_",
							num,
							"_weapon_",
							num2
						}), 0);
						string @string = PlayerPrefs.GetString(string.Concat(new object[]
						{
							"loadout_",
							num,
							"_weapon_",
							num2,
							"_name"
						}), "");
						if (@int < this._game.allWeapons.Length && this._game.allWeapons[@int].weaponName == @string)
						{
							this._allLoadouts[num].loadoutWeapons[num2].weaponID = @int;
							int[] intArray = PlayerPrefsExtended.GetIntArray(string.Concat(new object[]
							{
								"loadout_",
								num,
								"_weapon_",
								num2,
								"_attachments"
							}), 0, 0);
							Kit_WeaponRenderer component = this._game.allWeapons[@int].firstPersonPrefab.GetComponent<Kit_WeaponRenderer>();
							if (component)
							{
								if (intArray.Length == component.attachmentSlots.Length)
								{
									this._allLoadouts[num].loadoutWeapons[num2].attachments = intArray;
									for (int k = 0; k < this._allLoadouts[num].loadoutWeapons[num2].attachments.Length; k++)
									{
										int num3 = k;
										this._allLoadouts[num].loadoutWeapons[num2].attachments[num3] = Mathf.Clamp(this._allLoadouts[num].loadoutWeapons[num2].attachments[num3], 0, component.attachmentSlots[num3].attachments.Length - 1);
									}
								}
								else
								{
									this._allLoadouts[num].loadoutWeapons[num2].attachments = new int[component.attachmentSlots.Length];
								}
							}
							else
							{
								this._allLoadouts[num].loadoutWeapons[num2].attachments = new int[0];
							}
						}
					}
				}
				if (PlayerPrefs.GetInt("loadout_" + num + "_teams", 0) == this._allLoadouts[num].teamLoadout.Length)
				{
					for (int l = 0; l < this._allLoadouts[num].teamLoadout.Length; l++)
					{
						int num4 = l;
						int int2 = PlayerPrefs.GetInt(string.Concat(new object[]
						{
							"loadout_",
							num,
							"_pm_",
							num4
						}), 0);
						if (int2 < this._game.allPvpTeams[num4].playerModels.Length)
						{
							string string2 = PlayerPrefs.GetString(string.Concat(new object[]
							{
								"loadout_",
								num,
								"_pm_",
								num4,
								"_name"
							}), "");
							if (this._game.allPvpTeams[num4].playerModels[int2].displayName == string2)
							{
								this._allLoadouts[num].teamLoadout[num4].playerModelID = int2;
								Kit_ThirdPersonPlayerModel component2 = this._game.allPvpTeams[num4].playerModels[int2].prefab.GetComponent<Kit_ThirdPersonPlayerModel>();
								int[] intArray2 = PlayerPrefsExtended.GetIntArray(string.Concat(new object[]
								{
									"loadout_",
									num,
									"_pm_",
									num4,
									"_customization"
								}), 0, 0);
								if (intArray2.Length == component2.customizationSlots.Length)
								{
									this._allLoadouts[num].teamLoadout[num4].playerModelCustomizations = intArray2;
									for (int m = 0; m < this._allLoadouts[num].teamLoadout[num4].playerModelCustomizations.Length; m++)
									{
										int num5 = m;
										this._allLoadouts[num].teamLoadout[num4].playerModelCustomizations[num5] = Mathf.Clamp(this._allLoadouts[num].teamLoadout[num4].playerModelCustomizations[num5], 0, component2.customizationSlots[num5].customizations.Length - 1);
									}
								}
								else
								{
									this._allLoadouts[num].teamLoadout[num4].playerModelCustomizations = new int[component2.customizationSlots.Length];
								}
							}
						}
					}
				}
			}
		}
		int int3 = PlayerPrefs.GetInt("loadoutSelected", 0);
		this.CurrentSelectedteamForPlayerModel = PlayerPrefs.GetInt("teamSelected", 0);
		this.CurrentSelectedteamForPlayerModel = Mathf.Clamp(this.CurrentSelectedteamForPlayerModel, 0, this._game.allPvpTeams.Length - 1);
		if (int3 < this._allLoadouts.Length)
		{
			this._currentlySelectedLoadout = int3;
		}
		this._wasLoaded = true;
	}

	// Token: 0x06000245 RID: 581 RVA: 0x0000C6EC File Offset: 0x0000A8EC
	public void SaveAttachments(int weapon, int[] attachments)
	{
		PlayerPrefsExtended.SetIntArray("weapon_" + this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[weapon].weaponID + "_attachment", attachments);
		Debug.Log("saveweapon_" + this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[weapon].weaponID + "_attachment");
	}

	// Token: 0x06000246 RID: 582 RVA: 0x0000C75F File Offset: 0x0000A95F
	public int[] LoadAttachment(int weapon, int attachmentCount)
	{
		return PlayerPrefsExtended.GetIntArray("weapon_" + this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[weapon].weaponID + "_attachment", 0, attachmentCount);
	}

	// Token: 0x06000247 RID: 583 RVA: 0x0000C798 File Offset: 0x0000A998
	public void Save()
	{
		if (this._wasLoaded)
		{
			for (int i = 0; i < this._allLoadouts.Length; i++)
			{
				int num = i;
				PlayerPrefs.SetInt("loadout_" + num + "_weapons", this._allLoadouts[num].loadoutWeapons.Length);
				for (int j = 0; j < this._allLoadouts[num].loadoutWeapons.Length; j++)
				{
					int num2 = j;
					if (StoreController.Instance.openItems.Contains(this._game.allWeapons[this._allLoadouts[num].loadoutWeapons[num2].weaponID].id))
					{
						PlayerPrefs.SetString(string.Concat(new object[]
						{
							"loadout_",
							num,
							"_weapon_",
							num2,
							"_name"
						}), this._game.allWeapons[this._allLoadouts[num].loadoutWeapons[num2].weaponID].weaponName);
						PlayerPrefs.SetInt(string.Concat(new object[]
						{
							"loadout_",
							num,
							"_weapon_",
							num2
						}), this._allLoadouts[num].loadoutWeapons[num2].weaponID);
						PlayerPrefsExtended.SetIntArray(string.Concat(new object[]
						{
							"loadout_",
							num,
							"_weapon_",
							num2,
							"_attachments"
						}), this._allLoadouts[num].loadoutWeapons[num2].attachments);
					}
				}
				PlayerPrefs.SetInt("loadout_" + num + "_teams", this._allLoadouts[num].teamLoadout.Length);
				for (int k = 0; k < this._allLoadouts[num].teamLoadout.Length; k++)
				{
					int num3 = k;
					PlayerPrefs.SetString(string.Concat(new object[]
					{
						"loadout_",
						num,
						"_pm_",
						num3,
						"_name"
					}), this._game.allPvpTeams[num3].playerModels[this._allLoadouts[num].teamLoadout[num3].playerModelID].displayName);
					PlayerPrefs.SetInt(string.Concat(new object[]
					{
						"loadout_",
						num,
						"_pm_",
						num3
					}), this._allLoadouts[num].teamLoadout[num3].playerModelID);
					PlayerPrefsExtended.SetIntArray(string.Concat(new object[]
					{
						"loadout_",
						num,
						"_pm_",
						num3,
						"_customization"
					}), this._allLoadouts[num].teamLoadout[num3].playerModelCustomizations);
				}
			}
			PlayerPrefs.SetInt("loadoutSelected", this._currentlySelectedLoadout);
			PlayerPrefs.SetInt("teamSelected", this.CurrentSelectedteamForPlayerModel);
		}
	}

	// Token: 0x06000248 RID: 584 RVA: 0x0000CAB6 File Offset: 0x0000ACB6
	public Loadout GetSelecetedLoadout()
	{
		return this._allLoadouts[this._currentlySelectedLoadout];
	}

	// Token: 0x06000249 RID: 585 RVA: 0x0000CAC8 File Offset: 0x0000ACC8
	public void WeaponSelect(int slot, int id)
	{
		if (this._game.allWeapons[id].IsWeaponUnlocked(this._game))
		{
			Kit_WeaponRenderer component = this._game.allWeapons[id].firstPersonPrefab.GetComponent<Kit_WeaponRenderer>();
			if (this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[slot].weaponID != id)
			{
				this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[slot].weaponID = id;
				this.CurrentSelectedWeaponCategory = slot;
				if (component)
				{
					this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[slot].attachments = this.LoadAttachment(slot, component.attachmentSlots.Length);
					return;
				}
				this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[slot].attachments = new int[0];
				return;
			}
			else if (component)
			{
				if (this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[slot].attachments.Length != component.attachmentSlots.Length)
				{
					this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[slot].attachments = new int[component.attachmentSlots.Length];
					return;
				}
				for (int i = 0; i < this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[slot].attachments.Length; i++)
				{
					this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[slot].attachments[i] = Mathf.Clamp(this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[slot].attachments[i], 0, component.attachmentSlots[i].attachments.Length - 1);
				}
				return;
			}
			else
			{
				this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[slot].attachments = new int[0];
			}
		}
	}

	// Token: 0x0600024A RID: 586 RVA: 0x0000CC84 File Offset: 0x0000AE84
	public void SelectAttachment(int category, int attachmentSlot, int attachment)
	{
		Debug.Log("Select attachment " + attachment);
		this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[category].attachments[attachmentSlot] = attachment;
		this.SaveAttachments(category, this._allLoadouts[this._currentlySelectedLoadout].loadoutWeapons[category].attachments);
	}

	// Token: 0x0600024B RID: 587 RVA: 0x0000CCE2 File Offset: 0x0000AEE2
	public void PlayerModelSelectTeam(int team)
	{
		this.CurrentSelectedteamForPlayerModel = team;
	}

	// Token: 0x0600024C RID: 588 RVA: 0x0000CCEC File Offset: 0x0000AEEC
	public void PlayerModelSelect(int id)
	{
		Kit_ThirdPersonPlayerModel component = this._game.allPvpTeams[this.CurrentSelectedteamForPlayerModel].playerModels[id].prefab.GetComponent<Kit_ThirdPersonPlayerModel>();
		if (this._allLoadouts[this._currentlySelectedLoadout].teamLoadout[this.CurrentSelectedteamForPlayerModel].playerModelID != id)
		{
			this._allLoadouts[this._currentlySelectedLoadout].teamLoadout[this.CurrentSelectedteamForPlayerModel].playerModelID = id;
			this._allLoadouts[this._currentlySelectedLoadout].teamLoadout[this.CurrentSelectedteamForPlayerModel].playerModelCustomizations = new int[component.customizationSlots.Length];
			return;
		}
		if (this._allLoadouts[this._currentlySelectedLoadout].teamLoadout[this.CurrentSelectedteamForPlayerModel].playerModelCustomizations.Length != component.customizationSlots.Length)
		{
			this._allLoadouts[this._currentlySelectedLoadout].teamLoadout[this.CurrentSelectedteamForPlayerModel].playerModelCustomizations = new int[component.customizationSlots.Length];
			return;
		}
		for (int i = 0; i < this._allLoadouts[this._currentlySelectedLoadout].teamLoadout[this.CurrentSelectedteamForPlayerModel].playerModelCustomizations.Length; i++)
		{
			int num = i;
			this._allLoadouts[this._currentlySelectedLoadout].teamLoadout[this.CurrentSelectedteamForPlayerModel].playerModelCustomizations[num] = Mathf.Clamp(this._allLoadouts[this._currentlySelectedLoadout].teamLoadout[this.CurrentSelectedteamForPlayerModel].playerModelCustomizations[num], 0, component.customizationSlots[num].customizations.Length - 1);
		}
	}

	// Token: 0x04000198 RID: 408
	private static LoadoutService _instance;

	// Token: 0x04000199 RID: 409
	private Loadout[] _allLoadouts;

	// Token: 0x0400019A RID: 410
	private int _amountOfLoadouts = 5;

	// Token: 0x0400019B RID: 411
	private int _currentlySelectedLoadout;

	// Token: 0x0400019C RID: 412
	private bool _wasLoaded;

	// Token: 0x0400019D RID: 413
	private Kit_GameInformation _game;

	// Token: 0x0400019E RID: 414
	private Dictionary<int, int[]> _attachmentsDict;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x02000059 RID: 89
public class LocalizationController
{
	// Token: 0x14000005 RID: 5
	// (add) Token: 0x0600024D RID: 589 RVA: 0x0000CE64 File Offset: 0x0000B064
	// (remove) Token: 0x0600024E RID: 590 RVA: 0x0000CE9C File Offset: 0x0000B09C
	public event Action OnLanguageChanged = delegate()
	{
	};

	// Token: 0x1700001F RID: 31
	// (get) Token: 0x0600024F RID: 591 RVA: 0x0000CED1 File Offset: 0x0000B0D1
	public static LocalizationController Instance
	{
		get
		{
			if (LocalizationController._instance == null)
			{
				LocalizationController._instance = new LocalizationController();
			}
			return LocalizationController._instance;
		}
	}

	// Token: 0x17000020 RID: 32
	// (get) Token: 0x06000250 RID: 592 RVA: 0x0000CEE9 File Offset: 0x0000B0E9
	public int CurrentLanguage
	{
		get
		{
			return this._currentLanguage;
		}
	}

	// Token: 0x06000251 RID: 593 RVA: 0x0000CEF4 File Offset: 0x0000B0F4
	private LocalizationController()
	{
		this.LoadLanguage();
	}

	// Token: 0x06000252 RID: 594 RVA: 0x00005F78 File Offset: 0x00004178
	public void Initialize()
	{
	}

	// Token: 0x06000253 RID: 595 RVA: 0x0000CF5C File Offset: 0x0000B15C
	public void ChangeLanguage(int index)
	{
		if (this._currentLanguage != index)
		{
			this._currentLanguage = index;
			LanguageModel languageModel = Resources.Load<LanguageModel>(this._languagesPaths[this._currentLanguage]);
			this.CreateContent(languageModel);
			this.SaveLanguage(index);
			this.OnLanguageChanged();
		}
	}

	// Token: 0x06000254 RID: 596 RVA: 0x0000CFA5 File Offset: 0x0000B1A5
	public string GetText(string group, string key)
	{
		return this._content[group][key];
	}

	// Token: 0x06000255 RID: 597 RVA: 0x0000CFB9 File Offset: 0x0000B1B9
	public void OnLanguageChangedInvoke()
	{
		this.OnLanguageChanged();
	}

	// Token: 0x06000256 RID: 598 RVA: 0x0000CFC8 File Offset: 0x0000B1C8
	private void LoadLanguage()
	{
		if (PlayerPrefs.HasKey(this._languageKey))
		{
			this._currentLanguage = PlayerPrefs.GetInt(this._languageKey);
			LanguageModel languageModel = Resources.Load<LanguageModel>(this._languagesPaths[this._currentLanguage]);
			this.CreateContent(languageModel);
			return;
		}
		this._currentLanguage = 1;
		LanguageModel languageModel2 = Resources.Load<LanguageModel>(this._languagesPaths[1]);
		this.CreateContent(languageModel2);
	}

	// Token: 0x06000257 RID: 599 RVA: 0x0000D02C File Offset: 0x0000B22C
	private void CreateContent(LanguageModel languageModel)
	{
		this._content = new Dictionary<string, Dictionary<string, string>>();
		for (int i = 0; i < languageModel.Groups.Count; i++)
		{
			string name = languageModel.Groups[i].Name;
			Dictionary<string, string> dictionary = new Dictionary<string, string>();
			for (int j = 0; j < languageModel.Groups[i].Keyes.Count; j++)
			{
				string name2 = languageModel.Groups[i].Keyes[j].Name;
				string text = languageModel.Groups[i].Keyes[j].Text;
				dictionary.Add(name2, text);
			}
			this._content.Add(name, dictionary);
		}
	}

	// Token: 0x06000258 RID: 600 RVA: 0x0000D0EC File Offset: 0x0000B2EC
	private void SaveLanguage(int index)
	{
		PlayerPrefs.SetInt(this._languageKey, index);
		PlayerPrefs.Save();
	}

	// Token: 0x040001A2 RID: 418
	private static LocalizationController _instance;

	// Token: 0x040001A3 RID: 419
	private string[] _languagesPaths = new string[]
	{
		"Rus",
		"Eng"
	};

	// Token: 0x040001A4 RID: 420
	private Dictionary<string, Dictionary<string, string>> _content;

	// Token: 0x040001A5 RID: 421
	private string _languageKey = "LanguageIndex";

	// Token: 0x040001A6 RID: 422
	private int _currentLanguage;
}
﻿using System;
using TMPro;

// Token: 0x02000087 RID: 135
[Serializable]
public class LocalizationElement
{
	// Token: 0x04000280 RID: 640
	public string Key;

	// Token: 0x04000281 RID: 641
	public TextMeshProUGUI Text;
}
﻿using System;
using UnityEngine;

// Token: 0x020000DF RID: 223
public class LocalizationView : MonoBehaviour
{
	// Token: 0x06000582 RID: 1410 RVA: 0x00019122 File Offset: 0x00017322
	private void OnEnable()
	{
		LocalizationController.Instance.OnLanguageChanged += this.Localize;
		this.Localize();
	}

	// Token: 0x06000583 RID: 1411 RVA: 0x00019140 File Offset: 0x00017340
	private void OnDisable()
	{
		LocalizationController.Instance.OnLanguageChanged -= this.Localize;
	}

	// Token: 0x06000584 RID: 1412 RVA: 0x00019158 File Offset: 0x00017358
	public void Localize()
	{
		if (this.Elements == null)
		{
			return;
		}
		for (int i = 0; i < this.Elements.Length; i++)
		{
			LocalizationElement localizationElement = this.Elements[i];
			localizationElement.Text.text = LocalizationController.Instance.GetText(this.Group, localizationElement.Key);
		}
	}

	// Token: 0x040004CD RID: 1229
	public string Group;

	// Token: 0x040004CE RID: 1230
	public LocalizationElement[] Elements;
}
﻿using System;
using Photon.Realtime;

// Token: 0x0200005A RID: 90
public abstract class LoginBehaviourBase
{
	// Token: 0x06000259 RID: 601
	public abstract void Initialize(MenuController menuController);

	// Token: 0x0600025A RID: 602 RVA: 0x00005F78 File Offset: 0x00004178
	public virtual void AfterLogin()
	{
	}

	// Token: 0x0600025B RID: 603 RVA: 0x0000D0FF File Offset: 0x0000B2FF
	public virtual AuthenticationValues GetAuthenticationValues()
	{
		return new AuthenticationValues();
	}

	// Token: 0x0600025C RID: 604 RVA: 0x00005F78 File Offset: 0x00004178
	public virtual void OnConnectedToMaster()
	{
	}
}
﻿using System;
using MarsFPSKit;
using Photon.Pun;
using Photon.Realtime;
using UnityEngine;

// Token: 0x0200005B RID: 91
public class LoginController
{
	// Token: 0x17000021 RID: 33
	// (get) Token: 0x0600025E RID: 606 RVA: 0x0000D106 File Offset: 0x0000B306
	public static LoginController Instance
	{
		get
		{
			if (LoginController._instance == null)
			{
				LoginController._instance = new LoginController(new LoginSteam());
			}
			return LoginController._instance;
		}
	}

	// Token: 0x17000022 RID: 34
	// (get) Token: 0x0600025F RID: 607 RVA: 0x0000D123 File Offset: 0x0000B323
	public string PreviousUserName
	{
		get
		{
			return this._previousUsername;
		}
	}

	// Token: 0x17000023 RID: 35
	// (get) Token: 0x06000260 RID: 608 RVA: 0x0000D12B File Offset: 0x0000B32B
	// (set) Token: 0x06000261 RID: 609 RVA: 0x0000D133 File Offset: 0x0000B333
	public bool IsLoggedIn { get; private set; }

	// Token: 0x06000262 RID: 610 RVA: 0x0000D13C File Offset: 0x0000B33C
	private LoginController(LoginBehaviourBase loginBehaviour)
	{
		this._loginBehaviour = loginBehaviour;
	}

	// Token: 0x06000263 RID: 611 RVA: 0x0000D156 File Offset: 0x0000B356
	public void Initialize(MenuController menuController)
	{
		this._menuController = menuController;
		this._loginBehaviour.Initialize(menuController);
	}

	// Token: 0x06000264 RID: 612 RVA: 0x0000D16C File Offset: 0x0000B36C
	public void OnLoginButtonClick(string userName)
	{
		this._previousUsername = userName;
		Kit_GameSettings.userName = this._previousUsername;
		PlayerPrefs.SetString("previousUsername", this._previousUsername);
		PhotonNetwork.LocalPlayer.NickName = userName;
		MenuController menuController = this._menuController;
		if (menuController != null)
		{
			menuController.LoggedIn(this._previousUsername);
		}
		this.IsLoggedIn = true;
	}

	// Token: 0x06000265 RID: 613 RVA: 0x0000D1C4 File Offset: 0x0000B3C4
	public AuthenticationValues GetAuthenticationValues()
	{
		new AuthenticationValues().UserId = this._previousUsername;
		return this._loginBehaviour.GetAuthenticationValues();
	}

	// Token: 0x06000266 RID: 614 RVA: 0x0000D1E1 File Offset: 0x0000B3E1
	public void OnLoggedIn()
	{
		this.IsLoggedIn = true;
	}

	// Token: 0x040001A7 RID: 423
	public string _previousUsername = string.Empty;

	// Token: 0x040001A8 RID: 424
	private MenuController _menuController;

	// Token: 0x040001A9 RID: 425
	private static LoginController _instance;

	// Token: 0x040001AA RID: 426
	private LoginBehaviourBase _loginBehaviour;
}
﻿using System;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000DC RID: 220
public class LoginMenuScreenView : BaseMenuView
{
	// Token: 0x06000576 RID: 1398 RVA: 0x00018FF3 File Offset: 0x000171F3
	private void OnEnable()
	{
		this._loggingButton.onClick.AddListener(delegate()
		{
			this.LoginButton();
		});
	}

	// Token: 0x06000577 RID: 1399 RVA: 0x00019011 File Offset: 0x00017211
	private void OnDisable()
	{
		this._loggingButton.onClick.RemoveListener(delegate()
		{
			this.LoginButton();
		});
	}

	// Token: 0x06000578 RID: 1400 RVA: 0x0001902F File Offset: 0x0001722F
	public override void MenuInit(ISwitchableMenu menuController)
	{
		base.MenuInit(menuController);
		this._userNameInputField.text = LoginController.Instance.PreviousUserName;
	}

	// Token: 0x06000579 RID: 1401 RVA: 0x0001904D File Offset: 0x0001724D
	public void LoginButton()
	{
		LoginController.Instance.OnLoginButtonClick(this._userNameInputField.text);
	}

	// Token: 0x040004C3 RID: 1219
	[SerializeField]
	private TMP_InputField _userNameInputField;

	// Token: 0x040004C4 RID: 1220
	[SerializeField]
	private Button _loggingButton;
}
﻿using System;
using Photon.Realtime;
using UnityEngine;

// Token: 0x0200005C RID: 92
public class LoginPlayerPrefs : LoginBehaviourBase
{
	// Token: 0x06000267 RID: 615 RVA: 0x0000D1EC File Offset: 0x0000B3EC
	public override void Initialize(MenuController menuController)
	{
		this._menuController = menuController;
		if (LoginController.Instance._previousUsername != string.Empty)
		{
			return;
		}
		this._menuController = menuController;
		if (PlayerPrefs.HasKey("previousUsername"))
		{
			LoginController.Instance._previousUsername = PlayerPrefs.GetString("previousUsername");
		}
	}

	// Token: 0x06000268 RID: 616 RVA: 0x0000D23E File Offset: 0x0000B43E
	public override AuthenticationValues GetAuthenticationValues()
	{
		return new AuthenticationValues
		{
			UserId = LoginController.Instance._previousUsername
		};
	}

	// Token: 0x040001AC RID: 428
	private MenuController _menuController;
}
﻿using System;
using System.Text;
using MarsFPSKit;
using MarsFPSKit.Integrations.Steam;
using Photon.Realtime;
using Steamworks;
using UnityEngine;

// Token: 0x0200005D RID: 93
public class LoginSteam : LoginBehaviourBase
{
	// Token: 0x0600026A RID: 618 RVA: 0x0000D25D File Offset: 0x0000B45D
	public override void Initialize(MenuController mm)
	{
		if (Kit_SteamManager.Initialized)
		{
			Kit_GameSettings.userName = SteamFriends.GetPersonaName();
			mm.LoggedIn(SteamFriends.GetPersonaName());
			PlayerPrefs.SetString("previousUsername", SteamFriends.GetPersonaName());
		}
	}

	// Token: 0x0600026B RID: 619 RVA: 0x0000D28C File Offset: 0x0000B48C
	public override AuthenticationValues GetAuthenticationValues()
	{
		if (this.useSteamAuthentication)
		{
			AuthenticationValues authenticationValues = new AuthenticationValues();
			authenticationValues.UserId = SteamUser.GetSteamID().ToString();
			authenticationValues.AuthType = CustomAuthenticationType.Steam;
			authenticationValues.AddAuthParameter("ticket", this.GetSteamAuthTicket(out this.authenticationTicket));
			return authenticationValues;
		}
		AuthenticationValues authenticationValues2 = new AuthenticationValues();
		if (Kit_SteamManager.Initialized)
		{
			authenticationValues2.UserId = UnityEngine.Random.Range(0, 1000).ToString();
		}
		return authenticationValues2;
	}

	// Token: 0x0600026C RID: 620 RVA: 0x0000D308 File Offset: 0x0000B508
	public string GetSteamAuthTicket(out HAuthTicket hAuthTicket)
	{
		byte[] array = new byte[1024];
		uint num;
		hAuthTicket = SteamUser.GetAuthSessionTicket(array, array.Length, out num);
		Array.Resize<byte>(ref array, (int)num);
		StringBuilder stringBuilder = new StringBuilder();
		int num2 = 0;
		while ((long)num2 < (long)((ulong)num))
		{
			stringBuilder.AppendFormat("{0:x2}", array[num2]);
			num2++;
		}
		return stringBuilder.ToString();
	}

	// Token: 0x0600026D RID: 621 RVA: 0x0000D367 File Offset: 0x0000B567
	public override void OnConnectedToMaster()
	{
		if (this.useSteamAuthentication)
		{
			SteamUser.CancelAuthTicket(this.authenticationTicket);
		}
	}

	// Token: 0x040001AD RID: 429
	private bool useSteamAuthentication;

	// Token: 0x040001AE RID: 430
	private HAuthTicket authenticationTicket;

	// Token: 0x040001AF RID: 431
	private ObserveProcess _observeProcess;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using Photon.Pun;
using UnityEngine;

// Token: 0x020000A7 RID: 167
[CreateAssetMenu(menuName = "MarsFPSKit/Weapons/Magnetic Bomb Script")]
public class MagneticBombScript : Kit_ModernGrenadeScript
{
	// Token: 0x060003DF RID: 991 RVA: 0x00011684 File Offset: 0x0000F884
	private bool GetCrashWall(Kit_PlayerBehaviour pb)
	{
		RaycastHit raycastHit;
		if (Physics.Raycast(pb.playerCameraTransform.position, pb.playerCameraTransform.forward, out raycastHit, this.Distance, this.Layers) && raycastHit.collider.GetComponent<WallCrashView>())
		{
			pb.TargetCrashWallView = raycastHit.collider.GetComponent<WallCrashView>();
			return true;
		}
		return false;
	}

	// Token: 0x060003E0 RID: 992 RVA: 0x000116EC File Offset: 0x0000F8EC
	public override bool SupportsQuickUse(Kit_PlayerBehaviour pb, object runtimeData)
	{
		if ((this.grenadeMode == GrenadeMode.Both || this.grenadeMode == GrenadeMode.QuickUse) && runtimeData != null && runtimeData.GetType() == typeof(GrenadeControllerRuntimeData))
		{
			GrenadeControllerRuntimeData grenadeControllerRuntimeData = runtimeData as GrenadeControllerRuntimeData;
			if (pb.TargetCrashWallView)
			{
				pb.TargetCrashWallView.CrashWallRequest();
				pb.TargetCrashWallView = null;
				return false;
			}
			if (grenadeControllerRuntimeData.amountOfGrenadesLeft > 0 && this.GetCrashWall(pb))
			{
				return true;
			}
		}
		return false;
	}

	// Token: 0x060003E1 RID: 993 RVA: 0x00011764 File Offset: 0x0000F964
	public override float EndQuickUse(Kit_PlayerBehaviour pb, object runtimeData)
	{
		if (runtimeData != null && runtimeData.GetType() == typeof(GrenadeControllerRuntimeData))
		{
			GrenadeControllerRuntimeData grenadeControllerRuntimeData = runtimeData as GrenadeControllerRuntimeData;
			if (this.grenadePrefab)
			{
				object[] array = new object[2];
				array[0] = pb.isBot;
				if (pb.isBot)
				{
					array[1] = pb.botId;
				}
				else
				{
					array[1] = pb.photonView.OwnerActorNr;
				}
				if (pb.photonView.IsMine)
				{
					GameObject gameObject = PhotonNetwork.Instantiate(this.grenadePrefab.name, pb.playerCameraTransform.position, pb.playerCameraTransform.rotation, 0, null);
					pb.TargetCrashWallView.AddMagneticBomb(gameObject);
					gameObject.GetComponent<MagneticBombView>().GoToPoint(pb.TargetCrashWallView.GetBombPlace(pb.playerCameraTransform.forward));
				}
			}
			else
			{
				Debug.LogError("No Grenade Prefab!");
			}
			grenadeControllerRuntimeData.amountOfGrenadesLeft--;
			if (pb.isFirstPersonActive)
			{
				if (grenadeControllerRuntimeData.grenadeRenderer.anim)
				{
					grenadeControllerRuntimeData.grenadeRenderer.anim.Play("Quick Throw", 0, 0f);
				}
				else if (grenadeControllerRuntimeData.grenadeRenderer.legacyAnim)
				{
					grenadeControllerRuntimeData.grenadeRenderer.legacyAnim.Play(grenadeControllerRuntimeData.grenadeRenderer.legacyAnimData.quickThrow);
				}
				grenadeControllerRuntimeData.sounds.clip = this.throwQuickSound;
				grenadeControllerRuntimeData.sounds.Play();
				pb.thirdPersonPlayerModel.PlayGrenadeAnimation(1);
			}
			if (pb.voiceManager)
			{
				pb.voiceManager.GrenadeThrown(pb, this.voiceGrenadeSoundID);
			}
			return this.quickUseThrowTime;
		}
		return 0f;
	}

	// Token: 0x060003E2 RID: 994 RVA: 0x00011928 File Offset: 0x0000FB28
	public override WeaponDisplayData GetWeaponDisplayData(Kit_PlayerBehaviour pb, object runtimeData)
	{
		if (runtimeData != null && runtimeData.GetType() == typeof(GrenadeControllerRuntimeData) && (runtimeData as GrenadeControllerRuntimeData).amountOfGrenadesLeft > 0 && this.grenadeMode == GrenadeMode.QuickUse)
		{
			return new WeaponDisplayData
			{
				sprite = this.weaponHudPicture,
				name = this.weaponName
			};
		}
		return null;
	}

	// Token: 0x060003E3 RID: 995 RVA: 0x00011984 File Offset: 0x0000FB84
	public override WeaponQuickUseDisplayData GetWeaponQuickUseDisplayData(Kit_PlayerBehaviour pb, object runtimeData)
	{
		if (runtimeData != null && runtimeData.GetType() == typeof(GrenadeControllerRuntimeData))
		{
			GrenadeControllerRuntimeData grenadeControllerRuntimeData = runtimeData as GrenadeControllerRuntimeData;
			if (grenadeControllerRuntimeData.amountOfGrenadesLeft > 0 && (this.grenadeMode == GrenadeMode.QuickUse || this.grenadeMode == GrenadeMode.Both))
			{
				return new WeaponQuickUseDisplayData
				{
					sprite = this.weaponQuickUsePicture,
					name = this.weaponName,
					amount = grenadeControllerRuntimeData.amountOfGrenadesLeft
				};
			}
		}
		return null;
	}

	// Token: 0x0400030B RID: 779
	public LayerMask Layers;

	// Token: 0x0400030C RID: 780
	public float Distance = 5f;
}
﻿using System;
using UnityEngine;

// Token: 0x020000A8 RID: 168
public class MagneticBombView : MonoBehaviour
{
	// Token: 0x060003E5 RID: 997 RVA: 0x00011A0A File Offset: 0x0000FC0A
	private void Awake()
	{
		this._lerpProcess = new LerpProcess();
		this._startPosition = base.transform.position;
	}

	// Token: 0x060003E6 RID: 998 RVA: 0x00011A28 File Offset: 0x0000FC28
	public void GoToPoint(Transform point)
	{
		Quaternion startRotation = base.transform.rotation;
		this._lerpProcess.StartProcess(delegate(float t)
		{
			float x = Mathf.Lerp(this._startPosition.x, point.position.x, t);
			float y = Mathf.Lerp(this._startPosition.y, point.position.y, t) + Mathf.Lerp(0f, this._height, Mathf.Sin(3.1415927f * t));
			float z = Mathf.Lerp(this._startPosition.z, point.position.z, t);
			this.transform.position = new Vector3(x, y, z);
			this.transform.rotation = Quaternion.Lerp(startRotation, point.rotation, t);
		}, this._time);
	}

	// Token: 0x0400030D RID: 781
	[SerializeField]
	private float _height;

	// Token: 0x0400030E RID: 782
	private LerpProcess _lerpProcess;

	// Token: 0x0400030F RID: 783
	private Vector3 _startPosition;

	// Token: 0x04000310 RID: 784
	private float _time = 0.5f;
}
﻿using System;
using MarsFPSKit.Weapons;

// Token: 0x02000088 RID: 136
public class MainMenuData
{
	// Token: 0x04000282 RID: 642
	public Kit_WeaponBase WeaponToCustomize;

	// Token: 0x04000283 RID: 643
	public bool IsTeamChangeble;
}
﻿using System;
using MarsFPSKit;
using UnityEngine;

// Token: 0x0200005E RID: 94
public class MainMenuMain : MonoBehaviour
{
	// Token: 0x0600026F RID: 623 RVA: 0x0000D37C File Offset: 0x0000B57C
	private void Awake()
	{
		if (MarsScreen.lockCursor)
		{
			MarsScreen.lockCursor = false;
		}
		this._menuController = new MenuController();
	}

	// Token: 0x06000270 RID: 624 RVA: 0x00005F78 File Offset: 0x00004178
	private void Start()
	{
	}

	// Token: 0x040001B0 RID: 432
	private MenuController _menuController;
}
﻿using System;
using MarsFPSKit;
using UnityEngine;

// Token: 0x020000E0 RID: 224
public class MainMenuMusicView : MonoBehaviour
{
	// Token: 0x06000586 RID: 1414 RVA: 0x000191AB File Offset: 0x000173AB
	private void OnEnable()
	{
		Kit_GameSettings.OnMusicVolumeChanged += this.ChangeMusicVolume;
	}

	// Token: 0x06000587 RID: 1415 RVA: 0x000191BE File Offset: 0x000173BE
	private void OnDisable()
	{
		Kit_GameSettings.OnMusicVolumeChanged -= this.ChangeMusicVolume;
	}

	// Token: 0x06000588 RID: 1416 RVA: 0x000191D1 File Offset: 0x000173D1
	private void ChangeMusicVolume(float volume)
	{
		this._musicSource.volume = volume;
	}

	// Token: 0x040004CF RID: 1231
	[SerializeField]
	private AudioSource _musicSource;
}
﻿using System;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000E1 RID: 225
public class MainMenuView : BaseMenuView
{
	// Token: 0x0600058A RID: 1418 RVA: 0x000191E0 File Offset: 0x000173E0
	private void OnEnable()
	{
		this._storeButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(MenuType.Store);
		});
		this._homeButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(MenuType.Statistics);
		});
		this._weaponsButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(MenuType.Weapons);
		});
		this._solderButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(MenuType.PlayerModel);
		});
		this._battlePassButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(MenuType.BattlePass);
		});
		this._optionsButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(MenuType.Options);
		});
		this._exitButton.onClick.AddListener(delegate()
		{
			Application.Quit();
		});
		this._tournamentsButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(MenuType.TournamentList);
		});
		this._hostGameButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(MenuType.HostGame);
		});
		this._browseGamesButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(MenuType.BrowseServers);
		});
		this._quickMatchButton.onClick.AddListener(delegate()
		{
			HostGameController.Instance.JoinRandomRoom();
		});
	}

	// Token: 0x0600058B RID: 1419 RVA: 0x00019348 File Offset: 0x00017548
	private void OnDisable()
	{
		this._homeButton.onClick.RemoveAllListeners();
		this._weaponsButton.onClick.RemoveAllListeners();
		this._solderButton.onClick.RemoveAllListeners();
		this._battlePassButton.onClick.RemoveAllListeners();
		this._optionsButton.onClick.RemoveAllListeners();
		this._exitButton.onClick.RemoveAllListeners();
		this._tournamentsButton.onClick.RemoveAllListeners();
		this._hostGameButton.onClick.RemoveAllListeners();
		this._browseGamesButton.onClick.RemoveAllListeners();
		this._quickMatchButton.onClick.RemoveAllListeners();
		this._storeButton.onClick.RemoveAllListeners();
	}

	// Token: 0x0600058C RID: 1420 RVA: 0x00019405 File Offset: 0x00017605
	public override void MenuInit(ISwitchableMenu menuController)
	{
		base.MenuInit(menuController);
	}

	// Token: 0x040004D0 RID: 1232
	[SerializeField]
	private Button _homeButton;

	// Token: 0x040004D1 RID: 1233
	[SerializeField]
	private Button _weaponsButton;

	// Token: 0x040004D2 RID: 1234
	[SerializeField]
	private Button _solderButton;

	// Token: 0x040004D3 RID: 1235
	[SerializeField]
	private Button _battlePassButton;

	// Token: 0x040004D4 RID: 1236
	[SerializeField]
	private Button _storeButton;

	// Token: 0x040004D5 RID: 1237
	[SerializeField]
	private Button _optionsButton;

	// Token: 0x040004D6 RID: 1238
	[SerializeField]
	private Button _exitButton;

	// Token: 0x040004D7 RID: 1239
	[SerializeField]
	private Button _tournamentsButton;

	// Token: 0x040004D8 RID: 1240
	[SerializeField]
	private Button _quickMatchButton;

	// Token: 0x040004D9 RID: 1241
	[SerializeField]
	private Button _browseGamesButton;

	// Token: 0x040004DA RID: 1242
	[SerializeField]
	private Button _hostGameButton;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.UI;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000B1 RID: 177
[CreateAssetMenu(menuName = "MarsFPSKit/Options/Gameplay/MapTransparent")]
public class MapTransperancyOption : Kit_OptionBase
{
	// Token: 0x06000415 RID: 1045 RVA: 0x00011FCE File Offset: 0x000101CE
	public override string GetDisplayName()
	{
		return "Map Transperancy";
	}

	// Token: 0x06000416 RID: 1046 RVA: 0x00011FCE File Offset: 0x000101CE
	public override string GetHoverText()
	{
		return "Map Transperancy";
	}

	// Token: 0x06000417 RID: 1047 RVA: 0x00011EE5 File Offset: 0x000100E5
	public override OptionType GetOptionType()
	{
		return OptionType.Slider;
	}

	// Token: 0x06000418 RID: 1048 RVA: 0x00011FD5 File Offset: 0x000101D5
	public override void OnSliderStart(TextMeshProUGUI txt, Slider slider)
	{
		Kit_GameSettings.MapTransparent = PlayerPrefs.GetFloat(this._prefsKey, this._default);
		slider.SetValueWithoutNotify(Kit_GameSettings.MapTransparent);
	}

	// Token: 0x06000419 RID: 1049 RVA: 0x00011FF8 File Offset: 0x000101F8
	public override void OnSliderChange(TextMeshProUGUI txt, float newValue)
	{
		Kit_GameSettings.MapTransparent = newValue;
		PlayerPrefs.SetFloat(this._prefsKey, newValue);
	}

	// Token: 0x04000322 RID: 802
	private string _prefsKey = "MapTransperancy";

	// Token: 0x04000323 RID: 803
	private float _default = 0.5f;
}
﻿using System;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000DD RID: 221
public class MapViewer : MonoBehaviour
{
	// Token: 0x0600057D RID: 1405 RVA: 0x0001906C File Offset: 0x0001726C
	private void OnEnable()
	{
		this._lerp = new LerpProcess();
	}

	// Token: 0x0600057E RID: 1406 RVA: 0x0001907C File Offset: 0x0001727C
	public void ChangeMapImage(Sprite mapImage)
	{
		if (this._lerp == null)
		{
			this._lerp = new LerpProcess();
		}
		if (this._lerp.IsBusy)
		{
			this._lerp.EndProcess();
		}
		bool switched = false;
		this._lerp.StartProcess(delegate(float t)
		{
			this._blackImage.color = Color.Lerp(Color.clear, Color.black, this._curve.Evaluate(t));
			if (!switched && t >= 0.5f)
			{
				this._mapImage.sprite = mapImage;
				switched = true;
			}
		}, this._switchTime);
	}

	// Token: 0x040004C5 RID: 1221
	[SerializeField]
	private Image _blackImage;

	// Token: 0x040004C6 RID: 1222
	[SerializeField]
	private Image _mapImage;

	// Token: 0x040004C7 RID: 1223
	[SerializeField]
	private float _switchTime = 0.3f;

	// Token: 0x040004C8 RID: 1224
	[SerializeField]
	private AnimationCurve _curve;

	// Token: 0x040004C9 RID: 1225
	private LerpProcess _lerp;
}
﻿using System;
using MarsFPSKit;
using Newtonsoft.Json;
using Photon.Pun;
using UnityEngine;

// Token: 0x0200005F RID: 95
public class MenuController : ISwitchableMenu
{
	// Token: 0x17000024 RID: 36
	// (get) Token: 0x06000272 RID: 626 RVA: 0x0000D396 File Offset: 0x0000B596
	// (set) Token: 0x06000273 RID: 627 RVA: 0x0000D39E File Offset: 0x0000B59E
	public MainMenuData MainMenuData { get; set; } = new MainMenuData();

	// Token: 0x06000274 RID: 628 RVA: 0x0000D3A7 File Offset: 0x0000B5A7
	public MenuController()
	{
		this.SetInit();
	}

	// Token: 0x06000275 RID: 629 RVA: 0x0000D3C0 File Offset: 0x0000B5C0
	public void LoggedIn(string userName)
	{
		if (!this._wasLoggedIn)
		{
			this._wasLoggedIn = true;
			PhotonNetwork.LocalPlayer.NickName = userName;
			RegionController.Instance.OnLoggedIn();
			this._game.statistics.OnStart();
			if (!this._wasLevelingInizialized)
			{
				if (this._game.leveling)
				{
					this._game.leveling.Initialize();
				}
				this._wasLevelingInizialized = true;
			}
			MyFriendsManager myFriendsManager = this._myFriendsManager;
			if (myFriendsManager != null)
			{
				myFriendsManager.AfterLogin();
			}
			LoginController.Instance.OnLoggedIn();
		}
	}

	// Token: 0x06000276 RID: 630 RVA: 0x0000D450 File Offset: 0x0000B650
	private void SetInit()
	{
		this._menues = UnityEngine.Object.FindObjectsOfType<BaseMenuView>();
		this._game = Resources.Load<Kit_GameInformation>("Game");
		this._tournamentsScreenController = new TournamentsScreenController();
		this.InitializeAllControllers();
		this.InitializeAllMenues();
		this.HideAllMenus();
		this.SetStartMenu();
		this._myFriendsManager = UnityEngine.Object.FindObjectOfType<MyFriendsManager>();
	}

	// Token: 0x06000277 RID: 631 RVA: 0x0000D4A8 File Offset: 0x0000B6A8
	private void InitializeAllControllers()
	{
		DataSender.Instance.Initialize();
		LoginController.Instance.Initialize(this);
		RegionController.Instance.Initialize();
		LoadoutService.Instance.Initialize();
		SteamFriendsController.Instance.Init();
		BattlePassController.Instance.Initialize();
		StoreController.Instance.Initialize(this._game);
		TournamentsController.Instance.Initialize();
		this._tournamentsScreenController.Initialize();
		HostGameController.Instance.Initialize();
		AccountScreenController.Instance.Initialize(this._game);
		LocalizationController.Instance.Initialize();
	}

	// Token: 0x06000278 RID: 632 RVA: 0x0000D53C File Offset: 0x0000B73C
	private void InitializeAllMenues()
	{
		for (int i = 0; i < this._menues.Length; i++)
		{
			this._menues[i].MenuInit(this);
		}
	}

	// Token: 0x06000279 RID: 633 RVA: 0x0000D56C File Offset: 0x0000B76C
	private void HideAllMenus()
	{
		for (int i = 0; i < this._menues.Length; i++)
		{
			this._menues[i].HideMenu(false);
		}
	}

	// Token: 0x0600027A RID: 634 RVA: 0x0000D59C File Offset: 0x0000B79C
	private BaseMenuView FindMenuByMenuType(MenuType menuType)
	{
		BaseMenuView result = null;
		for (int i = 0; i < this._menues.Length; i++)
		{
			if (this._menues[i].MenuType == menuType)
			{
				result = this._menues[i];
				break;
			}
		}
		return result;
	}

	// Token: 0x0600027B RID: 635 RVA: 0x0000D5DC File Offset: 0x0000B7DC
	private void SetStartMenu()
	{
		if (LoginController.Instance.IsLoggedIn)
		{
			this._currentMenu = this.FindMenuByMenuType(MenuType.Main);
			BaseMenuView currentMenu = this._currentMenu;
			if (currentMenu != null)
			{
				currentMenu.ShowMenu(false);
			}
			this.LoggedIn(LoginController.Instance.PreviousUserName);
			return;
		}
		this._currentMenu = this.FindMenuByMenuType(MenuType.Login);
		BaseMenuView currentMenu2 = this._currentMenu;
		if (currentMenu2 == null)
		{
			return;
		}
		currentMenu2.ShowMenu(false);
	}

	// Token: 0x0600027C RID: 636 RVA: 0x0000D644 File Offset: 0x0000B844
	private void TestWebHook()
	{
		TournamentModel tournamentModel = new TournamentModel
		{
			Name = "Турнир 1",
			Date = 123,
			ID = 100,
			IsEnded = false,
			TicketPrice = 1000,
			PrizeFund = 1000
		};
		JsonConvert.SerializeObject(tournamentModel);
		DataSender.Instance.TestWebHooks(tournamentModel);
	}

	// Token: 0x0600027D RID: 637 RVA: 0x0000D6A4 File Offset: 0x0000B8A4
	public void SwitchMenu(MenuType menu)
	{
		BaseMenuView baseMenuView = this.FindMenuByMenuType(menu);
		if (baseMenuView && baseMenuView != this._currentMenu)
		{
			BaseMenuView currentMenu = this._currentMenu;
			if (currentMenu != null)
			{
				currentMenu.HideMenu(true);
			}
			baseMenuView.ShowMenu(true);
			this._currentMenu = baseMenuView;
		}
	}

	// Token: 0x0600027E RID: 638 RVA: 0x0000D6F0 File Offset: 0x0000B8F0
	public void SwitchToPreviousMenu()
	{
		MenuType previousMenu = this._currentMenu.PreviousMenu;
		if (previousMenu == MenuType.None)
		{
			return;
		}
		this.SwitchMenu(previousMenu);
	}

	// Token: 0x0600027F RID: 639 RVA: 0x0000D716 File Offset: 0x0000B916
	public void HideCurrentMenu()
	{
		BaseMenuView currentMenu = this._currentMenu;
		if (currentMenu == null)
		{
			return;
		}
		currentMenu.HideMenu(true);
	}

	// Token: 0x040001B1 RID: 433
	private MyFriendsManager _myFriendsManager;

	// Token: 0x040001B2 RID: 434
	private BaseMenuView[] _menues;

	// Token: 0x040001B3 RID: 435
	private BaseMenuView _currentMenu;

	// Token: 0x040001B4 RID: 436
	private Kit_GameInformation _game;

	// Token: 0x040001B5 RID: 437
	private bool _wasLevelingInizialized;

	// Token: 0x040001B6 RID: 438
	private bool _wasLoggedIn;

	// Token: 0x040001B7 RID: 439
	private TournamentsScreenController _tournamentsScreenController;

	// Token: 0x040001B8 RID: 440
	private AccountScreenController _accountScreenController;
}
﻿using System;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.UI;

// Token: 0x020000A9 RID: 169
public abstract class MenuMessageElement : MonoBehaviour
{
	// Token: 0x060003E8 RID: 1000 RVA: 0x00011A8A File Offset: 0x0000FC8A
	private void OnEnable()
	{
		this._showButton.onClick.AddListener(new UnityAction(this.ShowButtonClick));
		this._hideButton.onClick.AddListener(new UnityAction(this.HideButtonClick));
	}

	// Token: 0x060003E9 RID: 1001 RVA: 0x00011AC6 File Offset: 0x0000FCC6
	private void OnDisable()
	{
		this._showButton.onClick.RemoveListener(new UnityAction(this.ShowButtonClick));
		this._hideButton.onClick.RemoveListener(new UnityAction(this.HideButtonClick));
	}

	// Token: 0x060003EA RID: 1002
	public abstract void SetView();

	// Token: 0x060003EB RID: 1003
	public abstract void SetView(int[] arrayId);

	// Token: 0x060003EC RID: 1004
	protected abstract void ShowButtonClick();

	// Token: 0x060003ED RID: 1005
	protected abstract void HideButtonClick();

	// Token: 0x04000311 RID: 785
	[SerializeField]
	private Button _showButton;

	// Token: 0x04000312 RID: 786
	[SerializeField]
	private Button _hideButton;

	// Token: 0x04000313 RID: 787
	public ArrayEvent showButtonClickEvent = new ArrayEvent();
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x020000AB RID: 171
public class MenuMessageNewItems : MenuMessageElement
{
	// Token: 0x060003F0 RID: 1008 RVA: 0x00011B1D File Offset: 0x0000FD1D
	private void OnDestroy()
	{
		this.showButtonClickEvent.RemoveAllListeners();
	}

	// Token: 0x060003F1 RID: 1009 RVA: 0x00011B2C File Offset: 0x0000FD2C
	protected override void HideButtonClick()
	{
		int[] array = new int[this._itemsId.Count];
		this._itemsId.CopyTo(array, 0);
		StoreController.Instance.GetRewardItem(array);
		UnityEngine.Object.Destroy(base.gameObject);
	}

	// Token: 0x060003F2 RID: 1010 RVA: 0x00011B70 File Offset: 0x0000FD70
	protected override void ShowButtonClick()
	{
		int[] array = new int[this._itemsId.Count];
		this._itemsId.CopyTo(array, 0);
		StoreController.Instance.GetRewardItem(array);
		this.showButtonClickEvent.Invoke(this._itemsId);
		UnityEngine.Object.Destroy(base.gameObject);
	}

	// Token: 0x060003F3 RID: 1011 RVA: 0x00011BC2 File Offset: 0x0000FDC2
	public void AddItem(int id)
	{
		if (!this._itemsId.Contains(id))
		{
			this._itemsId.Add(id);
		}
	}

	// Token: 0x060003F4 RID: 1012 RVA: 0x00011BDE File Offset: 0x0000FDDE
	public void RemoveItem(int id)
	{
		if (this._itemsId.Contains(id))
		{
			this._itemsId.Remove(id);
		}
	}

	// Token: 0x060003F5 RID: 1013 RVA: 0x00005F78 File Offset: 0x00004178
	public override void SetView()
	{
	}

	// Token: 0x060003F6 RID: 1014 RVA: 0x00011BFC File Offset: 0x0000FDFC
	public override void SetView(int[] arrayId)
	{
		this._itemsId.RemoveRange(0, this._itemsId.Count);
		foreach (int item in arrayId)
		{
			if (!this._itemsId.Contains(item))
			{
				this._itemsId.Add(item);
			}
		}
	}

	// Token: 0x04000314 RID: 788
	[SerializeField]
	private List<int> _itemsId;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Events;

// Token: 0x020000AC RID: 172
public class MenuMessagePanel : MonoBehaviour
{
	// Token: 0x060003F8 RID: 1016 RVA: 0x00011C58 File Offset: 0x0000FE58
	private void OnEnable()
	{
		this._itemTaskManager = UnityEngine.Object.FindObjectOfType<ItemTaskManager>();
		if (this._itemTaskManager == null)
		{
			this._itemTaskManager = UnityEngine.Object.Instantiate<ItemTaskManager>(this._itemTaskManagerPrefab);
		}
		AccountScreenController instance = AccountScreenController.Instance;
		instance.AccountLoadedEvent = (Action)Delegate.Combine(instance.AccountLoadedEvent, new Action(this.SetView));
		StoreController.Instance.inventoryResultEvent.AddListener(new UnityAction(this.SetView));
	}

	// Token: 0x060003F9 RID: 1017 RVA: 0x00011CD0 File Offset: 0x0000FED0
	private void OnDisable()
	{
		AccountScreenController instance = AccountScreenController.Instance;
		instance.AccountLoadedEvent = (Action)Delegate.Remove(instance.AccountLoadedEvent, new Action(this.SetView));
		StoreController.Instance.inventoryResultEvent.RemoveListener(new UnityAction(this.SetView));
	}

	// Token: 0x060003FA RID: 1018 RVA: 0x00011D20 File Offset: 0x0000FF20
	private void SetView()
	{
		for (int i = 0; i < this._messageList.Count; i++)
		{
			UnityEngine.Object.Destroy(this._messageList[i].gameObject);
			this._messageList.RemoveAt(i);
		}
		if (this._itemTaskManager.GetCompletteTask().Length != 0)
		{
			MenuMessageElement menuMessageElement = UnityEngine.Object.Instantiate<MenuMessageElement>(this._prefabElement, this._content.transform);
			menuMessageElement.SetView(this._itemTaskManager.GetCompletteTask());
			if (!this._messageList.Contains(menuMessageElement))
			{
				this._messageList.Add(menuMessageElement);
			}
			menuMessageElement.showButtonClickEvent.AddListener(new UnityAction<List<int>>(this._openTween.FollowToBox));
		}
	}

	// Token: 0x04000315 RID: 789
	[SerializeField]
	private GameObject _content;

	// Token: 0x04000316 RID: 790
	[SerializeField]
	private List<MenuMessageElement> _messageList;

	// Token: 0x04000317 RID: 791
	[SerializeField]
	private ItemTaskManager _itemTaskManagerPrefab;

	// Token: 0x04000318 RID: 792
	[SerializeField]
	private MenuMessageElement _prefabElement;

	// Token: 0x04000319 RID: 793
	[SerializeField]
	private CameraFolowTween _openTween;

	// Token: 0x0400031A RID: 794
	private ItemTaskManager _itemTaskManager;
}
﻿using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000E2 RID: 226
public class MenuSoundsView : MonoBehaviour
{
	// Token: 0x06000597 RID: 1431 RVA: 0x00019490 File Offset: 0x00017690
	private IEnumerator Start()
	{
		yield return new WaitForSeconds(1f);
		this.SetUpButtons();
		yield break;
	}

	// Token: 0x06000598 RID: 1432 RVA: 0x0001949F File Offset: 0x0001769F
	private void OnDisable()
	{
		this.Unsubscribe();
	}

	// Token: 0x06000599 RID: 1433 RVA: 0x000194A8 File Offset: 0x000176A8
	private void SetUpButtons()
	{
		Button[] componentsInChildren = this._root.GetComponentsInChildren<Button>(true);
		this._buttons = new List<ButtonInteractionView>();
		for (int i = 0; i < componentsInChildren.Length; i++)
		{
			if (componentsInChildren[i].GetComponent<ButtonInteractionView>() != null)
			{
				this._buttons.Add(componentsInChildren[i].GetComponent<ButtonInteractionView>());
			}
			else
			{
				ButtonInteractionView item = componentsInChildren[i].gameObject.AddComponent<ButtonInteractionView>();
				this._buttons.Add(item);
			}
		}
		this.Subscribe();
	}

	// Token: 0x0600059A RID: 1434 RVA: 0x00019520 File Offset: 0x00017720
	private void Subscribe()
	{
		for (int i = 0; i < this._buttons.Count; i++)
		{
			this._buttons[i].OnClick += this.OnButtonClick;
			this._buttons[i].OnEnter += this.OnButtonEnter;
		}
	}

	// Token: 0x0600059B RID: 1435 RVA: 0x00019580 File Offset: 0x00017780
	private void Unsubscribe()
	{
		if (this._buttons == null)
		{
			return;
		}
		for (int i = 0; i < this._buttons.Count; i++)
		{
			this._buttons[i].OnClick -= this.OnButtonClick;
			this._buttons[i].OnEnter -= this.OnButtonEnter;
		}
	}

	// Token: 0x0600059C RID: 1436 RVA: 0x000195E6 File Offset: 0x000177E6
	private void OnButtonEnter()
	{
		this._menuAudio.PlayOneShot(this._buttonEnterAudioClip);
	}

	// Token: 0x0600059D RID: 1437 RVA: 0x000195F9 File Offset: 0x000177F9
	private void OnButtonClick()
	{
		this._menuAudio.PlayOneShot(this._buttonClickAudioClip);
	}

	// Token: 0x040004DB RID: 1243
	[SerializeField]
	private AudioSource _menuAudio;

	// Token: 0x040004DC RID: 1244
	[SerializeField]
	private AudioClip _buttonEnterAudioClip;

	// Token: 0x040004DD RID: 1245
	[SerializeField]
	private AudioClip _buttonClickAudioClip;

	// Token: 0x040004DE RID: 1246
	[SerializeField]
	private GameObject _root;

	// Token: 0x040004DF RID: 1247
	private List<ButtonInteractionView> _buttons;
}
﻿using System;

// Token: 0x0200006E RID: 110
public enum MenuType
{
	// Token: 0x0400020A RID: 522
	Main,
	// Token: 0x0400020B RID: 523
	Weapons,
	// Token: 0x0400020C RID: 524
	Options,
	// Token: 0x0400020D RID: 525
	WeaponAttachments,
	// Token: 0x0400020E RID: 526
	PlayerModel,
	// Token: 0x0400020F RID: 527
	HostGame,
	// Token: 0x04000210 RID: 528
	Login,
	// Token: 0x04000211 RID: 529
	Pause,
	// Token: 0x04000212 RID: 530
	BrowseServers,
	// Token: 0x04000213 RID: 531
	None,
	// Token: 0x04000214 RID: 532
	Map,
	// Token: 0x04000215 RID: 533
	Hide,
	// Token: 0x04000216 RID: 534
	Root,
	// Token: 0x04000217 RID: 535
	ChooseTeam,
	// Token: 0x04000218 RID: 536
	TournamentList,
	// Token: 0x04000219 RID: 537
	TournamentParticipation,
	// Token: 0x0400021A RID: 538
	TournamentCreate,
	// Token: 0x0400021B RID: 539
	TournamentOverview,
	// Token: 0x0400021C RID: 540
	Statistics,
	// Token: 0x0400021D RID: 541
	Store,
	// Token: 0x0400021E RID: 542
	BattlePass
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.UI;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000B2 RID: 178
[CreateAssetMenu(menuName = "MarsFPSKit/Options/Gameplay/MinimapTransparent")]
public class MinimapTransparentOption : Kit_OptionBase
{
	// Token: 0x0600041B RID: 1051 RVA: 0x0001202A File Offset: 0x0001022A
	public override string GetDisplayName()
	{
		return "MinimapTransparent";
	}

	// Token: 0x0600041C RID: 1052 RVA: 0x00011DE0 File Offset: 0x0000FFE0
	public override string GetHoverText()
	{
		return LocalizationController.Instance.GetText("OptionsHoverText", this.LocalizationKey);
	}

	// Token: 0x0600041D RID: 1053 RVA: 0x00011EE5 File Offset: 0x000100E5
	public override OptionType GetOptionType()
	{
		return OptionType.Slider;
	}

	// Token: 0x0600041E RID: 1054 RVA: 0x00012031 File Offset: 0x00010231
	public override void OnSliderStart(TextMeshProUGUI txt, Slider slider)
	{
		Kit_GameSettings.MinimapTransparent = PlayerPrefs.GetFloat(this._prefsKey, this._default);
		slider.SetValueWithoutNotify(Kit_GameSettings.MinimapTransparent);
	}

	// Token: 0x0600041F RID: 1055 RVA: 0x00012054 File Offset: 0x00010254
	public override void OnSliderChange(TextMeshProUGUI txt, float newValue)
	{
		Kit_GameSettings.MinimapTransparent = newValue;
		PlayerPrefs.SetFloat(this._prefsKey, newValue);
	}

	// Token: 0x04000324 RID: 804
	private string _prefsKey = "MinimapTransparent";

	// Token: 0x04000325 RID: 805
	private float _default = 1f;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.UI;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000B3 RID: 179
[CreateAssetMenu(menuName = "MarsFPSKit/Options/Audio/Music Volume")]
public class MusicVolumeOption : Kit_OptionBase
{
	// Token: 0x06000421 RID: 1057 RVA: 0x00012086 File Offset: 0x00010286
	public override string GetDisplayName()
	{
		return "Music Volume";
	}

	// Token: 0x06000422 RID: 1058 RVA: 0x00011DE0 File Offset: 0x0000FFE0
	public override string GetHoverText()
	{
		return LocalizationController.Instance.GetText("OptionsHoverText", this.LocalizationKey);
	}

	// Token: 0x06000423 RID: 1059 RVA: 0x00011EE5 File Offset: 0x000100E5
	public override OptionType GetOptionType()
	{
		return OptionType.Slider;
	}

	// Token: 0x06000424 RID: 1060 RVA: 0x00012090 File Offset: 0x00010290
	public override void OnSliderStart(TextMeshProUGUI txt, Slider slider)
	{
		float @float = PlayerPrefs.GetFloat(this._prefsKey, 0.25f);
		Kit_GameSettings.MusicVolume = @float;
		slider.minValue = 0f;
		slider.maxValue = 1f;
		slider.value = @float;
		this.OnSliderChange(txt, @float);
	}

	// Token: 0x06000425 RID: 1061 RVA: 0x000120D9 File Offset: 0x000102D9
	public override void OnSliderChange(TextMeshProUGUI txt, float newValue)
	{
		Kit_GameSettings.MusicVolume = newValue;
		PlayerPrefs.SetFloat(this._prefsKey, newValue);
	}

	// Token: 0x04000326 RID: 806
	private string _prefsKey = "MusicVolume";
}
﻿using System;
using MarsFPSKit;
using UnityEngine;

// Token: 0x020000AD RID: 173
public class MyAutoSpawn : Kit_AutoSpawnSystemBase
{
	// Token: 0x060003FC RID: 1020 RVA: 0x00005F78 File Offset: 0x00004178
	public override void Interruption()
	{
	}

	// Token: 0x060003FD RID: 1021 RVA: 0x00005F78 File Offset: 0x00004178
	public override void LocalPlayerDied()
	{
	}

	// Token: 0x060003FE RID: 1022 RVA: 0x00005F78 File Offset: 0x00004178
	public override void LocalPlayerSpawned()
	{
	}

	// Token: 0x0400031B RID: 795
	[SerializeField]
	private Kit_IngameMain _main;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using Photon.Pun;
using Photon.Realtime;
using UnityEngine;

// Token: 0x020000E3 RID: 227
public class MyFriendsManager : MonoBehaviourPunCallbacks
{
	// Token: 0x0600059F RID: 1439 RVA: 0x0001960C File Offset: 0x0001780C
	private void Start()
	{
		this._friendsPanelView.ViewInit(this, new Action<string>(this.AddFriend));
	}

	// Token: 0x060005A0 RID: 1440 RVA: 0x00019626 File Offset: 0x00017826
	private void Update()
	{
		if (PhotonNetwork.IsConnectedAndReady && Time.time > this.lastUpdate)
		{
			this.lastUpdate = Time.time + this.updateInterval;
			this.UpdateFriends();
		}
	}

	// Token: 0x060005A1 RID: 1441 RVA: 0x00019654 File Offset: 0x00017854
	public void UpdateFriends()
	{
		if (PhotonNetwork.IsConnectedAndReady && PhotonNetwork.InLobby)
		{
			if (this.myFriends.Count > 0)
			{
				PhotonNetwork.FindFriends(this.myFriends.ToArray());
				return;
			}
			this.OnFriendListUpdate(new List<FriendInfo>());
		}
	}

	// Token: 0x060005A2 RID: 1442 RVA: 0x00019690 File Offset: 0x00017890
	public void SaveFriends()
	{
		PlayerPrefs.SetInt("friendsFor" + Kit_GameSettings.userName + "Amount", this.myFriends.Count);
		for (int i = 0; i < this.myFriends.Count; i++)
		{
			PlayerPrefs.SetString(string.Concat(new object[]
			{
				"friendsFor",
				Kit_GameSettings.userName,
				"Index",
				i
			}), this.myFriends[i]);
		}
	}

	// Token: 0x060005A3 RID: 1443 RVA: 0x00019714 File Offset: 0x00017914
	public void LoadFriends()
	{
		int @int = PlayerPrefs.GetInt("friendsFor" + Kit_GameSettings.userName + "Amount");
		this.myFriends = new List<string>();
		for (int i = 0; i < @int; i++)
		{
			string @string = PlayerPrefs.GetString(string.Concat(new object[]
			{
				"friendsFor",
				Kit_GameSettings.userName,
				"Index",
				i
			}));
			if (!string.IsNullOrEmpty(@string))
			{
				this.myFriends.Add(@string);
			}
		}
	}

	// Token: 0x060005A4 RID: 1444 RVA: 0x00019798 File Offset: 0x00017998
	public void AfterLogin()
	{
		this.LoadFriends();
		this.UpdateFriends();
	}

	// Token: 0x060005A5 RID: 1445 RVA: 0x000197A6 File Offset: 0x000179A6
	public void BeforeOpening()
	{
		this.UpdateFriends();
	}

	// Token: 0x060005A6 RID: 1446 RVA: 0x000197AE File Offset: 0x000179AE
	public override void OnFriendListUpdate(List<FriendInfo> friendList)
	{
		this._friendsPanelView.UpdateFriendsList(friendList);
	}

	// Token: 0x060005A7 RID: 1447 RVA: 0x000197BC File Offset: 0x000179BC
	public void JoinRoom(string room)
	{
		PhotonNetwork.JoinRoom(room, null);
	}

	// Token: 0x060005A8 RID: 1448 RVA: 0x000197C6 File Offset: 0x000179C6
	public void RemoveFriend(string str)
	{
		if (this.myFriends.Contains(str))
		{
			this.myFriends.Remove(str);
			this.UpdateFriends();
			this.SaveFriends();
		}
	}

	// Token: 0x060005A9 RID: 1449 RVA: 0x000197EF File Offset: 0x000179EF
	public void AddFriend(string str)
	{
		if (!this.myFriends.Contains(str) && !string.IsNullOrEmpty(str))
		{
			this.myFriends.Add(str);
			this.UpdateFriends();
			this.SaveFriends();
		}
	}

	// Token: 0x040004E0 RID: 1248
	public List<string> myFriends = new List<string>();

	// Token: 0x040004E1 RID: 1249
	public FriendsPanelView _friendsPanelView;

	// Token: 0x040004E2 RID: 1250
	public float updateInterval = 5f;

	// Token: 0x040004E3 RID: 1251
	private float lastUpdate;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000D7 RID: 215
public class MyHUD : Kit_PlayerHUD
{
	// Token: 0x0600053D RID: 1341 RVA: 0x000180FA File Offset: 0x000162FA
	public override void DisplayHealth(float hp)
	{
		this._healthBar.fillAmount = hp / 100f;
	}

	// Token: 0x0600053E RID: 1342 RVA: 0x00018110 File Offset: 0x00016310
	public override void DisplayWeaponsAndQuickUses(Kit_PlayerBehaviour pb, WeaponManagerControllerRuntimeData runtimeData)
	{
		List<WeaponDisplayData> list = new List<WeaponDisplayData>();
		List<WeaponQuickUseDisplayData> list2 = new List<WeaponQuickUseDisplayData>();
		for (int i = 0; i < runtimeData.weaponsInUse.Length; i++)
		{
			for (int j = 0; j < runtimeData.weaponsInUse[i].weaponsInSlot.Length; j++)
			{
				WeaponDisplayData weaponDisplayData = runtimeData.weaponsInUse[i].weaponsInSlot[j].behaviour.GetWeaponDisplayData(pb, runtimeData.weaponsInUse[i].weaponsInSlot[j].runtimeData);
				WeaponQuickUseDisplayData weaponQuickUseDisplayData = runtimeData.weaponsInUse[i].weaponsInSlot[j].behaviour.GetWeaponQuickUseDisplayData(pb, runtimeData.weaponsInUse[i].weaponsInSlot[j].runtimeData);
				if (weaponDisplayData != null)
				{
					if (runtimeData.currentWeapon[0] == i && runtimeData.currentWeapon[1] == j)
					{
						weaponDisplayData.selected = true;
					}
					else
					{
						weaponDisplayData.selected = false;
					}
					list.Add(weaponDisplayData);
				}
				if (weaponQuickUseDisplayData != null)
				{
					list2.Add(weaponQuickUseDisplayData);
				}
			}
		}
		if (list.Count != this.weaponDisplayActives.Count)
		{
			while (list.Count != this.weaponDisplayActives.Count)
			{
				if (this.weaponDisplayActives.Count > list.Count)
				{
					UnityEngine.Object.Destroy(this.weaponDisplayActives[this.weaponDisplayActives.Count - 1].gameObject);
					this.weaponDisplayActives.RemoveAt(this.weaponDisplayActives.Count - 1);
				}
				else if (this.weaponDisplayActives.Count < list.Count)
				{
					Image component = UnityEngine.Object.Instantiate<GameObject>(this.weaponDisplayPrefab, this.weaponDisplayGo, false).GetComponent<Image>();
					this.weaponDisplayActives.Add(component);
				}
			}
		}
		for (int k = 0; k < list.Count; k++)
		{
			this.weaponDisplayActives[k].sprite = list[k].sprite;
			if (list[k].selected)
			{
				this.weaponDisplayActives[k].color = this.weaponDisplaySelectedColor;
			}
			else
			{
				this.weaponDisplayActives[k].color = this.weaponDisplayUnselectedColor;
			}
		}
		for (int l = 0; l < list.Count; l++)
		{
			for (int m = 0; m < list2.Count; m++)
			{
				if (list[l].name == list2[m].name)
				{
					int num = this.weaponDisplayActives[l].transform.childCount;
					while (list2[m].amount - 1 > num)
					{
						GameObject gameObject = UnityEngine.Object.Instantiate<GameObject>(this.weaponQuickUseDisplayPrefab, this.weaponDisplayActives[l].transform, false);
						gameObject.GetComponent<RectTransform>().anchoredPosition += new Vector2((float)(this._quickUseOffset * (num + 1)), 0f);
						gameObject.GetComponent<Image>().sprite = list2[m].sprite;
						num++;
					}
					while (list2[m].amount - 1 < num)
					{
						Transform[] componentsInChildren = this.weaponDisplayActives[l].GetComponentsInChildren<Transform>();
						UnityEngine.Object.Destroy(componentsInChildren[componentsInChildren.Length - 1].gameObject);
						num--;
					}
					break;
				}
			}
		}
	}

	// Token: 0x0600053F RID: 1343 RVA: 0x0001845E File Offset: 0x0001665E
	public override void DisplayCrosshair(float size, bool overrideShow)
	{
		base.DisplayCrosshair(size, overrideShow);
		this._grenadeCrosshair.HideCrosshair();
	}

	// Token: 0x06000540 RID: 1344 RVA: 0x00018473 File Offset: 0x00016673
	public void DisplayGrenadeCrosshair(bool isCloseThrow)
	{
		this._grenadeCrosshair.ShowCrosshair(isCloseThrow);
	}

	// Token: 0x040004A5 RID: 1189
	[SerializeField]
	private Image _healthBar;

	// Token: 0x040004A6 RID: 1190
	[SerializeField]
	private int _quickUseOffset;

	// Token: 0x040004A7 RID: 1191
	[SerializeField]
	private GrenadeCrosshairView _grenadeCrosshair;
}
﻿using System;
using System.Collections.Generic;
using ExitGames.Client.Photon;
using MarsFPSKit;
using MarsFPSKit.UI;
using Photon.Pun;
using Photon.Realtime;
using UnityEngine;

// Token: 0x020000E4 RID: 228
public class MyInLobbyManager : MonoBehaviourPunCallbacks, IPunObservable
{
	// Token: 0x060005AB RID: 1451 RVA: 0x0001983D File Offset: 0x00017A3D
	private void Awake()
	{
		this.cachedRoomList = new Dictionary<string, RoomInfo>();
		this.RedrawGameModeSelection();
	}

	// Token: 0x060005AC RID: 1452 RVA: 0x00019850 File Offset: 0x00017A50
	private void Start()
	{
		if (PhotonNetwork.InRoom)
		{
			Hashtable hashtable = new Hashtable();
			hashtable.Add("mapVote", -1);
			PhotonNetwork.LocalPlayer.SetCustomProperties(hashtable, null, null);
			this.LobbyEntered();
			if (PhotonNetwork.IsMasterClient)
			{
				this.voteOverAtTime = PhotonNetwork.Time + this.voteTimeGiven;
				List<int> list = new List<int>();
				int num = (int)PhotonNetwork.CurrentRoom.CustomProperties["gameMode"];
				int num2 = 0;
				while (list.Count < Mathf.Clamp(this._game.allPvpGameModes[num].lobbyAmountOfMapsToVoteFor, 0, this._game.allPvpGameModes[num].lobbyMaps.Length) && num2 < 100)
				{
					num2++;
					int item = UnityEngine.Random.Range(0, this._game.allPvpGameModes[num].lobbyMaps.Length);
					if (!list.Contains(item))
					{
						list.Add(item);
					}
				}
				Debug.Log(list.Count);
				this.mapVotes = list.ToArray();
				return;
			}
		}
		else
		{
			this.LobbyLeft();
		}
	}

	// Token: 0x060005AD RID: 1453 RVA: 0x00019960 File Offset: 0x00017B60
	private void Update()
	{
		if (Kit_GameSettings.currentNetworkingMode == KitNetworkingMode.Lobby && PhotonNetwork.InRoom)
		{
			if (this.countdownUntilGameIsEnteredTimeLeft < 10f && this.countdownUntilGameIsEnteredHasBegun)
			{
				if (Mathf.FloorToInt(this.countdownUntilGameIsEnteredTimeLeft) != this.lastCountdownInt)
				{
					this.lastCountdownInt = Mathf.FloorToInt(this.countdownUntilGameIsEnteredTimeLeft);
					if (this.countdownSound)
					{
						AudioSource.PlayClipAtPoint(this.countdownSound, Camera.main.transform.position, 1f);
					}
				}
			}
			else if (Time.time > this.currentLobbyStateLastUpdate)
			{
				if (PhotonNetwork.Time > this.voteOverAtTime)
				{
					if ((int)PhotonNetwork.CurrentRoom.PlayerCount >= this._game.allPvpGameModes[(int)PhotonNetwork.CurrentRoom.CustomProperties["gameMode"]].lobbyMinimumPlayersNeeded)
					{
						if (!this.countdownUntilGameIsEnteredHasBegun && PhotonNetwork.IsMasterClient)
						{
							this.countdownUntilGameIsEnteredTimeLeft = this.timeUntilGameStartsOnceEnoughPlayersAreFound;
							this.countdownUntilGameIsEnteredHasBegun = true;
						}
					}
					else if (this._game.allPvpGameModes[(int)PhotonNetwork.CurrentRoom.CustomProperties["gameMode"]].lobbyStartWithBotsAfterSeconds > 0f)
					{
						if (PhotonNetwork.Time > this.lobbyCreatedAtNetworkTime + (double)this._game.allPvpGameModes[(int)PhotonNetwork.CurrentRoom.CustomProperties["gameMode"]].lobbyStartWithBotsAfterSeconds)
						{
							if (!this.countdownUntilGameIsEnteredHasBegun && PhotonNetwork.IsMasterClient)
							{
								this.countdownUntilGameIsEnteredTimeLeft = this.timeUntilGameStartsOnceEnoughPlayersAreFound;
								this.countdownUntilGameIsEnteredHasBegun = true;
								this.countdownIsBotOverride = true;
								Hashtable customProperties = PhotonNetwork.CurrentRoom.CustomProperties;
								customProperties["bots"] = true;
								PhotonNetwork.CurrentRoom.SetCustomProperties(customProperties, null, null);
							}
						}
						else if (UnityEngine.Random.Range(0, 4) < 2 && this._game.allPvpGameModes[(int)PhotonNetwork.CurrentRoom.CustomProperties["gameMode"]].lobbyMinimumPlayersNeeded - (int)PhotonNetwork.CurrentRoom.PlayerCount > 1)
						{
						}
					}
					else if (UnityEngine.Random.Range(0, 4) < 2 && this._game.allPvpGameModes[(int)PhotonNetwork.CurrentRoom.CustomProperties["gameMode"]].lobbyMinimumPlayersNeeded - (int)PhotonNetwork.CurrentRoom.PlayerCount > 1)
					{
					}
				}
				else
				{
					UnityEngine.Random.Range(0, 5);
				}
				this.currentLobbyStateLastUpdate = Time.time + this.currentLobbyStateUpdateTime;
			}
			if (PhotonNetwork.IsMasterClient && this.countdownUntilGameIsEnteredHasBegun && this.countdownUntilGameIsEnteredTimeLeft > 0f)
			{
				this.countdownUntilGameIsEnteredTimeLeft -= Time.deltaTime;
				if (this.countdownUntilGameIsEnteredTimeLeft <= 0f)
				{
					if (this.countdownIsBotOverride)
					{
						Hashtable customProperties2 = PhotonNetwork.CurrentRoom.CustomProperties;
						customProperties2["bots"] = true;
						PhotonNetwork.CurrentRoom.SetCustomProperties(customProperties2, null, null);
					}
					else
					{
						Hashtable customProperties3 = PhotonNetwork.CurrentRoom.CustomProperties;
						int num = (int)PhotonNetwork.CurrentRoom.CustomProperties["gameMode"];
						customProperties3["bots"] = this._game.allPvpGameModes[num].lobbyBotsEnabled;
						PhotonNetwork.CurrentRoom.SetCustomProperties(customProperties3, null, null);
					}
					Hashtable customProperties4 = PhotonNetwork.CurrentRoom.CustomProperties;
					if (this.mapVotes.Length != 0)
					{
						int num2 = this.mapVotes[this.GetMapWithMostVotes()];
						customProperties4["map"] = num2;
						int num3 = (int)customProperties4["gameMode"];
						PhotonNetwork.CurrentRoom.SetCustomProperties(customProperties4, null, null);
						Kit_SceneSyncer.instance.LoadScene(this._game.allPvpGameModes[num3].lobbyMaps[num2].sceneName);
						return;
					}
					int num4 = (int)customProperties4["map"];
					int num5 = (int)customProperties4["gameMode"];
					Kit_SceneSyncer.instance.LoadScene(this._game.allPvpGameModes[num5].lobbyMaps[num4].sceneName);
				}
			}
		}
	}

	// Token: 0x060005AE RID: 1454 RVA: 0x00019D80 File Offset: 0x00017F80
	public void OnPhotonSerializeView(PhotonStream stream, PhotonMessageInfo info)
	{
		if (stream.IsWriting)
		{
			stream.SendNext(this.countdownUntilGameIsEnteredHasBegun);
			stream.SendNext(this.countdownUntilGameIsEnteredTimeLeft);
			stream.SendNext(this.lobbyCreatedAtNetworkTime);
			stream.SendNext(this.countdownIsBotOverride);
			stream.SendNext(this.voteOverAtTime);
			stream.SendNext(this.mapVotes.Length);
			for (int i = 0; i < this.mapVotes.Length; i++)
			{
				stream.SendNext(this.mapVotes[i]);
			}
			return;
		}
		this.countdownUntilGameIsEnteredHasBegun = (bool)stream.ReceiveNext();
		this.countdownUntilGameIsEnteredTimeLeft = (float)stream.ReceiveNext();
		this.lobbyCreatedAtNetworkTime = (double)stream.ReceiveNext();
		this.countdownIsBotOverride = (bool)stream.ReceiveNext();
		this.voteOverAtTime = (double)stream.ReceiveNext();
		int num = (int)stream.ReceiveNext();
		if (this.mapVotes.Length != num)
		{
			this.mapVotes = new int[num];
		}
		for (int j = 0; j < num; j++)
		{
			int num2 = (int)stream.ReceiveNext();
			if (this.mapVotes[j] != num2)
			{
				this.mapVotes[j] = num2;
				this.RedrawMaps();
			}
		}
	}

	// Token: 0x060005AF RID: 1455 RVA: 0x00019ED4 File Offset: 0x000180D4
	private void UpdateCachedRoomList(List<RoomInfo> roomList)
	{
		foreach (RoomInfo roomInfo in roomList)
		{
			if (!roomInfo.IsOpen || !roomInfo.IsVisible || roomInfo.RemovedFromList)
			{
				if (this.cachedRoomList.ContainsKey(roomInfo.Name))
				{
					this.cachedRoomList.Remove(roomInfo.Name);
				}
			}
			else if (this.cachedRoomList.ContainsKey(roomInfo.Name))
			{
				this.cachedRoomList[roomInfo.Name] = roomInfo;
			}
			else
			{
				this.cachedRoomList.Add(roomInfo.Name, roomInfo);
			}
		}
	}

	// Token: 0x060005B0 RID: 1456 RVA: 0x00019F9C File Offset: 0x0001819C
	public override void OnJoinedRoom()
	{
		Room currentRoom = PhotonNetwork.CurrentRoom;
		if (currentRoom.CustomProperties["lobby"] != null && (bool)currentRoom.CustomProperties["lobby"])
		{
			Hashtable hashtable = new Hashtable();
			hashtable.Add("mapVote", -1);
			PhotonNetwork.LocalPlayer.SetCustomProperties(hashtable, null, null);
			this.LobbyEntered();
		}
	}

	// Token: 0x060005B1 RID: 1457 RVA: 0x0001A004 File Offset: 0x00018204
	public override void OnCreatedRoom()
	{
		Room currentRoom = PhotonNetwork.CurrentRoom;
		if (currentRoom.CustomProperties["lobby"] != null && (bool)currentRoom.CustomProperties["lobby"])
		{
			this.lobbyCreatedAtNetworkTime = PhotonNetwork.Time;
			Hashtable hashtable = new Hashtable();
			hashtable.Add("mapVote", -1);
			PhotonNetwork.LocalPlayer.SetCustomProperties(hashtable, null, null);
			this.LobbyEntered();
			this.RedrawMaps();
		}
	}

	// Token: 0x060005B2 RID: 1458 RVA: 0x0001A07C File Offset: 0x0001827C
	public override void OnLeftRoom()
	{
		if (Kit_GameSettings.currentNetworkingMode == KitNetworkingMode.Lobby)
		{
			this.LobbyLeft();
		}
	}

	// Token: 0x060005B3 RID: 1459 RVA: 0x0001A08C File Offset: 0x0001828C
	public override void OnRoomListUpdate(List<RoomInfo> roomList)
	{
		this.UpdateCachedRoomList(roomList);
		this.RedrawGameModeSelection();
	}

	// Token: 0x060005B4 RID: 1460 RVA: 0x0001A09C File Offset: 0x0001829C
	public override void OnPlayerEnteredRoom(Player newPlayer)
	{
		if (Kit_GameSettings.currentNetworkingMode == KitNetworkingMode.Lobby)
		{
			this.RedrawLobbyMembers();
			if (this.playerJoinedSound)
			{
				AudioSource.PlayClipAtPoint(this.playerJoinedSound, Camera.main.transform.position, 1f);
			}
			if (PhotonNetwork.IsMasterClient && this.countdownIsBotOverride)
			{
				this.currentLobbyStateLastUpdate = Time.time + this.currentLobbyStateUpdateTime;
				this.countdownUntilGameIsEnteredHasBegun = false;
				this.countdownIsBotOverride = false;
				this.lobbyCreatedAtNetworkTime += 10.0;
				Hashtable customProperties = PhotonNetwork.CurrentRoom.CustomProperties;
				int num = (int)PhotonNetwork.CurrentRoom.CustomProperties["gameMode"];
				customProperties["bots"] = this._game.allPvpGameModes[num].lobbyBotsEnabled;
				PhotonNetwork.CurrentRoom.SetCustomProperties(customProperties, null, null);
			}
			this.RedrawMaps();
		}
	}

	// Token: 0x060005B5 RID: 1461 RVA: 0x0001A18C File Offset: 0x0001838C
	public override void OnPlayerLeftRoom(Player otherPlayer)
	{
		if (Kit_GameSettings.currentNetworkingMode == KitNetworkingMode.Lobby)
		{
			this.RedrawLobbyMembers();
			if (this.playerLeftSound)
			{
				AudioSource.PlayClipAtPoint(this.playerLeftSound, Camera.main.transform.position, 1f);
			}
			if ((int)PhotonNetwork.CurrentRoom.PlayerCount < this._game.allPvpGameModes[(int)PhotonNetwork.CurrentRoom.CustomProperties["gameMode"]].lobbyMinimumPlayersNeeded && !this.countdownIsBotOverride)
			{
				this.currentLobbyStateLastUpdate = Time.time + this.currentLobbyStateUpdateTime;
				this.countdownUntilGameIsEnteredHasBegun = false;
			}
			this.RedrawMaps();
		}
	}

	// Token: 0x060005B6 RID: 1462 RVA: 0x0001A233 File Offset: 0x00018433
	public override void OnPlayerPropertiesUpdate(Player target, Hashtable changedProps)
	{
		this.RedrawMaps();
	}

	// Token: 0x060005B7 RID: 1463 RVA: 0x0001A23B File Offset: 0x0001843B
	private void LobbyEntered()
	{
		Debug.Log("LobbyManager: LobbyEnterd");
		Kit_GameSettings.currentNetworkingMode = KitNetworkingMode.Lobby;
		this.RedrawLobbyMembers();
		this.wasInLobby = true;
	}

	// Token: 0x060005B8 RID: 1464 RVA: 0x0001A25A File Offset: 0x0001845A
	private void LobbyLeft()
	{
		Kit_GameSettings.currentNetworkingMode = KitNetworkingMode.Traditional;
		this.countdownUntilGameIsEnteredHasBegun = false;
		this.countdownUntilGameIsEnteredTimeLeft = 0f;
		this.lastCountdownInt = 10;
		bool flag = this.wasInLobby;
	}

	// Token: 0x060005B9 RID: 1465 RVA: 0x0001A284 File Offset: 0x00018484
	public void RedrawLobbyMembers()
	{
		if (PhotonNetwork.InRoom)
		{
			Room currentRoom = PhotonNetwork.CurrentRoom;
			while (this.playerEntries.Count < (int)currentRoom.MaxPlayers)
			{
				Kit_LobbyPlayerEntry component = UnityEngine.Object.Instantiate<GameObject>(this.playerEntryPrefab, this.playerEntryGo, false).GetComponent<Kit_LobbyPlayerEntry>();
				this.playerEntries.Add(component);
			}
			for (int i = 0; i < (int)currentRoom.MaxPlayers; i++)
			{
				this.playerEntries[i].txt.text = "Searching...";
			}
			for (int j = 0; j < PhotonNetwork.PlayerList.Length; j++)
			{
				this.playerEntries[j].txt.text = PhotonNetwork.PlayerList[j].NickName;
			}
		}
	}

	// Token: 0x060005BA RID: 1466 RVA: 0x0001A33C File Offset: 0x0001853C
	private void RedrawMaps()
	{
		for (int i = 0; i < this.mapVoteEntries.Count; i++)
		{
			UnityEngine.Object.Destroy(this.mapVoteEntries[i].gameObject);
		}
		this.mapVoteEntries = new List<Kit_LobbyButton>();
		if (PhotonNetwork.InRoom)
		{
			if (this.mapVotes.Length != 0)
			{
				for (int j = 0; j < this.mapVotes.Length; j++)
				{
					int id = j;
					Kit_LobbyButton component = UnityEngine.Object.Instantiate<GameObject>(this.mapVotePrefab, this.mapVoteGo, false).GetComponent<Kit_LobbyButton>();
					component.txt.text = this.GetVotesForMap(j) + "/" + this.GetTotalVotes();
					component.img.sprite = this._game.allPvpGameModes[(int)PhotonNetwork.CurrentRoom.CustomProperties["gameMode"]].lobbyMaps[id].mapPicture;
					component.btn.onClick.AddListener(delegate()
					{
						this.VoteForMap(id);
					});
					this.mapVoteEntries.Add(component);
				}
				return;
			}
			Kit_LobbyButton component2 = UnityEngine.Object.Instantiate<GameObject>(this.mapVotePrefab, this.mapVoteGo, false).GetComponent<Kit_LobbyButton>();
			component2.txt.text = "";
			component2.img.sprite = this._game.allPvpGameModes[(int)PhotonNetwork.CurrentRoom.CustomProperties["gameMode"]].lobbyMaps[(int)PhotonNetwork.CurrentRoom.CustomProperties["map"]].mapPicture;
			this.mapVoteEntries.Add(component2);
		}
	}

	// Token: 0x060005BB RID: 1467 RVA: 0x0001A500 File Offset: 0x00018700
	public void VoteForMap(int id)
	{
		if (PhotonNetwork.InRoom)
		{
			Hashtable customProperties = PhotonNetwork.LocalPlayer.CustomProperties;
			customProperties["mapVote"] = id;
			PhotonNetwork.LocalPlayer.SetCustomProperties(customProperties, null, null);
			this.RedrawMaps();
		}
	}

	// Token: 0x060005BC RID: 1468 RVA: 0x0001A544 File Offset: 0x00018744
	public void RedrawGameModeSelection()
	{
		int num = 0;
		int[] array = new int[this._game.allPvpGameModes.Length];
		foreach (RoomInfo roomInfo in this.cachedRoomList.Values)
		{
			if (roomInfo.CustomProperties["lobby"] != null && (bool)roomInfo.CustomProperties["lobby"] && roomInfo.CustomProperties["gameMode"] != null)
			{
				int num2 = (int)roomInfo.CustomProperties["gameMode"];
				int playerCount = roomInfo.PlayerCount;
				num += playerCount;
				if (num2 >= 0 && num2 < array.Length)
				{
					array[num2] += playerCount;
				}
			}
		}
		int count = this.gameModeSelectionEntries.Count;
	}

	// Token: 0x060005BD RID: 1469 RVA: 0x0001A638 File Offset: 0x00018838
	public void SearchGame(int gameMode)
	{
		if (PhotonNetwork.IsConnectedAndReady && PhotonNetwork.InLobby)
		{
			foreach (RoomInfo roomInfo in this.cachedRoomList.Values)
			{
				if ((int)roomInfo.CustomProperties["gameMode"] == gameMode && (bool)roomInfo.CustomProperties["lobby"] && roomInfo.PlayerCount < (int)roomInfo.MaxPlayers)
				{
					PhotonNetwork.JoinRoom(roomInfo.Name, null);
					return;
				}
			}
			this.countdownIsBotOverride = false;
			this.countdownUntilGameIsEnteredHasBegun = false;
			this.countdownUntilGameIsEnteredTimeLeft = 0f;
			this.currentLobbyStateLastUpdate = 0f;
			this.voteOverAtTime = 0.0;
			RoomOptions roomOptions = new RoomOptions();
			roomOptions.MaxPlayers = this._game.allPvpGameModes[gameMode].lobbyMaximumPlayers;
			roomOptions.CustomRoomProperties = new Hashtable();
			roomOptions.CustomRoomProperties.Add("lobby", true);
			roomOptions.CustomRoomProperties.Add("map", UnityEngine.Random.Range(0, this._game.allPvpGameModes[gameMode].lobbyMaps.Length));
			roomOptions.CustomRoomProperties.Add("gameMode", gameMode);
			roomOptions.CustomRoomProperties.Add("duration", 0);
			roomOptions.CustomRoomProperties.Add("ping", 0);
			roomOptions.CustomRoomProperties.Add("afk", 0);
			roomOptions.CustomRoomProperties.Add("bots", this._game.allPvpGameModes[gameMode].lobbyBotsEnabled);
			roomOptions.CustomRoomProperties.Add("password", "");
			roomOptions.CustomRoomProperties.Add("playerNeeded", this._game.allPvpGameModes[gameMode].lobbyMinimumPlayersNeeded);
			roomOptions.CustomRoomPropertiesForLobby = new string[]
			{
				"lobby",
				"map",
				"gameMode",
				"duration",
				"bots",
				"password"
			};
			PhotonNetwork.OfflineMode = false;
			PhotonNetwork.CreateRoom(PhotonNetwork.LocalPlayer.NickName + "'s Lobby", roomOptions, null, null);
		}
	}

	// Token: 0x060005BE RID: 1470 RVA: 0x0001A8AC File Offset: 0x00018AAC
	public void LeaveLobby()
	{
		if (PhotonNetwork.InRoom)
		{
			PhotonNetwork.LeaveRoom(true);
			this.countdownIsBotOverride = false;
			this.countdownUntilGameIsEnteredHasBegun = false;
			this.countdownUntilGameIsEnteredTimeLeft = 0f;
			this.currentLobbyStateLastUpdate = 0f;
			this.voteOverAtTime = 0.0;
		}
	}

	// Token: 0x060005BF RID: 1471 RVA: 0x0001A8FC File Offset: 0x00018AFC
	public int GetVotesForMap(int id)
	{
		int num = 0;
		for (int i = 0; i < PhotonNetwork.PlayerList.Length; i++)
		{
			if (PhotonNetwork.PlayerList[i].CustomProperties["mapVote"] != null && (int)PhotonNetwork.PlayerList[i].CustomProperties["mapVote"] == id)
			{
				num++;
			}
		}
		return num;
	}

	// Token: 0x060005C0 RID: 1472 RVA: 0x0001A958 File Offset: 0x00018B58
	public int GetTotalVotes()
	{
		int num = 0;
		for (int i = 0; i < PhotonNetwork.PlayerList.Length; i++)
		{
			if (PhotonNetwork.PlayerList[i].CustomProperties["mapVote"] != null && (int)PhotonNetwork.PlayerList[i].CustomProperties["mapVote"] >= 0)
			{
				num++;
			}
		}
		return num;
	}

	// Token: 0x060005C1 RID: 1473 RVA: 0x0001A9B4 File Offset: 0x00018BB4
	public int GetMapWithMostVotes()
	{
		int result = -1;
		int num = -1;
		for (int i = 0; i < this.mapVotes.Length; i++)
		{
			int num2 = i;
			if (this.GetVotesForMap(num2) > num)
			{
				num = this.GetVotesForMap(num2);
				result = num2;
			}
		}
		return result;
	}

	// Token: 0x040004E4 RID: 1252
	[SerializeField]
	private Kit_GameInformation _game;

	// Token: 0x040004E5 RID: 1253
	[Header("Settings")]
	public float timeUntilGameStartsOnceEnoughPlayersAreFound = 15f;

	// Token: 0x040004E6 RID: 1254
	public double voteTimeGiven = 30.0;

	// Token: 0x040004E7 RID: 1255
	public int lobbyScreenId;

	// Token: 0x040004E8 RID: 1256
	[Header("Sounds")]
	public AudioClip playerJoinedSound;

	// Token: 0x040004E9 RID: 1257
	public AudioClip playerLeftSound;

	// Token: 0x040004EA RID: 1258
	public AudioClip countdownSound;

	// Token: 0x040004EB RID: 1259
	private int lastCountdownInt = 10;

	// Token: 0x040004EC RID: 1260
	[Header("UI")]
	public float currentLobbyStateUpdateTime;

	// Token: 0x040004ED RID: 1261
	private float currentLobbyStateLastUpdate;

	// Token: 0x040004EE RID: 1262
	[Header("Game Mode Selection")]
	public GameObject gameModeSelectionPrefab;

	// Token: 0x040004EF RID: 1263
	public RectTransform gameModeSelectionGo;

	// Token: 0x040004F0 RID: 1264
	public List<Kit_LobbyButton> gameModeSelectionEntries = new List<Kit_LobbyButton>();

	// Token: 0x040004F1 RID: 1265
	[Header("Player Entries")]
	public GameObject playerEntryPrefab;

	// Token: 0x040004F2 RID: 1266
	public RectTransform playerEntryGo;

	// Token: 0x040004F3 RID: 1267
	public List<Kit_LobbyPlayerEntry> playerEntries = new List<Kit_LobbyPlayerEntry>();

	// Token: 0x040004F4 RID: 1268
	[Header("Map Preview/Vote")]
	public GameObject mapVotePrefab;

	// Token: 0x040004F5 RID: 1269
	public RectTransform mapVoteGo;

	// Token: 0x040004F6 RID: 1270
	public List<Kit_LobbyButton> mapVoteEntries = new List<Kit_LobbyButton>();

	// Token: 0x040004F7 RID: 1271
	private Dictionary<string, RoomInfo> cachedRoomList;

	// Token: 0x040004F8 RID: 1272
	private bool countdownUntilGameIsEnteredHasBegun;

	// Token: 0x040004F9 RID: 1273
	private float countdownUntilGameIsEnteredTimeLeft;

	// Token: 0x040004FA RID: 1274
	private double lobbyCreatedAtNetworkTime;

	// Token: 0x040004FB RID: 1275
	private double voteOverAtTime;

	// Token: 0x040004FC RID: 1276
	private bool countdownIsBotOverride;

	// Token: 0x040004FD RID: 1277
	public int[] mapVotes;

	// Token: 0x040004FE RID: 1278
	private bool wasInLobby;
}
﻿using System;
using UnityEngine;

// Token: 0x02000060 RID: 96
public class ObserveProcess
{
	// Token: 0x06000280 RID: 640 RVA: 0x0000D729 File Offset: 0x0000B929
	public ObserveProcess()
	{
		this._sceneDriver = UnityEngine.Object.FindObjectOfType<SceneDriver>();
	}

	// Token: 0x06000281 RID: 641 RVA: 0x0000D73C File Offset: 0x0000B93C
	public void StartProcess(Action action)
	{
		if (!this._isBusy)
		{
			this._action = action;
			this._isBusy = true;
			this._sceneDriver.OnUpdate += this._action;
		}
	}

	// Token: 0x06000282 RID: 642 RVA: 0x0000D765 File Offset: 0x0000B965
	public void EndProcess()
	{
		this._sceneDriver.OnUpdate -= this._action;
		this._isBusy = false;
	}

	// Token: 0x040001BA RID: 442
	private SceneDriver _sceneDriver;

	// Token: 0x040001BB RID: 443
	private Action _action;

	// Token: 0x040001BC RID: 444
	private bool _isBusy;
}
﻿using System;
using System.Collections;
using MarsFPSKit.UI;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000074 RID: 116
[CreateAssetMenu(menuName = "MarsFPSKit/Options/Input")]
public class OptionInput : Kit_OptionBase
{
	// Token: 0x06000327 RID: 807 RVA: 0x0000FB52 File Offset: 0x0000DD52
	public override string GetDisplayName()
	{
		return "Forward";
	}

	// Token: 0x06000328 RID: 808 RVA: 0x0000FB59 File Offset: 0x0000DD59
	public override string GetHoverText()
	{
		return LocalizationController.Instance.GetText("InputOptionsHoverText", this.LocalizationKey);
	}

	// Token: 0x06000329 RID: 809 RVA: 0x0000FB70 File Offset: 0x0000DD70
	public override OptionType GetOptionType()
	{
		return OptionType.Button;
	}

	// Token: 0x0600032A RID: 810 RVA: 0x0000FB73 File Offset: 0x0000DD73
	public override void OnButtonStart(TextMeshProUGUI txt, Button button)
	{
		this._observeProcess = new ObserveProcess();
		this._waitProcess = new WaitProcess();
		txt.text = InputSettingsController.Instance.GetKeyString(this.InputAction);
	}

	// Token: 0x0600032B RID: 811 RVA: 0x0000FBA4 File Offset: 0x0000DDA4
	public override void OnButtonChange(TextMeshProUGUI txt, Button button)
	{
		if (!InputSettingsController.Instance.IsWriting)
		{
			button.enabled = false;
			InputSettingsController.Instance.IsWriting = true;
			txt.text = "...";
			this._observeProcess.StartProcess(delegate
			{
				if (Input.anyKeyDown)
				{
					using (IEnumerator enumerator = Enum.GetValues(typeof(KeyCode)).GetEnumerator())
					{
						while (enumerator.MoveNext())
						{
							KeyCode item = (KeyCode)enumerator.Current;
							if (Input.GetKey(item))
							{
								this._waitProcess.StartProcess(delegate
								{
									InputSettingsController.Instance.SetInput(this.InputAction, item);
									txt.text = InputSettingsController.Instance.GetKeyString(this.InputAction);
									button.enabled = true;
								}, 0.25f);
								this._observeProcess.EndProcess();
								break;
							}
						}
					}
				}
			});
		}
	}

	// Token: 0x04000256 RID: 598
	public InputActions InputAction;

	// Token: 0x04000257 RID: 599
	private ObserveProcess _observeProcess;

	// Token: 0x04000258 RID: 600
	private WaitProcess _waitProcess;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x02000021 RID: 33
public class ParticleCollision : MonoBehaviour
{
	// Token: 0x060000EB RID: 235 RVA: 0x00005D3C File Offset: 0x00003F3C
	private void Start()
	{
		this.m_ParticleSystem = base.GetComponent<ParticleSystem>();
	}

	// Token: 0x060000EC RID: 236 RVA: 0x00005D4C File Offset: 0x00003F4C
	private void OnParticleCollision(GameObject other)
	{
		int collisionEvents = this.m_ParticleSystem.GetCollisionEvents(other, this.m_CollisionEvents);
		for (int i = 0; i < collisionEvents; i++)
		{
			ExtinguishableFire component = this.m_CollisionEvents[i].colliderComponent.GetComponent<ExtinguishableFire>();
			if (component != null)
			{
				component.Extinguish();
			}
		}
	}

	// Token: 0x04000081 RID: 129
	private List<ParticleCollisionEvent> m_CollisionEvents = new List<ParticleCollisionEvent>();

	// Token: 0x04000082 RID: 130
	private ParticleSystem m_ParticleSystem;
}
﻿using System;
using UnityEngine;

// Token: 0x02000022 RID: 34
[Serializable]
public class ParticleExamples
{
	// Token: 0x04000083 RID: 131
	public string title;

	// Token: 0x04000084 RID: 132
	[TextArea]
	public string description;

	// Token: 0x04000085 RID: 133
	public bool isWeaponEffect;

	// Token: 0x04000086 RID: 134
	public GameObject particleSystemGO;

	// Token: 0x04000087 RID: 135
	public Vector3 particlePosition;

	// Token: 0x04000088 RID: 136
	public Vector3 particleRotation;
}
﻿using System;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000023 RID: 35
public class ParticleMenu : MonoBehaviour
{
	// Token: 0x060000EF RID: 239 RVA: 0x00005DB4 File Offset: 0x00003FB4
	private void Start()
	{
		this.Navigate(0);
		this.currentIndex = 0;
	}

	// Token: 0x060000F0 RID: 240 RVA: 0x00005DC4 File Offset: 0x00003FC4
	public void Navigate(int i)
	{
		this.currentIndex = (this.particleSystems.Length + this.currentIndex + i) % this.particleSystems.Length;
		if (this.currentGO != null)
		{
			UnityEngine.Object.Destroy(this.currentGO);
		}
		this.currentGO = UnityEngine.Object.Instantiate<GameObject>(this.particleSystems[this.currentIndex].particleSystemGO, this.spawnLocation.position + this.particleSystems[this.currentIndex].particlePosition, Quaternion.Euler(this.particleSystems[this.currentIndex].particleRotation));
		this.gunGameObject.SetActive(this.particleSystems[this.currentIndex].isWeaponEffect);
		this.title.text = this.particleSystems[this.currentIndex].title;
		this.description.text = this.particleSystems[this.currentIndex].description;
		this.navigationDetails.text = this.currentIndex + 1 + " out of " + this.particleSystems.Length.ToString();
	}

	// Token: 0x04000089 RID: 137
	public ParticleExamples[] particleSystems;

	// Token: 0x0400008A RID: 138
	public GameObject gunGameObject;

	// Token: 0x0400008B RID: 139
	private int currentIndex;

	// Token: 0x0400008C RID: 140
	private GameObject currentGO;

	// Token: 0x0400008D RID: 141
	public Transform spawnLocation;

	// Token: 0x0400008E RID: 142
	public Text title;

	// Token: 0x0400008F RID: 143
	public Text description;

	// Token: 0x04000090 RID: 144
	public Text navigationDetails;
}
﻿using System;
using MarsFPSKit;
using Photon.Pun;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000E5 RID: 229
public class PauseMenuView : BaseMenuView
{
	// Token: 0x060005C3 RID: 1475 RVA: 0x0001AA48 File Offset: 0x00018C48
	private void OnEnable()
	{
		this._resumeButton.onClick.AddListener(delegate()
		{
			this._menuController.HideCurrentMenu();
		});
		this._changeTeamButton.onClick.AddListener(delegate()
		{
			this.ChangeTeam();
		});
		this._ingameMain.OnPlayerChanged += this.SetUpPauseMenu;
	}

	// Token: 0x060005C4 RID: 1476 RVA: 0x0001AAA4 File Offset: 0x00018CA4
	private void OnDisable()
	{
		this._resumeButton.onClick.RemoveListener(delegate()
		{
			this._menuController.HideCurrentMenu();
		});
		this._changeTeamButton.onClick.RemoveListener(delegate()
		{
			this.ChangeTeam();
		});
		this._ingameMain.OnPlayerChanged -= this.SetUpPauseMenu;
	}

	// Token: 0x060005C5 RID: 1477 RVA: 0x00019405 File Offset: 0x00017605
	public override void MenuInit(ISwitchableMenu menuController)
	{
		base.MenuInit(menuController);
	}

	// Token: 0x060005C6 RID: 1478 RVA: 0x00011605 File Offset: 0x0000F805
	public override void ShowMenu(bool isAnimated)
	{
		base.ShowMenu(isAnimated);
	}

	// Token: 0x060005C7 RID: 1479 RVA: 0x0001160E File Offset: 0x0000F80E
	public override void HideMenu(bool isAnimated)
	{
		base.HideMenu(isAnimated);
	}

	// Token: 0x060005C8 RID: 1480 RVA: 0x0001AB00 File Offset: 0x00018D00
	private void SetUpPauseMenu(bool myPlayer)
	{
		if (myPlayer)
		{
			this._changeTeamButtonText.text = LocalizationController.Instance.GetText("PauseMenu", "Suicide");
		}
		else
		{
			this._changeTeamButtonText.text = LocalizationController.Instance.GetText("PauseMenu", "Change Team");
		}
		if (myPlayer)
		{
			this._resumeButtonText.text = LocalizationController.Instance.GetText("PauseMenu", "Resume");
			return;
		}
		if (this._ingameMain.currentGameModeBehaviour.CanSpawn(this._ingameMain, PhotonNetwork.LocalPlayer))
		{
			this._resumeButtonText.text = LocalizationController.Instance.GetText("PauseMenu", "Spawn");
			return;
		}
		this._resumeButtonText.text = LocalizationController.Instance.GetText("PauseMenu", "Close");
	}

	// Token: 0x060005C9 RID: 1481 RVA: 0x0001ABD0 File Offset: 0x00018DD0
	private void ChangeTeam()
	{
		if (!this._menuController.MainMenuData.IsTeamChangeble && this._ingameMain.IsSpawnedOnce)
		{
			return;
		}
		if (!this._ingameMain.myPlayer)
		{
			this._menuController.SwitchMenu(MenuType.ChooseTeam);
			return;
		}
		this._ingameMain.myPlayer.Suicide();
		this.SetUpPauseMenu(false);
	}

	// Token: 0x040004FF RID: 1279
	[SerializeField]
	private Kit_IngameMain _ingameMain;

	// Token: 0x04000500 RID: 1280
	[SerializeField]
	private Button _resumeButton;

	// Token: 0x04000501 RID: 1281
	[SerializeField]
	private Button _changeTeamButton;

	// Token: 0x04000502 RID: 1282
	[SerializeField]
	private TextMeshProUGUI _resumeButtonText;

	// Token: 0x04000503 RID: 1283
	[SerializeField]
	private TextMeshProUGUI _changeTeamButtonText;
}
﻿using System;
using ExitGames.Client.Photon;
using MarsFPSKit;
using Photon.Pun;
using Photon.Realtime;

// Token: 0x02000061 RID: 97
public class PhotonGRoupController : MonoBehaviourPunCallbacks, IOnEventCallback
{
	// Token: 0x06000283 RID: 643 RVA: 0x0000D77F File Offset: 0x0000B97F
	private void Start()
	{
		this._wait = new WaitProcess();
	}

	// Token: 0x06000284 RID: 644 RVA: 0x0000D78C File Offset: 0x0000B98C
	public override void OnDisconnected(DisconnectCause cause)
	{
		base.OnDisconnected(cause);
		if (this._isJoining)
		{
			PhotonNetwork.AuthValues = LoginController.Instance.GetAuthenticationValues();
			PhotonNetwork.ConnectToBestCloudServer();
		}
	}

	// Token: 0x06000285 RID: 645 RVA: 0x0000D7B2 File Offset: 0x0000B9B2
	public override void OnConnectedToMaster()
	{
		base.OnConnectedToMaster();
		if (this._isJoining)
		{
			this._wait.StartProcess(delegate
			{
				PhotonNetwork.JoinRoom(this._targetRoomName, null);
				this._isJoining = false;
			}, this._waitTime);
		}
	}

	// Token: 0x06000286 RID: 646 RVA: 0x0000D7E0 File Offset: 0x0000B9E0
	public void OnEvent(EventData photonEvent)
	{
		byte code = photonEvent.Code;
		object customData = photonEvent.CustomData;
		if (code == Kit_EventIDs.changeRoomEvent)
		{
			this._targetRoomName = (string)customData;
			this._isJoining = true;
			PhotonNetwork.LeaveRoom(true);
		}
	}

	// Token: 0x040001BD RID: 445
	private string _targetRoomName;

	// Token: 0x040001BE RID: 446
	private static PhotonGRoupController _instance;

	// Token: 0x040001BF RID: 447
	private bool _isJoining;

	// Token: 0x040001C0 RID: 448
	private WaitProcess _wait;

	// Token: 0x040001C1 RID: 449
	private float _waitTime = 0.1f;
}
﻿using System;

// Token: 0x02000017 RID: 23
public static class PhotonNetworkExtension
{
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using Photon.Pun;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000B6 RID: 182
public class PlantingBombHUD : Kit_GameModeHUDBase
{
	// Token: 0x06000447 RID: 1095 RVA: 0x00013394 File Offset: 0x00011594
	private void Awake()
	{
		this._waitProcess = new WaitProcess();
		this._bombSetupLerp = new LerpProcess();
		this._bombSetupWait = new WaitProcess();
		this._bombPlaces = UnityEngine.Object.FindObjectOfType<BombSpawner>().BombPlaces;
		this._width = (float)Screen.width;
		this._height = (float)Screen.height;
		this._bombFinder = new ObserveProcess();
		this._bombFinder.StartProcess(delegate
		{
			this._bombView = UnityEngine.Object.FindObjectOfType<BombView>();
			if (this._bombView)
			{
				this._bombTransform = this._bombView.transform;
				this._bombFinder.EndProcess();
			}
		});
		this._roundCompleteAnimation.HideRoundComplete();
	}

	// Token: 0x06000448 RID: 1096 RVA: 0x00013417 File Offset: 0x00011617
	private void OnDisable()
	{
		this._bombGameMode.OnRoundComplete -= this.SetRoundComplete;
	}

	// Token: 0x06000449 RID: 1097 RVA: 0x00013430 File Offset: 0x00011630
	public void OnBombSetuped(Kit_PlayerBehaviour pb)
	{
		Debug.Log("OnBombSetuped");
		this.BombProgressImage.fillAmount = 0f;
		this._roundCompleteText.text = LocalizationController.Instance.GetText("HUD", this._bombSetupedString);
		this._roundCompleteText.enabled = true;
	}

	// Token: 0x0600044A RID: 1098 RVA: 0x00013483 File Offset: 0x00011683
	public void ChangeSides()
	{
		this._changeSides.Test();
		this._roundCompleteText.enabled = false;
		this._roundCompleteAnimation.HideRoundComplete();
	}

	// Token: 0x0600044B RID: 1099 RVA: 0x00005F78 File Offset: 0x00004178
	public void DeactivateCHangeSideUI()
	{
	}

	// Token: 0x0600044C RID: 1100 RVA: 0x000134A8 File Offset: 0x000116A8
	private bool IsArraysEquals(int[] arr1, int[] arr2)
	{
		if (arr1.Length == arr2.Length)
		{
			for (int i = 0; i < arr1.Length; i++)
			{
				if (arr1[i] != arr2[i])
				{
					return false;
				}
			}
			return true;
		}
		return false;
	}

	// Token: 0x0600044D RID: 1101 RVA: 0x000134DC File Offset: 0x000116DC
	private Kit_PlayerBehaviour GetPlayer(Kit_IngameMain main)
	{
		List<Kit_PlayerBehaviour> allActivePlayers = main.allActivePlayers;
		int actorNumber = PhotonNetwork.LocalPlayer.ActorNumber;
		for (int i = 0; i < allActivePlayers.Count; i++)
		{
			if (allActivePlayers[i].id == actorNumber && !allActivePlayers[i].isBot)
			{
				return allActivePlayers[i];
			}
		}
		return null;
	}

	// Token: 0x0600044E RID: 1102 RVA: 0x00013534 File Offset: 0x00011734
	private void BombSetuping(Kit_PlayerBehaviour pb)
	{
		if (!pb.IsBombSetuping)
		{
			this.BombProgressImage.fillAmount = 0f;
			return;
		}
		if (pb.SetupingProgress <= 1f)
		{
			this.BombProgressImage.fillAmount = pb.SetupingProgress;
			pb.SetupingProgress += pb.Speed * Time.deltaTime;
			return;
		}
		pb.EndSetupBomb();
	}

	// Token: 0x0600044F RID: 1103 RVA: 0x00013598 File Offset: 0x00011798
	private void SetUpIconsPositions()
	{
		for (int i = 0; i < this._bombPlaces.Length; i++)
		{
			Vector3 vector = this._camera.WorldToScreenPoint(this._bombPlaces[i].position);
			float x = Mathf.Clamp(vector.x, 0f, this._width);
			float y = Mathf.Clamp(vector.y, 0f, this._height);
			vector = new Vector3(x, y, vector.z);
			if (vector.z >= 0f)
			{
				this._bombPlaceIcons[i].anchoredPosition = vector;
			}
			else
			{
				this._bombPlaceIcons[i].anchoredPosition = Vector2.zero;
			}
		}
		if (this._bombGameMode.IsBombIconVisible())
		{
			if (this._bombView != null && this._bombTransform != null && this._bombView.IsActive)
			{
				if (!this._bombIcon.gameObject.activeInHierarchy)
				{
					this._bombIcon.gameObject.SetActive(true);
				}
				Vector3 vector2 = this._camera.WorldToScreenPoint(this._bombTransform.position);
				float x2 = Mathf.Clamp(vector2.x, 0f, this._width);
				float y2 = Mathf.Clamp(vector2.y, 0f, this._height);
				vector2 = new Vector3(x2, y2, vector2.z);
				if (vector2.z >= 0f)
				{
					this._bombIcon.anchoredPosition = vector2;
					return;
				}
				this._bombIcon.anchoredPosition = Vector2.zero;
				return;
			}
			else if (this._bombIcon.gameObject.activeInHierarchy)
			{
				this._bombIcon.gameObject.SetActive(false);
				return;
			}
		}
		else if (this._bombIcon.gameObject.activeInHierarchy)
		{
			this._bombIcon.gameObject.SetActive(false);
		}
	}

	// Token: 0x06000450 RID: 1104 RVA: 0x00013782 File Offset: 0x00011982
	private void TestInput()
	{
		if (Input.GetKeyDown(KeyCode.U))
		{
			this.ChangeSides();
		}
		if (Input.GetKeyDown(KeyCode.J))
		{
			this._roundCompleteAnimation.ShowRoundComplete();
		}
	}

	// Token: 0x06000451 RID: 1105 RVA: 0x000137A8 File Offset: 0x000119A8
	public override void HUDUpdate(Kit_IngameMain main)
	{
		if (main.currentGameModeBehaviour.AreEnoughPlayersThere(main) || main.hasGameModeStarted)
		{
			this.roundedRestSeconds = Mathf.CeilToInt(main.timer);
			this.displaySeconds = this.roundedRestSeconds % 60;
			this.displayMinutes = this.roundedRestSeconds / 60;
			this.timer.text = string.Format("{0:00} : {1:00}", this.displayMinutes, this.displaySeconds);
			this.timer.enabled = true;
			if (main.currentGameModeRuntimeData != null && main.currentGameModeRuntimeData.GetType() == typeof(PlantingBombRuntimeData))
			{
				PlantingBombRuntimeData drd = main.currentGameModeRuntimeData as PlantingBombRuntimeData;
				if (!this._isInitialized)
				{
					this._bombGameMode = (PvPGMBPlantingBomb)main.currentGameModeBehaviour;
					this._bombGameMode.OnRoundComplete += this.SetRoundComplete;
					this._isInitialized = true;
				}
				this.SetBombInfo(main, drd);
			}
		}
		else
		{
			this.timer.enabled = false;
		}
		this.SetUpIconsPositions();
	}

	// Token: 0x06000452 RID: 1106 RVA: 0x000138B7 File Offset: 0x00011AB7
	public override void HUDInitialize(Kit_IngameMain main)
	{
		this._camera = main.mainCamera;
		this._bombGameMode = (PvPGMBPlantingBomb)main.currentGameModeBehaviour;
	}

	// Token: 0x06000453 RID: 1107 RVA: 0x000138D8 File Offset: 0x00011AD8
	private void SetPoints(Kit_IngameMain main, PlantingBombRuntimeData drd)
	{
		if (this.pointsActive.Count == 0)
		{
			for (int i = 0; i < Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, main.currentGameModeBehaviour.maximumAmountOfTeams); i++)
			{
				GameObject gameObject = UnityEngine.Object.Instantiate<GameObject>(this.pointsPrefab, this.pointsGo, false);
				this.pointsActive.Add(gameObject.GetComponentInChildren<TextMeshProUGUI>());
				this.pointsActive[i].color = main.gameInformation.allPvpTeams[i].teamColor;
			}
		}
		if (this.SuperPointsActive.Count == 0)
		{
			for (int j = 0; j < Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, main.currentGameModeBehaviour.maximumAmountOfTeams); j++)
			{
				GameObject gameObject2 = UnityEngine.Object.Instantiate<GameObject>(this.pointsPrefab, this.pointsSuperGo, false);
				this.SuperPointsActive.Add(gameObject2.GetComponentInChildren<TextMeshProUGUI>());
				this.SuperPointsActive[j].color = main.gameInformation.allPvpTeams[j].teamColor;
			}
		}
		for (int k = 0; k < drd.teamPoints.Length; k++)
		{
			this.pointsActive[k].text = drd.teamPoints[k].ToString();
		}
		for (int l = 0; l < drd.teamSuperPoints.Length; l++)
		{
			this.SuperPointsActive[l].text = drd.teamSuperPoints[l].ToString();
		}
	}

	// Token: 0x06000454 RID: 1108 RVA: 0x00013A58 File Offset: 0x00011C58
	private void SetRoundComplete(Kit_IngameMain main, PlantingBombRuntimeData drd)
	{
		if (!this.IsArraysEquals(this.oldSuperData, drd.teamSuperPoints))
		{
			this._roundCompleteText.enabled = false;
			for (int i = 0; i < this.oldData.Length; i++)
			{
				this.oldData[i] = drd.teamPoints[i];
			}
			return;
		}
		if (!this.IsArraysEquals(this.oldData, drd.teamPoints))
		{
			this._roundCompleteText.enabled = false;
			this._roundCompleteAnimation.ShowRoundComplete();
			for (int j = 0; j < this.oldData.Length; j++)
			{
				this.oldData[j] = drd.teamPoints[j];
			}
		}
	}

	// Token: 0x06000455 RID: 1109 RVA: 0x00013AF7 File Offset: 0x00011CF7
	private void SetRoundComplete()
	{
		this._roundCompleteText.enabled = false;
		this._roundCompleteAnimation.ShowRoundComplete();
	}

	// Token: 0x06000456 RID: 1110 RVA: 0x00013B10 File Offset: 0x00011D10
	private void SetBombInfo(Kit_IngameMain main, PlantingBombRuntimeData drd)
	{
		if (!this._ourPlayer)
		{
			this._ourPlayer = main.myPlayer;
		}
		if (this._ourPlayer)
		{
			this.BombUI.enabled = this._ourPlayer.IsBomb;
			this._setupBombText.text = ((this._ourPlayer.IsInBombPlace && !this._ourPlayer.IsBombSetuping) ? LocalizationController.Instance.GetText("HUD", this._setBombText) : "");
			this.BombSetuping(this._ourPlayer);
			return;
		}
		this.BombUI.enabled = false;
		this._setupBombText.text = "";
		this.BombProgressImage.fillAmount = 0f;
	}

	// Token: 0x0400033A RID: 826
	[SerializeField]
	private TextMeshProUGUI _roundCompleteText;

	// Token: 0x0400033B RID: 827
	[SerializeField]
	private TextMeshProUGUI _setupBombText;

	// Token: 0x0400033C RID: 828
	[SerializeField]
	private RectTransform[] _bombPlaceIcons;

	// Token: 0x0400033D RID: 829
	[SerializeField]
	private RectTransform _bombIcon;

	// Token: 0x0400033E RID: 830
	[SerializeField]
	private ChangeObjectives _changeSides;

	// Token: 0x0400033F RID: 831
	[SerializeField]
	private RoundCompleteAnimation _roundCompleteAnimation;

	// Token: 0x04000340 RID: 832
	public TextMeshProUGUI timer;

	// Token: 0x04000341 RID: 833
	public RectTransform pointsGo;

	// Token: 0x04000342 RID: 834
	public RectTransform pointsSuperGo;

	// Token: 0x04000343 RID: 835
	public GameObject pointsPrefab;

	// Token: 0x04000344 RID: 836
	public List<TextMeshProUGUI> pointsActive = new List<TextMeshProUGUI>();

	// Token: 0x04000345 RID: 837
	public List<TextMeshProUGUI> SuperPointsActive = new List<TextMeshProUGUI>();

	// Token: 0x04000346 RID: 838
	public Image BombUI;

	// Token: 0x04000347 RID: 839
	public Image BombProgressImage;

	// Token: 0x04000348 RID: 840
	private int roundedRestSeconds;

	// Token: 0x04000349 RID: 841
	private int displaySeconds;

	// Token: 0x0400034A RID: 842
	private int displayMinutes;

	// Token: 0x0400034B RID: 843
	private int[] oldData = new int[2];

	// Token: 0x0400034C RID: 844
	private int[] oldSuperData = new int[2];

	// Token: 0x0400034D RID: 845
	private WaitProcess _waitProcess;

	// Token: 0x0400034E RID: 846
	private float _waitTime = 5f;

	// Token: 0x0400034F RID: 847
	private LerpProcess _bombSetupLerp;

	// Token: 0x04000350 RID: 848
	private WaitProcess _bombSetupWait;

	// Token: 0x04000351 RID: 849
	private float _bombSetupTime;

	// Token: 0x04000352 RID: 850
	private Kit_PlayerBehaviour _ourPlayer;

	// Token: 0x04000353 RID: 851
	private string _setBombText = "Press F to Setup the Bomb";

	// Token: 0x04000354 RID: 852
	private string _roundCompleteString = "Round Complete";

	// Token: 0x04000355 RID: 853
	private string _bombSetupedString = "The bomb was planted!";

	// Token: 0x04000356 RID: 854
	private Transform[] _bombPlaces;

	// Token: 0x04000357 RID: 855
	private Camera _camera;

	// Token: 0x04000358 RID: 856
	private float _width;

	// Token: 0x04000359 RID: 857
	private float _height;

	// Token: 0x0400035A RID: 858
	private Transform _bombTransform;

	// Token: 0x0400035B RID: 859
	private BombView _bombView;

	// Token: 0x0400035C RID: 860
	private PvPGMBPlantingBomb _bombGameMode;

	// Token: 0x0400035D RID: 861
	private ObserveProcess _bombFinder;

	// Token: 0x0400035E RID: 862
	private bool _isInitialized;
}
﻿using System;

// Token: 0x020000B7 RID: 183
public class PlantingBombRuntimeData : ITeamGameMode
{
	// Token: 0x17000053 RID: 83
	// (get) Token: 0x06000459 RID: 1113 RVA: 0x00013C77 File Offset: 0x00011E77
	public int[] TeamPoints
	{
		get
		{
			return this.teamCommonPoints;
		}
	}

	// Token: 0x0400035F RID: 863
	public int[] teamPoints;

	// Token: 0x04000360 RID: 864
	public int[] teamSuperPoints;

	// Token: 0x04000361 RID: 865
	public int[] teamCommonPoints;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using UnityEngine;

// Token: 0x020000E6 RID: 230
public class PlayerAllModels : MonoBehaviour
{
	// Token: 0x060005CF RID: 1487 RVA: 0x0001AC49 File Offset: 0x00018E49
	public void ViewInit(PlayerModelChooseScreenView screenView)
	{
		this._screenView = screenView;
	}

	// Token: 0x060005D0 RID: 1488 RVA: 0x0001AC52 File Offset: 0x00018E52
	public void SetView(Kit_Team team)
	{
		this._team = team;
		this.ClearContent();
		this.SetPlayerModels();
		this._currentPlayerModel = null;
		this.SetStartValue();
	}

	// Token: 0x060005D1 RID: 1489 RVA: 0x0001AC74 File Offset: 0x00018E74
	public void SelectPlayerModel(PlayerModelView playerModel)
	{
		PlayerModelView currentPlayerModel = this._currentPlayerModel;
		if (currentPlayerModel != null)
		{
			currentPlayerModel.Deselect();
		}
		this._currentPlayerModel = playerModel;
		this._currentPlayerModel.Select();
		LoadoutService.Instance.PlayerModelSelect(this._playersModelsViews.IndexOf(playerModel));
		this._screenView.SelectModel();
	}

	// Token: 0x060005D2 RID: 1490 RVA: 0x0001ACC8 File Offset: 0x00018EC8
	public void SetStartValue()
	{
		int currentSelectedteamForPlayerModel = LoadoutService.Instance.CurrentSelectedteamForPlayerModel;
		int num = LoadoutService.Instance.GetSelecetedLoadout().teamLoadout[currentSelectedteamForPlayerModel].playerModelID;
		num = ((num >= this._playersModelsViews.Count) ? (this._playersModelsViews.Count - 1) : num);
		PlayerModelView currentPlayerModel = this._currentPlayerModel;
		if (currentPlayerModel != null)
		{
			currentPlayerModel.Deselect();
		}
		this._currentPlayerModel = this._playersModelsViews[num];
		this._currentPlayerModel.Select();
		LoadoutService.Instance.PlayerModelSelect(num);
		this._screenView.SelectModel();
	}

	// Token: 0x060005D3 RID: 1491 RVA: 0x0001AD5C File Offset: 0x00018F5C
	private void ClearContent()
	{
		if (this._playersModelsViews != null && this._playersModelsViews.Count > 0)
		{
			for (int i = 0; i < this._playersModelsViews.Count; i++)
			{
				UnityEngine.Object.Destroy(this._playersModelsViews[i].gameObject);
			}
		}
		this._playersModelsViews = new List<PlayerModelView>();
	}

	// Token: 0x060005D4 RID: 1492 RVA: 0x0001ADB8 File Offset: 0x00018FB8
	private void SetPlayerModels()
	{
		Kit_PlayerModelInformation[] playerModels = this._team.playerModels;
		for (int i = 0; i < playerModels.Length; i++)
		{
			PlayerModelView playerModelView = UnityEngine.Object.Instantiate<PlayerModelView>(this._playerModelViewPrefab, this._contentTransform);
			playerModelView.ViewInit(this, playerModels[i]);
			this._playersModelsViews.Add(playerModelView);
		}
	}

	// Token: 0x04000504 RID: 1284
	[SerializeField]
	private PlayerModelView _playerModelViewPrefab;

	// Token: 0x04000505 RID: 1285
	[SerializeField]
	private RectTransform _contentTransform;

	// Token: 0x04000506 RID: 1286
	private List<PlayerModelView> _playersModelsViews;

	// Token: 0x04000507 RID: 1287
	private PlayerModelView _currentPlayerModel;

	// Token: 0x04000508 RID: 1288
	private PlayerModelChooseScreenView _screenView;

	// Token: 0x04000509 RID: 1289
	private Kit_Team _team;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using UnityEngine;

// Token: 0x020000E7 RID: 231
public class PlayerAllTeams : MonoBehaviour
{
	// Token: 0x060005D6 RID: 1494 RVA: 0x0001AE07 File Offset: 0x00019007
	public void ViewInit(PlayerModelChooseScreenView screenView, Kit_GameInformation game)
	{
		this._screenView = screenView;
		this._game = game;
		this.SetTeams();
	}

	// Token: 0x060005D7 RID: 1495 RVA: 0x0001AE20 File Offset: 0x00019020
	public void SelectTeam(PlayerTeamView teamView, Kit_Team team)
	{
		PlayerTeamView currentTeam = this._currentTeam;
		if (currentTeam != null)
		{
			currentTeam.Deselect();
		}
		this._currentTeam = teamView;
		this._currentTeam.Select();
		this._screenView.SelectTeam(team);
		LoadoutService.Instance.PlayerModelSelectTeam(this._teams.IndexOf(teamView));
	}

	// Token: 0x060005D8 RID: 1496 RVA: 0x0001AE74 File Offset: 0x00019074
	public void SetStartValue()
	{
		PlayerTeamView currentTeam = this._currentTeam;
		if (currentTeam != null)
		{
			currentTeam.Deselect();
		}
		this._currentTeam = this._teams[LoadoutService.Instance.CurrentSelectedteamForPlayerModel];
		this._currentTeam.Select();
		this._screenView.SelectTeam(this._game.allPvpTeams[LoadoutService.Instance.CurrentSelectedteamForPlayerModel]);
	}

	// Token: 0x060005D9 RID: 1497 RVA: 0x0001AEDC File Offset: 0x000190DC
	private void SetTeams()
	{
		this._teams = new List<PlayerTeamView>();
		Kit_Team[] allPvpTeams = this._game.allPvpTeams;
		for (int i = 0; i < allPvpTeams.Length; i++)
		{
			PlayerTeamView playerTeamView = UnityEngine.Object.Instantiate<PlayerTeamView>(this._playerTeamViewPrefab, this._contentTransform);
			playerTeamView.ViewInit(this, allPvpTeams[i]);
			this._teams.Add(playerTeamView);
		}
	}

	// Token: 0x0400050A RID: 1290
	[SerializeField]
	private PlayerTeamView _playerTeamViewPrefab;

	// Token: 0x0400050B RID: 1291
	[SerializeField]
	private RectTransform _contentTransform;

	// Token: 0x0400050C RID: 1292
	private List<PlayerTeamView> _teams;

	// Token: 0x0400050D RID: 1293
	private PlayerTeamView _currentTeam;

	// Token: 0x0400050E RID: 1294
	private Kit_GameInformation _game;

	// Token: 0x0400050F RID: 1295
	private PlayerModelChooseScreenView _screenView;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using UnityEngine;

// Token: 0x020000E8 RID: 232
public class PlayerDrawer : MonoBehaviour
{
	// Token: 0x060005DB RID: 1499 RVA: 0x0001AF36 File Offset: 0x00019136
	public void ViewInit(Kit_GameInformation game)
	{
		this._game = game;
	}

	// Token: 0x060005DC RID: 1500 RVA: 0x0001AF40 File Offset: 0x00019140
	public void RedrawPlayerModel(bool onlyUpdateCustomization = false)
	{
		int currentSelectedteamForPlayerModel = LoadoutService.Instance.CurrentSelectedteamForPlayerModel;
		int currentSelectedWeaponCategory = LoadoutService.Instance.CurrentSelectedWeaponCategory;
		if (!onlyUpdateCustomization)
		{
			if (this._playerModelCurrent)
			{
				UnityEngine.Object.Destroy(this._playerModelCurrent.gameObject);
			}
			Loadout selecetedLoadout = LoadoutService.Instance.GetSelecetedLoadout();
			GameObject gameObject = UnityEngine.Object.Instantiate<GameObject>(this._game.allPvpTeams[currentSelectedteamForPlayerModel].playerModels[selecetedLoadout.teamLoadout[currentSelectedteamForPlayerModel].playerModelID].prefab, this._playerModelPreviewGo, false);
			this._playerModelCurrent = gameObject.GetComponent<Kit_ThirdPersonPlayerModel>();
			if (this._playerModelCurrent.anim)
			{
				this._playerModelIkHelper = this._playerModelCurrent.anim.gameObject.AddComponent<Kit_LoadoutIKHelper>();
				this._playerModelIkHelper.anim = this._playerModelCurrent.anim;
				this._playerModelIkHelper.applyIk = false;
			}
			this._playerModelCurrent.SetAnimType(this._game.allWeapons[LoadoutService.Instance.GetSelecetedLoadout().loadoutWeapons[currentSelectedWeaponCategory].weaponID].thirdPersonAnimType, true);
			this.RedrawWeapon(false);
		}
		this._playerModelCurrent.SetCustomizations(LoadoutService.Instance.GetSelecetedLoadout().teamLoadout[currentSelectedteamForPlayerModel].playerModelCustomizations, null);
		this.RedrawWeapon(true);
	}

	// Token: 0x060005DD RID: 1501 RVA: 0x0001B084 File Offset: 0x00019284
	public void RedrawWeapon(bool onlyUpdateCustomization = false)
	{
		if (!this._weaponPreviewObjectThirdPerson)
		{
			onlyUpdateCustomization = false;
		}
		int currentSelectedWeaponCategory = LoadoutService.Instance.CurrentSelectedWeaponCategory;
		if (onlyUpdateCustomization)
		{
			Kit_ThirdPersonWeaponRenderer component = this._weaponPreviewObjectThirdPerson.GetComponent<Kit_ThirdPersonWeaponRenderer>();
			if (component)
			{
				component.SetAttachments(LoadoutService.Instance.GetSelecetedLoadout().loadoutWeapons[currentSelectedWeaponCategory].attachments, null, null, null);
				return;
			}
		}
		else
		{
			if (this._weaponPreviewObjectThirdPerson)
			{
				UnityEngine.Object.Destroy(this._weaponPreviewObjectThirdPerson);
			}
			this._weaponPreviewObjectThirdPerson = UnityEngine.Object.Instantiate<GameObject>(this._game.allWeapons[LoadoutService.Instance.GetSelecetedLoadout().loadoutWeapons[currentSelectedWeaponCategory].weaponID].thirdPersonPrefab, this._playerModelCurrent.weaponsInHandsGo, false);
			this._playerModelCurrent.SetAnimType(this._game.allWeapons[LoadoutService.Instance.GetSelecetedLoadout().loadoutWeapons[currentSelectedWeaponCategory].weaponID].thirdPersonAnimType, true);
			Kit_ThirdPersonWeaponRenderer component2 = this._weaponPreviewObjectThirdPerson.GetComponent<Kit_ThirdPersonWeaponRenderer>();
			if (component2)
			{
				component2.SetAttachments(LoadoutService.Instance.GetSelecetedLoadout().loadoutWeapons[currentSelectedWeaponCategory].attachments, null, null, null);
				if (component2.leftHandIK.Length != 0)
				{
					this._playerModelIkHelper.leftHandGoal = component2.leftHandIK[Mathf.Clamp(this._playerModelCurrent.inverseKinematicID, 0, component2.leftHandIK.Length - 1)];
					this._playerModelIkHelper.applyIk = true;
					return;
				}
				this._playerModelIkHelper.applyIk = false;
			}
		}
	}

	// Token: 0x060005DE RID: 1502 RVA: 0x0001B1F1 File Offset: 0x000193F1
	public void RotateModel(Vector2 value)
	{
		this._playerModelRotateEmpty.Rotate(Vector3.up, -value.x);
	}

	// Token: 0x04000510 RID: 1296
	[SerializeField]
	private Transform _playerModelPreviewGo;

	// Token: 0x04000511 RID: 1297
	[SerializeField]
	private Transform _playerModelRotateEmpty;

	// Token: 0x04000512 RID: 1298
	[SerializeField]
	private Camera _playerRenderCamera;

	// Token: 0x04000513 RID: 1299
	private Kit_ThirdPersonPlayerModel _playerModelCurrent;

	// Token: 0x04000514 RID: 1300
	private Kit_LoadoutIKHelper _playerModelIkHelper;

	// Token: 0x04000515 RID: 1301
	public GameObject _weaponPreviewObjectThirdPerson;

	// Token: 0x04000516 RID: 1302
	private Kit_GameInformation _game;
}
﻿using System;
using MarsFPSKit;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000E9 RID: 233
public class PlayerModelChooseScreenView : BaseMenuView
{
	// Token: 0x060005E0 RID: 1504 RVA: 0x0001B20A File Offset: 0x0001940A
	private void OnEnable()
	{
		this._backButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(this._previousMenu);
			LoadoutService.Instance.Save();
		});
		this._dragView.OnMosueDrag += this._playerDrawer.RotateModel;
	}

	// Token: 0x060005E1 RID: 1505 RVA: 0x0001B244 File Offset: 0x00019444
	private void OnDisable()
	{
		this._backButton.onClick.RemoveListener(delegate()
		{
			this._menuController.SwitchMenu(this._previousMenu);
			LoadoutService.Instance.Save();
		});
		this._dragView.OnMosueDrag -= this._playerDrawer.RotateModel;
	}

	// Token: 0x060005E2 RID: 1506 RVA: 0x0001B280 File Offset: 0x00019480
	public override void MenuInit(ISwitchableMenu menuController)
	{
		base.MenuInit(menuController);
		this._game = Resources.Load<Kit_GameInformation>("Game");
		this._allTeams.ViewInit(this, this._game);
		this._allModels.ViewInit(this);
		this._playerDrawer.ViewInit(this._game);
		this.SetStartValue();
	}

	// Token: 0x060005E3 RID: 1507 RVA: 0x0001B2D9 File Offset: 0x000194D9
	public void SelectTeam(Kit_Team team)
	{
		this._allModels.SetView(team);
	}

	// Token: 0x060005E4 RID: 1508 RVA: 0x0001B2E7 File Offset: 0x000194E7
	public void SelectModel()
	{
		this._playerDrawer.RedrawPlayerModel(false);
	}

	// Token: 0x060005E5 RID: 1509 RVA: 0x0001B2F5 File Offset: 0x000194F5
	public void SetStartValue()
	{
		this._allTeams.SetStartValue();
	}

	// Token: 0x04000517 RID: 1303
	[SerializeField]
	private PlayerAllTeams _allTeams;

	// Token: 0x04000518 RID: 1304
	[SerializeField]
	private PlayerAllModels _allModels;

	// Token: 0x04000519 RID: 1305
	[SerializeField]
	private PlayerDrawer _playerDrawer;

	// Token: 0x0400051A RID: 1306
	[SerializeField]
	private Button _backButton;

	// Token: 0x0400051B RID: 1307
	[SerializeField]
	private DragView _dragView;

	// Token: 0x0400051C RID: 1308
	private Kit_GameInformation _game;
}
﻿using System;
using MarsFPSKit;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000EA RID: 234
public class PlayerModelView : MonoBehaviour
{
	// Token: 0x060005E9 RID: 1513 RVA: 0x0001B31F File Offset: 0x0001951F
	private void OnEnable()
	{
		this._playerModelButton.onClick.AddListener(delegate()
		{
			this._playerAllModels.SelectPlayerModel(this);
		});
	}

	// Token: 0x060005EA RID: 1514 RVA: 0x0001B33D File Offset: 0x0001953D
	private void OnDisable()
	{
		this._playerModelButton.onClick.RemoveListener(delegate()
		{
			this._playerAllModels.SelectPlayerModel(this);
		});
	}

	// Token: 0x060005EB RID: 1515 RVA: 0x0001B35B File Offset: 0x0001955B
	public void ViewInit(PlayerAllModels playerAllModels, Kit_PlayerModelInformation playerModel)
	{
		this._playerAllModels = playerAllModels;
		this._playerModel = playerModel;
		this._playerModelText.text = this._playerModel.displayName;
	}

	// Token: 0x060005EC RID: 1516 RVA: 0x0001B381 File Offset: 0x00019581
	public void Select()
	{
		this._playerModelText.color = Color.black;
		this._background.color = Color.white;
	}

	// Token: 0x060005ED RID: 1517 RVA: 0x0001B3A3 File Offset: 0x000195A3
	public void Deselect()
	{
		this._playerModelText.color = Color.white;
		this._background.color = Color.black;
	}

	// Token: 0x0400051D RID: 1309
	[SerializeField]
	private TextMeshProUGUI _playerModelText;

	// Token: 0x0400051E RID: 1310
	[SerializeField]
	private Button _playerModelButton;

	// Token: 0x0400051F RID: 1311
	[SerializeField]
	private Image _background;

	// Token: 0x04000520 RID: 1312
	private PlayerAllModels _playerAllModels;

	// Token: 0x04000521 RID: 1313
	private Kit_PlayerModelInformation _playerModel;
}
﻿using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x02000018 RID: 24
public class PlayerPrefsExtended
{
	// Token: 0x06000093 RID: 147 RVA: 0x00004B04 File Offset: 0x00002D04
	public static bool SetBool(string name, bool value)
	{
		try
		{
			PlayerPrefs.SetInt(name, value ? 1 : 0);
		}
		catch
		{
			return false;
		}
		return true;
	}

	// Token: 0x06000094 RID: 148 RVA: 0x00004B38 File Offset: 0x00002D38
	public static bool GetBool(string name)
	{
		return PlayerPrefs.GetInt(name) == 1;
	}

	// Token: 0x06000095 RID: 149 RVA: 0x00004B43 File Offset: 0x00002D43
	public static bool GetBool(string name, bool defaultValue)
	{
		return 1 == PlayerPrefs.GetInt(name, defaultValue ? 1 : 0);
	}

	// Token: 0x06000096 RID: 150 RVA: 0x00004B58 File Offset: 0x00002D58
	public static long GetLong(string key, long defaultValue)
	{
		int @int;
		int int2;
		PlayerPrefsExtended.SplitLong(defaultValue, out @int, out int2);
		@int = PlayerPrefs.GetInt(key + "_lowBits", @int);
		int2 = PlayerPrefs.GetInt(key + "_highBits", int2);
		return (long)((ulong)int2 << 32 | (ulong)@int);
	}

	// Token: 0x06000097 RID: 151 RVA: 0x00004B9C File Offset: 0x00002D9C
	public static long GetLong(string key)
	{
		int @int = PlayerPrefs.GetInt(key + "_lowBits");
		return (long)((ulong)PlayerPrefs.GetInt(key + "_highBits") << 32 | (ulong)@int);
	}

	// Token: 0x06000098 RID: 152 RVA: 0x00004BD1 File Offset: 0x00002DD1
	private static void SplitLong(long input, out int lowBits, out int highBits)
	{
		lowBits = (int)((uint)input);
		highBits = (int)((uint)(input >> 32));
	}

	// Token: 0x06000099 RID: 153 RVA: 0x00004BE0 File Offset: 0x00002DE0
	public static void SetLong(string key, long value)
	{
		int value2;
		int value3;
		PlayerPrefsExtended.SplitLong(value, out value2, out value3);
		PlayerPrefs.SetInt(key + "_lowBits", value2);
		PlayerPrefs.SetInt(key + "_highBits", value3);
	}

	// Token: 0x0600009A RID: 154 RVA: 0x00004C19 File Offset: 0x00002E19
	public static bool SetVector2(string key, Vector2 vector)
	{
		return PlayerPrefsExtended.SetFloatArray(key, new float[]
		{
			vector.x,
			vector.y
		});
	}

	// Token: 0x0600009B RID: 155 RVA: 0x00004C3C File Offset: 0x00002E3C
	private static Vector2 GetVector2(string key)
	{
		float[] floatArray = PlayerPrefsExtended.GetFloatArray(key);
		if (floatArray.Length < 2)
		{
			return Vector2.zero;
		}
		return new Vector2(floatArray[0], floatArray[1]);
	}

	// Token: 0x0600009C RID: 156 RVA: 0x00004C67 File Offset: 0x00002E67
	public static Vector2 GetVector2(string key, Vector2 defaultValue)
	{
		if (PlayerPrefs.HasKey(key))
		{
			return PlayerPrefsExtended.GetVector2(key);
		}
		return defaultValue;
	}

	// Token: 0x0600009D RID: 157 RVA: 0x00004C79 File Offset: 0x00002E79
	public static bool SetVector3(string key, Vector3 vector)
	{
		return PlayerPrefsExtended.SetFloatArray(key, new float[]
		{
			vector.x,
			vector.y,
			vector.z
		});
	}

	// Token: 0x0600009E RID: 158 RVA: 0x00004CA4 File Offset: 0x00002EA4
	public static Vector3 GetVector3(string key)
	{
		float[] floatArray = PlayerPrefsExtended.GetFloatArray(key);
		if (floatArray.Length < 3)
		{
			return Vector3.zero;
		}
		return new Vector3(floatArray[0], floatArray[1], floatArray[2]);
	}

	// Token: 0x0600009F RID: 159 RVA: 0x00004CD2 File Offset: 0x00002ED2
	public static Vector3 GetVector3(string key, Vector3 defaultValue)
	{
		if (PlayerPrefs.HasKey(key))
		{
			return PlayerPrefsExtended.GetVector3(key);
		}
		return defaultValue;
	}

	// Token: 0x060000A0 RID: 160 RVA: 0x00004CE4 File Offset: 0x00002EE4
	public static bool SetQuaternion(string key, Quaternion vector)
	{
		return PlayerPrefsExtended.SetFloatArray(key, new float[]
		{
			vector.x,
			vector.y,
			vector.z,
			vector.w
		});
	}

	// Token: 0x060000A1 RID: 161 RVA: 0x00004D18 File Offset: 0x00002F18
	public static Quaternion GetQuaternion(string key)
	{
		float[] floatArray = PlayerPrefsExtended.GetFloatArray(key);
		if (floatArray.Length < 4)
		{
			return Quaternion.identity;
		}
		return new Quaternion(floatArray[0], floatArray[1], floatArray[2], floatArray[3]);
	}

	// Token: 0x060000A2 RID: 162 RVA: 0x00004D49 File Offset: 0x00002F49
	public static Quaternion GetQuaternion(string key, Quaternion defaultValue)
	{
		if (PlayerPrefs.HasKey(key))
		{
			return PlayerPrefsExtended.GetQuaternion(key);
		}
		return defaultValue;
	}

	// Token: 0x060000A3 RID: 163 RVA: 0x00004D5B File Offset: 0x00002F5B
	public static bool SetColor(string key, Color color)
	{
		return PlayerPrefsExtended.SetFloatArray(key, new float[]
		{
			color.r,
			color.g,
			color.b,
			color.a
		});
	}

	// Token: 0x060000A4 RID: 164 RVA: 0x00004D90 File Offset: 0x00002F90
	public static Color GetColor(string key)
	{
		float[] floatArray = PlayerPrefsExtended.GetFloatArray(key);
		if (floatArray.Length < 4)
		{
			return new Color(0f, 0f, 0f, 0f);
		}
		return new Color(floatArray[0], floatArray[1], floatArray[2], floatArray[3]);
	}

	// Token: 0x060000A5 RID: 165 RVA: 0x00004DD5 File Offset: 0x00002FD5
	public static Color GetColor(string key, Color defaultValue)
	{
		if (PlayerPrefs.HasKey(key))
		{
			return PlayerPrefsExtended.GetColor(key);
		}
		return defaultValue;
	}

	// Token: 0x060000A6 RID: 166 RVA: 0x00004DE8 File Offset: 0x00002FE8
	public static bool SetBoolArray(string key, bool[] boolArray)
	{
		byte[] array = new byte[(boolArray.Length + 7) / 8 + 5];
		array[0] = Convert.ToByte(PlayerPrefsExtended.ArrayType.Bool);
		new BitArray(boolArray).CopyTo(array, 5);
		PlayerPrefsExtended.Initialize();
		PlayerPrefsExtended.ConvertInt32ToBytes(boolArray.Length, array);
		return PlayerPrefsExtended.SaveBytes(key, array);
	}

	// Token: 0x060000A7 RID: 167 RVA: 0x00004E34 File Offset: 0x00003034
	public static bool[] GetBoolArray(string key)
	{
		if (!PlayerPrefs.HasKey(key))
		{
			return new bool[0];
		}
		byte[] array = Convert.FromBase64String(PlayerPrefs.GetString(key));
		if (array.Length < 5)
		{
			Debug.LogError("Corrupt preference file for " + key);
			return new bool[0];
		}
		if (array[0] != 2)
		{
			Debug.LogError(key + " is not a boolean array");
			return new bool[0];
		}
		PlayerPrefsExtended.Initialize();
		byte[] array2 = new byte[array.Length - 5];
		Array.Copy(array, 5, array2, 0, array2.Length);
		BitArray bitArray = new BitArray(array2);
		bitArray.Length = PlayerPrefsExtended.ConvertBytesToInt32(array);
		bool[] array3 = new bool[bitArray.Count];
		bitArray.CopyTo(array3, 0);
		return array3;
	}

	// Token: 0x060000A8 RID: 168 RVA: 0x00004EDC File Offset: 0x000030DC
	public static bool[] GetBoolArray(string key, bool defaultValue, int defaultSize)
	{
		if (PlayerPrefs.HasKey(key))
		{
			return PlayerPrefsExtended.GetBoolArray(key);
		}
		bool[] array = new bool[defaultSize];
		for (int i = 0; i < defaultSize; i++)
		{
			array[i] = defaultValue;
		}
		return array;
	}

	// Token: 0x060000A9 RID: 169 RVA: 0x00004F10 File Offset: 0x00003110
	public static bool SetStringArray(string key, string[] stringArray)
	{
		byte[] array = new byte[stringArray.Length + 1];
		array[0] = Convert.ToByte(PlayerPrefsExtended.ArrayType.String);
		PlayerPrefsExtended.Initialize();
		for (int i = 0; i < stringArray.Length; i++)
		{
			if (stringArray[i] == null)
			{
				Debug.LogError("Can't save null entries in the string array when setting " + key);
				return false;
			}
			if (stringArray[i].Length > 255)
			{
				Debug.LogError("Strings cannot be longer than 255 characters when setting " + key);
				return false;
			}
			array[PlayerPrefsExtended.idx++] = (byte)stringArray[i].Length;
		}
		try
		{
			PlayerPrefs.SetString(key, Convert.ToBase64String(array) + "|" + string.Join("", stringArray));
		}
		catch
		{
			return false;
		}
		return true;
	}

	// Token: 0x060000AA RID: 170 RVA: 0x00004FD8 File Offset: 0x000031D8
	public static string[] GetStringArray(string key)
	{
		if (!PlayerPrefs.HasKey(key))
		{
			return new string[0];
		}
		string @string = PlayerPrefs.GetString(key);
		int num = @string.IndexOf("|"[0]);
		if (num < 4)
		{
			Debug.LogError("Corrupt preference file for " + key);
			return new string[0];
		}
		byte[] array = Convert.FromBase64String(@string.Substring(0, num));
		if (array[0] != 3)
		{
			Debug.LogError(key + " is not a string array");
			return new string[0];
		}
		PlayerPrefsExtended.Initialize();
		int num2 = array.Length - 1;
		string[] array2 = new string[num2];
		int num3 = num + 1;
		for (int i = 0; i < num2; i++)
		{
			int num4 = (int)array[PlayerPrefsExtended.idx++];
			if (num3 + num4 > @string.Length)
			{
				Debug.LogError("Corrupt preference file for " + key);
				return new string[0];
			}
			array2[i] = @string.Substring(num3, num4);
			num3 += num4;
		}
		return array2;
	}

	// Token: 0x060000AB RID: 171 RVA: 0x000050CC File Offset: 0x000032CC
	public static string[] GetStringArray(string key, string defaultValue, int defaultSize)
	{
		if (PlayerPrefs.HasKey(key))
		{
			return PlayerPrefsExtended.GetStringArray(key);
		}
		string[] array = new string[defaultSize];
		for (int i = 0; i < defaultSize; i++)
		{
			array[i] = defaultValue;
		}
		return array;
	}

	// Token: 0x060000AC RID: 172 RVA: 0x00005100 File Offset: 0x00003300
	public static bool SetIntArray(string key, int[] intArray)
	{
		return PlayerPrefsExtended.SetValue<int[]>(key, intArray, PlayerPrefsExtended.ArrayType.Int32, 1, new Action<int[], byte[], int>(PlayerPrefsExtended.ConvertFromInt));
	}

	// Token: 0x060000AD RID: 173 RVA: 0x00005117 File Offset: 0x00003317
	public static bool SetFloatArray(string key, float[] floatArray)
	{
		return PlayerPrefsExtended.SetValue<float[]>(key, floatArray, PlayerPrefsExtended.ArrayType.Float, 1, new Action<float[], byte[], int>(PlayerPrefsExtended.ConvertFromFloat));
	}

	// Token: 0x060000AE RID: 174 RVA: 0x0000512E File Offset: 0x0000332E
	public static bool SetVector2Array(string key, Vector2[] vector2Array)
	{
		return PlayerPrefsExtended.SetValue<Vector2[]>(key, vector2Array, PlayerPrefsExtended.ArrayType.Vector2, 2, new Action<Vector2[], byte[], int>(PlayerPrefsExtended.ConvertFromVector2));
	}

	// Token: 0x060000AF RID: 175 RVA: 0x00005145 File Offset: 0x00003345
	public static bool SetVector3Array(string key, Vector3[] vector3Array)
	{
		return PlayerPrefsExtended.SetValue<Vector3[]>(key, vector3Array, PlayerPrefsExtended.ArrayType.Vector3, 3, new Action<Vector3[], byte[], int>(PlayerPrefsExtended.ConvertFromVector3));
	}

	// Token: 0x060000B0 RID: 176 RVA: 0x0000515C File Offset: 0x0000335C
	public static bool SetQuaternionArray(string key, Quaternion[] quaternionArray)
	{
		return PlayerPrefsExtended.SetValue<Quaternion[]>(key, quaternionArray, PlayerPrefsExtended.ArrayType.Quaternion, 4, new Action<Quaternion[], byte[], int>(PlayerPrefsExtended.ConvertFromQuaternion));
	}

	// Token: 0x060000B1 RID: 177 RVA: 0x00005173 File Offset: 0x00003373
	public static bool SetColorArray(string key, Color[] colorArray)
	{
		return PlayerPrefsExtended.SetValue<Color[]>(key, colorArray, PlayerPrefsExtended.ArrayType.Color, 4, new Action<Color[], byte[], int>(PlayerPrefsExtended.ConvertFromColor));
	}

	// Token: 0x060000B2 RID: 178 RVA: 0x0000518C File Offset: 0x0000338C
	private static bool SetValue<T>(string key, T array, PlayerPrefsExtended.ArrayType arrayType, int vectorNumber, Action<T, byte[], int> convert) where T : IList
	{
		byte[] array2 = new byte[4 * array.Count * vectorNumber + 1];
		array2[0] = Convert.ToByte(arrayType);
		PlayerPrefsExtended.Initialize();
		for (int i = 0; i < array.Count; i++)
		{
			convert(array, array2, i);
		}
		return PlayerPrefsExtended.SaveBytes(key, array2);
	}

	// Token: 0x060000B3 RID: 179 RVA: 0x000051EE File Offset: 0x000033EE
	private static void ConvertFromInt(int[] array, byte[] bytes, int i)
	{
		PlayerPrefsExtended.ConvertInt32ToBytes(array[i], bytes);
	}

	// Token: 0x060000B4 RID: 180 RVA: 0x000051F9 File Offset: 0x000033F9
	private static void ConvertFromFloat(float[] array, byte[] bytes, int i)
	{
		PlayerPrefsExtended.ConvertFloatToBytes(array[i], bytes);
	}

	// Token: 0x060000B5 RID: 181 RVA: 0x00005204 File Offset: 0x00003404
	private static void ConvertFromVector2(Vector2[] array, byte[] bytes, int i)
	{
		PlayerPrefsExtended.ConvertFloatToBytes(array[i].x, bytes);
		PlayerPrefsExtended.ConvertFloatToBytes(array[i].y, bytes);
	}

	// Token: 0x060000B6 RID: 182 RVA: 0x0000522A File Offset: 0x0000342A
	private static void ConvertFromVector3(Vector3[] array, byte[] bytes, int i)
	{
		PlayerPrefsExtended.ConvertFloatToBytes(array[i].x, bytes);
		PlayerPrefsExtended.ConvertFloatToBytes(array[i].y, bytes);
		PlayerPrefsExtended.ConvertFloatToBytes(array[i].z, bytes);
	}

	// Token: 0x060000B7 RID: 183 RVA: 0x00005264 File Offset: 0x00003464
	private static void ConvertFromQuaternion(Quaternion[] array, byte[] bytes, int i)
	{
		PlayerPrefsExtended.ConvertFloatToBytes(array[i].x, bytes);
		PlayerPrefsExtended.ConvertFloatToBytes(array[i].y, bytes);
		PlayerPrefsExtended.ConvertFloatToBytes(array[i].z, bytes);
		PlayerPrefsExtended.ConvertFloatToBytes(array[i].w, bytes);
	}

	// Token: 0x060000B8 RID: 184 RVA: 0x000052BC File Offset: 0x000034BC
	private static void ConvertFromColor(Color[] array, byte[] bytes, int i)
	{
		PlayerPrefsExtended.ConvertFloatToBytes(array[i].r, bytes);
		PlayerPrefsExtended.ConvertFloatToBytes(array[i].g, bytes);
		PlayerPrefsExtended.ConvertFloatToBytes(array[i].b, bytes);
		PlayerPrefsExtended.ConvertFloatToBytes(array[i].a, bytes);
	}

	// Token: 0x060000B9 RID: 185 RVA: 0x00005314 File Offset: 0x00003514
	public static int[] GetIntArray(string key)
	{
		List<int> list = new List<int>();
		PlayerPrefsExtended.GetValue<List<int>>(key, list, PlayerPrefsExtended.ArrayType.Int32, 1, new Action<List<int>, byte[]>(PlayerPrefsExtended.ConvertToInt));
		return list.ToArray();
	}

	// Token: 0x060000BA RID: 186 RVA: 0x00005344 File Offset: 0x00003544
	public static int[] GetIntArray(string key, int defaultValue, int defaultSize)
	{
		if (PlayerPrefs.HasKey(key))
		{
			return PlayerPrefsExtended.GetIntArray(key);
		}
		int[] array = new int[defaultSize];
		for (int i = 0; i < defaultSize; i++)
		{
			array[i] = defaultValue;
		}
		return array;
	}

	// Token: 0x060000BB RID: 187 RVA: 0x00005378 File Offset: 0x00003578
	public static float[] GetFloatArray(string key)
	{
		List<float> list = new List<float>();
		PlayerPrefsExtended.GetValue<List<float>>(key, list, PlayerPrefsExtended.ArrayType.Float, 1, new Action<List<float>, byte[]>(PlayerPrefsExtended.ConvertToFloat));
		return list.ToArray();
	}

	// Token: 0x060000BC RID: 188 RVA: 0x000053A8 File Offset: 0x000035A8
	public static float[] GetFloatArray(string key, float defaultValue, int defaultSize)
	{
		if (PlayerPrefs.HasKey(key))
		{
			return PlayerPrefsExtended.GetFloatArray(key);
		}
		float[] array = new float[defaultSize];
		for (int i = 0; i < defaultSize; i++)
		{
			array[i] = defaultValue;
		}
		return array;
	}

	// Token: 0x060000BD RID: 189 RVA: 0x000053DC File Offset: 0x000035DC
	public static Vector2[] GetVector2Array(string key)
	{
		List<Vector2> list = new List<Vector2>();
		PlayerPrefsExtended.GetValue<List<Vector2>>(key, list, PlayerPrefsExtended.ArrayType.Vector2, 2, new Action<List<Vector2>, byte[]>(PlayerPrefsExtended.ConvertToVector2));
		return list.ToArray();
	}

	// Token: 0x060000BE RID: 190 RVA: 0x0000540C File Offset: 0x0000360C
	public static Vector2[] GetVector2Array(string key, Vector2 defaultValue, int defaultSize)
	{
		if (PlayerPrefs.HasKey(key))
		{
			return PlayerPrefsExtended.GetVector2Array(key);
		}
		Vector2[] array = new Vector2[defaultSize];
		for (int i = 0; i < defaultSize; i++)
		{
			array[i] = defaultValue;
		}
		return array;
	}

	// Token: 0x060000BF RID: 191 RVA: 0x00005444 File Offset: 0x00003644
	public static Vector3[] GetVector3Array(string key)
	{
		List<Vector3> list = new List<Vector3>();
		PlayerPrefsExtended.GetValue<List<Vector3>>(key, list, PlayerPrefsExtended.ArrayType.Vector3, 3, new Action<List<Vector3>, byte[]>(PlayerPrefsExtended.ConvertToVector3));
		return list.ToArray();
	}

	// Token: 0x060000C0 RID: 192 RVA: 0x00005474 File Offset: 0x00003674
	public static Vector3[] GetVector3Array(string key, Vector3 defaultValue, int defaultSize)
	{
		if (PlayerPrefs.HasKey(key))
		{
			return PlayerPrefsExtended.GetVector3Array(key);
		}
		Vector3[] array = new Vector3[defaultSize];
		for (int i = 0; i < defaultSize; i++)
		{
			array[i] = defaultValue;
		}
		return array;
	}

	// Token: 0x060000C1 RID: 193 RVA: 0x000054AC File Offset: 0x000036AC
	public static Quaternion[] GetQuaternionArray(string key)
	{
		List<Quaternion> list = new List<Quaternion>();
		PlayerPrefsExtended.GetValue<List<Quaternion>>(key, list, PlayerPrefsExtended.ArrayType.Quaternion, 4, new Action<List<Quaternion>, byte[]>(PlayerPrefsExtended.ConvertToQuaternion));
		return list.ToArray();
	}

	// Token: 0x060000C2 RID: 194 RVA: 0x000054DC File Offset: 0x000036DC
	public static Quaternion[] GetQuaternionArray(string key, Quaternion defaultValue, int defaultSize)
	{
		if (PlayerPrefs.HasKey(key))
		{
			return PlayerPrefsExtended.GetQuaternionArray(key);
		}
		Quaternion[] array = new Quaternion[defaultSize];
		for (int i = 0; i < defaultSize; i++)
		{
			array[i] = defaultValue;
		}
		return array;
	}

	// Token: 0x060000C3 RID: 195 RVA: 0x00005514 File Offset: 0x00003714
	public static Color[] GetColorArray(string key)
	{
		List<Color> list = new List<Color>();
		PlayerPrefsExtended.GetValue<List<Color>>(key, list, PlayerPrefsExtended.ArrayType.Color, 4, new Action<List<Color>, byte[]>(PlayerPrefsExtended.ConvertToColor));
		return list.ToArray();
	}

	// Token: 0x060000C4 RID: 196 RVA: 0x00005544 File Offset: 0x00003744
	public static Color[] GetColorArray(string key, Color defaultValue, int defaultSize)
	{
		if (PlayerPrefs.HasKey(key))
		{
			return PlayerPrefsExtended.GetColorArray(key);
		}
		Color[] array = new Color[defaultSize];
		for (int i = 0; i < defaultSize; i++)
		{
			array[i] = defaultValue;
		}
		return array;
	}

	// Token: 0x060000C5 RID: 197 RVA: 0x0000557C File Offset: 0x0000377C
	private static void GetValue<T>(string key, T list, PlayerPrefsExtended.ArrayType arrayType, int vectorNumber, Action<T, byte[]> convert) where T : IList
	{
		if (PlayerPrefs.HasKey(key))
		{
			byte[] array = Convert.FromBase64String(PlayerPrefs.GetString(key));
			if ((array.Length - 1) % (vectorNumber * 4) != 0)
			{
				Debug.LogError("Corrupt preference file for " + key);
				return;
			}
			if ((PlayerPrefsExtended.ArrayType)array[0] != arrayType)
			{
				Debug.LogError(key + " is not a " + arrayType.ToString() + " array");
				return;
			}
			PlayerPrefsExtended.Initialize();
			int num = (array.Length - 1) / (vectorNumber * 4);
			for (int i = 0; i < num; i++)
			{
				convert(list, array);
			}
		}
	}

	// Token: 0x060000C6 RID: 198 RVA: 0x00005606 File Offset: 0x00003806
	private static void ConvertToInt(List<int> list, byte[] bytes)
	{
		list.Add(PlayerPrefsExtended.ConvertBytesToInt32(bytes));
	}

	// Token: 0x060000C7 RID: 199 RVA: 0x00005614 File Offset: 0x00003814
	private static void ConvertToFloat(List<float> list, byte[] bytes)
	{
		list.Add(PlayerPrefsExtended.ConvertBytesToFloat(bytes));
	}

	// Token: 0x060000C8 RID: 200 RVA: 0x00005622 File Offset: 0x00003822
	private static void ConvertToVector2(List<Vector2> list, byte[] bytes)
	{
		list.Add(new Vector2(PlayerPrefsExtended.ConvertBytesToFloat(bytes), PlayerPrefsExtended.ConvertBytesToFloat(bytes)));
	}

	// Token: 0x060000C9 RID: 201 RVA: 0x0000563B File Offset: 0x0000383B
	private static void ConvertToVector3(List<Vector3> list, byte[] bytes)
	{
		list.Add(new Vector3(PlayerPrefsExtended.ConvertBytesToFloat(bytes), PlayerPrefsExtended.ConvertBytesToFloat(bytes), PlayerPrefsExtended.ConvertBytesToFloat(bytes)));
	}

	// Token: 0x060000CA RID: 202 RVA: 0x0000565A File Offset: 0x0000385A
	private static void ConvertToQuaternion(List<Quaternion> list, byte[] bytes)
	{
		list.Add(new Quaternion(PlayerPrefsExtended.ConvertBytesToFloat(bytes), PlayerPrefsExtended.ConvertBytesToFloat(bytes), PlayerPrefsExtended.ConvertBytesToFloat(bytes), PlayerPrefsExtended.ConvertBytesToFloat(bytes)));
	}

	// Token: 0x060000CB RID: 203 RVA: 0x0000567F File Offset: 0x0000387F
	private static void ConvertToColor(List<Color> list, byte[] bytes)
	{
		list.Add(new Color(PlayerPrefsExtended.ConvertBytesToFloat(bytes), PlayerPrefsExtended.ConvertBytesToFloat(bytes), PlayerPrefsExtended.ConvertBytesToFloat(bytes), PlayerPrefsExtended.ConvertBytesToFloat(bytes)));
	}

	// Token: 0x060000CC RID: 204 RVA: 0x000056A4 File Offset: 0x000038A4
	public static void ShowArrayType(string key)
	{
		byte[] array = Convert.FromBase64String(PlayerPrefs.GetString(key));
		if (array.Length != 0)
		{
			PlayerPrefsExtended.ArrayType arrayType = (PlayerPrefsExtended.ArrayType)array[0];
			Debug.Log(key + " is a " + arrayType.ToString() + " array");
		}
	}

	// Token: 0x060000CD RID: 205 RVA: 0x000056E7 File Offset: 0x000038E7
	private static void Initialize()
	{
		if (BitConverter.IsLittleEndian)
		{
			PlayerPrefsExtended.endianDiff1 = 0;
			PlayerPrefsExtended.endianDiff2 = 0;
		}
		else
		{
			PlayerPrefsExtended.endianDiff1 = 3;
			PlayerPrefsExtended.endianDiff2 = 1;
		}
		if (PlayerPrefsExtended.byteBlock == null)
		{
			PlayerPrefsExtended.byteBlock = new byte[4];
		}
		PlayerPrefsExtended.idx = 1;
	}

	// Token: 0x060000CE RID: 206 RVA: 0x00005724 File Offset: 0x00003924
	private static bool SaveBytes(string key, byte[] bytes)
	{
		try
		{
			PlayerPrefs.SetString(key, Convert.ToBase64String(bytes));
		}
		catch
		{
			return false;
		}
		return true;
	}

	// Token: 0x060000CF RID: 207 RVA: 0x00005758 File Offset: 0x00003958
	private static void ConvertFloatToBytes(float f, byte[] bytes)
	{
		PlayerPrefsExtended.byteBlock = BitConverter.GetBytes(f);
		PlayerPrefsExtended.ConvertTo4Bytes(bytes);
	}

	// Token: 0x060000D0 RID: 208 RVA: 0x0000576B File Offset: 0x0000396B
	private static float ConvertBytesToFloat(byte[] bytes)
	{
		PlayerPrefsExtended.ConvertFrom4Bytes(bytes);
		return BitConverter.ToSingle(PlayerPrefsExtended.byteBlock, 0);
	}

	// Token: 0x060000D1 RID: 209 RVA: 0x0000577E File Offset: 0x0000397E
	private static void ConvertInt32ToBytes(int i, byte[] bytes)
	{
		PlayerPrefsExtended.byteBlock = BitConverter.GetBytes(i);
		PlayerPrefsExtended.ConvertTo4Bytes(bytes);
	}

	// Token: 0x060000D2 RID: 210 RVA: 0x00005791 File Offset: 0x00003991
	private static int ConvertBytesToInt32(byte[] bytes)
	{
		PlayerPrefsExtended.ConvertFrom4Bytes(bytes);
		return BitConverter.ToInt32(PlayerPrefsExtended.byteBlock, 0);
	}

	// Token: 0x060000D3 RID: 211 RVA: 0x000057A4 File Offset: 0x000039A4
	private static void ConvertTo4Bytes(byte[] bytes)
	{
		bytes[PlayerPrefsExtended.idx] = PlayerPrefsExtended.byteBlock[PlayerPrefsExtended.endianDiff1];
		bytes[PlayerPrefsExtended.idx + 1] = PlayerPrefsExtended.byteBlock[1 + PlayerPrefsExtended.endianDiff2];
		bytes[PlayerPrefsExtended.idx + 2] = PlayerPrefsExtended.byteBlock[2 - PlayerPrefsExtended.endianDiff2];
		bytes[PlayerPrefsExtended.idx + 3] = PlayerPrefsExtended.byteBlock[3 - PlayerPrefsExtended.endianDiff1];
		PlayerPrefsExtended.idx += 4;
	}

	// Token: 0x060000D4 RID: 212 RVA: 0x00005814 File Offset: 0x00003A14
	private static void ConvertFrom4Bytes(byte[] bytes)
	{
		PlayerPrefsExtended.byteBlock[PlayerPrefsExtended.endianDiff1] = bytes[PlayerPrefsExtended.idx];
		PlayerPrefsExtended.byteBlock[1 + PlayerPrefsExtended.endianDiff2] = bytes[PlayerPrefsExtended.idx + 1];
		PlayerPrefsExtended.byteBlock[2 - PlayerPrefsExtended.endianDiff2] = bytes[PlayerPrefsExtended.idx + 2];
		PlayerPrefsExtended.byteBlock[3 - PlayerPrefsExtended.endianDiff1] = bytes[PlayerPrefsExtended.idx + 3];
		PlayerPrefsExtended.idx += 4;
	}

	// Token: 0x04000063 RID: 99
	private static int endianDiff1;

	// Token: 0x04000064 RID: 100
	private static int endianDiff2;

	// Token: 0x04000065 RID: 101
	private static int idx;

	// Token: 0x04000066 RID: 102
	private static byte[] byteBlock;

	// Token: 0x0200046F RID: 1135
	private enum ArrayType
	{
		// Token: 0x04001B67 RID: 7015
		Float,
		// Token: 0x04001B68 RID: 7016
		Int32,
		// Token: 0x04001B69 RID: 7017
		Bool,
		// Token: 0x04001B6A RID: 7018
		String,
		// Token: 0x04001B6B RID: 7019
		Vector2,
		// Token: 0x04001B6C RID: 7020
		Vector3,
		// Token: 0x04001B6D RID: 7021
		Quaternion,
		// Token: 0x04001B6E RID: 7022
		Color
	}
}
﻿using System;
using TMPro;
using UnityEngine;

// Token: 0x020000FA RID: 250
public class PlayerStatisticsView : MonoBehaviour
{
	// Token: 0x06000639 RID: 1593 RVA: 0x0001D4E4 File Offset: 0x0001B6E4
	public void SetView(AccountModel account)
	{
		if (account == null)
		{
			return;
		}
		this._playerNameText.text = account.Name;
		this._playerKillsText.text = account.Statistics.Kills.ToString();
		this._playerAssistsText.text = ((float)account.Statistics.Kills / (float)account.Statistics.Deaths).ToString("F2");
		this._playerDeathsText.text = account.Statistics.Deaths.ToString();
		base.gameObject.SetActive(true);
	}

	// Token: 0x04000566 RID: 1382
	[SerializeField]
	private TextMeshProUGUI _playerNameText;

	// Token: 0x04000567 RID: 1383
	[SerializeField]
	private TextMeshProUGUI _playerKillsText;

	// Token: 0x04000568 RID: 1384
	[SerializeField]
	private TextMeshProUGUI _playerAssistsText;

	// Token: 0x04000569 RID: 1385
	[SerializeField]
	private TextMeshProUGUI _playerDeathsText;
}
﻿using System;
using MarsFPSKit;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000EB RID: 235
public class PlayerTeamView : MonoBehaviour
{
	// Token: 0x060005F1 RID: 1521 RVA: 0x0001B3D3 File Offset: 0x000195D3
	private void OnEnable()
	{
		this._playerTeamViewButton.onClick.AddListener(delegate()
		{
			this._allTeams.SelectTeam(this, this._team);
		});
	}

	// Token: 0x060005F2 RID: 1522 RVA: 0x0001B3F1 File Offset: 0x000195F1
	private void OnDisable()
	{
		this._playerTeamViewButton.onClick.RemoveListener(delegate()
		{
			this._allTeams.SelectTeam(this, this._team);
		});
	}

	// Token: 0x060005F3 RID: 1523 RVA: 0x0001B40F File Offset: 0x0001960F
	public void ViewInit(PlayerAllTeams allTeams, Kit_Team team)
	{
		this._allTeams = allTeams;
		this._team = team;
		this._teamNameText.text = team.teamName;
	}

	// Token: 0x060005F4 RID: 1524 RVA: 0x0001B430 File Offset: 0x00019630
	public void Select()
	{
		this._teamNameText.color = Color.black;
		this._background.color = Color.white;
	}

	// Token: 0x060005F5 RID: 1525 RVA: 0x0001B452 File Offset: 0x00019652
	public void Deselect()
	{
		this._teamNameText.color = Color.white;
		this._background.color = Color.black;
	}

	// Token: 0x04000522 RID: 1314
	[SerializeField]
	private TextMeshProUGUI _teamNameText;

	// Token: 0x04000523 RID: 1315
	[SerializeField]
	private Button _playerTeamViewButton;

	// Token: 0x04000524 RID: 1316
	[SerializeField]
	private Image _background;

	// Token: 0x04000525 RID: 1317
	private PlayerAllTeams _allTeams;

	// Token: 0x04000526 RID: 1318
	private Kit_Team _team;
}
﻿using System;
using UnityEngine;

// Token: 0x02000108 RID: 264
public class PulseEffectView : MonoBehaviour
{
	// Token: 0x060006B4 RID: 1716 RVA: 0x0001F9AF File Offset: 0x0001DBAF
	private void Update()
	{
		this.Play();
	}

	// Token: 0x060006B5 RID: 1717 RVA: 0x0001F9B8 File Offset: 0x0001DBB8
	private void Play()
	{
		this._rectTransform.localScale = Vector2.Lerp(this._minScale, this._maxScale, this._curve.Evaluate(this._time));
		this._time += this._speed * Time.deltaTime;
	}

	// Token: 0x040005F5 RID: 1525
	[SerializeField]
	private RectTransform _rectTransform;

	// Token: 0x040005F6 RID: 1526
	[SerializeField]
	private float _speed;

	// Token: 0x040005F7 RID: 1527
	[SerializeField]
	private Vector2 _maxScale;

	// Token: 0x040005F8 RID: 1528
	[SerializeField]
	private Vector2 _minScale;

	// Token: 0x040005F9 RID: 1529
	[SerializeField]
	private AnimationCurve _curve;

	// Token: 0x040005FA RID: 1530
	private float _time;
}
﻿using System;
using System.Collections.Generic;
using System.Linq;
using ExitGames.Client.Photon;
using MarsFPSKit;
using MarsFPSKit.Spectating;
using Photon.Pun;
using Photon.Realtime;
using UnityEngine;

// Token: 0x020000B8 RID: 184
[CreateAssetMenu(menuName = "MarsFPSKit/Gamemodes/Planting Bomb Logic")]
public class PvPGMBPlantingBomb : Kit_PvP_GameModeBase, IChangableSpawn, IRounds
{
	// Token: 0x1400000A RID: 10
	// (add) Token: 0x0600045B RID: 1115 RVA: 0x00013C80 File Offset: 0x00011E80
	// (remove) Token: 0x0600045C RID: 1116 RVA: 0x00013CB8 File Offset: 0x00011EB8
	public event Action OnRoundComplete = delegate()
	{
	};

	// Token: 0x17000054 RID: 84
	// (get) Token: 0x0600045D RID: 1117 RVA: 0x00013CED File Offset: 0x00011EED
	public bool IsEndingRound
	{
		get
		{
			return this._isEndingRound;
		}
	}

	// Token: 0x17000055 RID: 85
	// (get) Token: 0x0600045E RID: 1118 RVA: 0x00013CF5 File Offset: 0x00011EF5
	public Kit_IngameMain Main
	{
		get
		{
			return this._main;
		}
	}

	// Token: 0x17000056 RID: 86
	// (get) Token: 0x0600045F RID: 1119 RVA: 0x00013D00 File Offset: 0x00011F00
	private int playersInTeamOne
	{
		get
		{
			int num = 0;
			for (int i = 0; i < PhotonNetwork.PlayerList.Length; i++)
			{
				if (PhotonNetwork.PlayerList[i].CustomProperties["team"] != null && (int)PhotonNetwork.PlayerList[i].CustomProperties["team"] == 0)
				{
					num++;
				}
			}
			return num;
		}
	}

	// Token: 0x17000057 RID: 87
	// (get) Token: 0x06000460 RID: 1120 RVA: 0x00013D5C File Offset: 0x00011F5C
	private int playersInTeamTwo
	{
		get
		{
			int num = 0;
			for (int i = 0; i < PhotonNetwork.PlayerList.Length; i++)
			{
				if (PhotonNetwork.PlayerList[i].CustomProperties["team"] != null && (int)PhotonNetwork.PlayerList[i].CustomProperties["team"] == 1)
				{
					num++;
				}
			}
			return num;
		}
	}

	// Token: 0x17000058 RID: 88
	// (get) Token: 0x06000461 RID: 1121 RVA: 0x00013DB8 File Offset: 0x00011FB8
	public int TerroristTeam
	{
		get
		{
			return this._terroristTeam;
		}
	}

	// Token: 0x06000462 RID: 1122 RVA: 0x00013DC0 File Offset: 0x00011FC0
	public void OnDeactivateBomb()
	{
		if (!this._isEndingRound)
		{
			this._bombView.IsBombDeactivated = true;
			this._bombView.SetPassive();
			if (PhotonNetwork.IsMasterClient)
			{
				PlantingBombRuntimeData plantingBombRuntimeData = this._main.currentGameModeRuntimeData as PlantingBombRuntimeData;
				plantingBombRuntimeData.teamPoints[this._defenderTeam]++;
				plantingBombRuntimeData.teamCommonPoints[this._defenderTeam]++;
				this.CompleteProcessor(this._main, null, true);
			}
		}
	}

	// Token: 0x06000463 RID: 1123 RVA: 0x00013E3C File Offset: 0x0001203C
	public void OnBombSetup()
	{
		if (this._main.gameModeStage < 2)
		{
			this._main.timer = this.BombTimer;
			this._isBombSetuped = true;
		}
	}

	// Token: 0x06000464 RID: 1124 RVA: 0x00013E64 File Offset: 0x00012064
	public void GetBobm(Kit_PlayerBehaviour who)
	{
		this._bombView.GetBomb(who);
	}

	// Token: 0x06000465 RID: 1125 RVA: 0x00013E72 File Offset: 0x00012072
	public void DropBomb(Vector3 position)
	{
		if (!this._bombView.IsActive)
		{
			this._bombView.DropBomb(position);
		}
	}

	// Token: 0x06000466 RID: 1126 RVA: 0x00013E8D File Offset: 0x0001208D
	public void SetupBomb(Vector3 place)
	{
		if (!this._isEndingRound)
		{
			this._bombSpawner.SetupBomb(place);
		}
	}

	// Token: 0x06000467 RID: 1127 RVA: 0x00013EA3 File Offset: 0x000120A3
	public bool IsBombIconVisible()
	{
		return PhotonNetwork.LocalPlayer.CustomProperties["team"] != null && (int)PhotonNetwork.LocalPlayer.CustomProperties["team"] == this._terroristTeam;
	}

	// Token: 0x06000468 RID: 1128 RVA: 0x00013EDE File Offset: 0x000120DE
	public void RespawnBomb()
	{
		this._bombSpawner.ResetPosition();
		this._bombSpawner.SetTeam(this._terroristTeam);
		this._isBombSetuped = false;
	}

	// Token: 0x06000469 RID: 1129 RVA: 0x00013F03 File Offset: 0x00012103
	public void ExploseBomb()
	{
		this._bombView.BombExplosion();
	}

	// Token: 0x0600046A RID: 1130 RVA: 0x00013F10 File Offset: 0x00012110
	public Hashtable GetSyncBombData()
	{
		return new Hashtable
		{
			{
				"IsActive",
				this._bombView.IsActive
			},
			{
				"Position",
				this._bombView.transform.position
			},
			{
				"Team",
				this._bombView.Team
			},
			{
				"IsBombSetup",
				this._bombView.IsBombSetup
			}
		};
	}

	// Token: 0x0600046B RID: 1131 RVA: 0x00013F94 File Offset: 0x00012194
	public void SetSyncBombData(Hashtable bombData)
	{
		this._bombView.transform.position = (Vector3)bombData["Position"];
		if (!(bool)bombData["IsActive"])
		{
			this._bombView.SetPassive();
		}
		this._bombView.SetTeam((int)bombData["Team"]);
		this._bombView.IsBombSetup = (bool)bombData["IsBombSetup"];
	}

	// Token: 0x0600046C RID: 1132 RVA: 0x00014014 File Offset: 0x00012214
	public void RoundComplete()
	{
		this.OnRoundComplete();
	}

	// Token: 0x0600046D RID: 1133 RVA: 0x00014024 File Offset: 0x00012224
	private bool CheckRoundWiner(Kit_IngameMain main, Kit_PlayerBehaviour pb)
	{
		if (this._isEndingRound)
		{
			return false;
		}
		if (this.IsTeamDead(main, this._defenderTeam, pb))
		{
			PlantingBombRuntimeData plantingBombRuntimeData = main.currentGameModeRuntimeData as PlantingBombRuntimeData;
			plantingBombRuntimeData.teamPoints[this._terroristTeam]++;
			plantingBombRuntimeData.teamCommonPoints[this._terroristTeam]++;
			return true;
		}
		if (this.IsTeamDead(main, this._terroristTeam, pb) && !this._isBombSetuped)
		{
			PlantingBombRuntimeData plantingBombRuntimeData2 = main.currentGameModeRuntimeData as PlantingBombRuntimeData;
			plantingBombRuntimeData2.teamPoints[this._defenderTeam]++;
			plantingBombRuntimeData2.teamCommonPoints[this._defenderTeam]++;
			return true;
		}
		return this._bombView.IsBombDeactivated;
	}

	// Token: 0x0600046E RID: 1134 RVA: 0x000140E4 File Offset: 0x000122E4
	private bool IsTeamDead(Kit_IngameMain main, int team, Kit_PlayerBehaviour pb)
	{
		Kit_PlayerBehaviour[] array = UnityEngine.Object.FindObjectsOfType<Kit_PlayerBehaviour>();
		for (int i = 0; i < array.Length; i++)
		{
			if (array[i].myTeam == team && array[i] != pb)
			{
				return false;
			}
		}
		return true;
	}

	// Token: 0x0600046F RID: 1135 RVA: 0x00014120 File Offset: 0x00012320
	private bool CheckSuperRoundWiner(Kit_IngameMain main)
	{
		PlantingBombRuntimeData plantingBombRuntimeData = main.currentGameModeRuntimeData as PlantingBombRuntimeData;
		for (int i = 0; i < plantingBombRuntimeData.teamPoints.Length; i++)
		{
			if (plantingBombRuntimeData.teamPoints[i] >= this.roundsCount[this._currentRoundCount])
			{
				plantingBombRuntimeData.teamSuperPoints[i]++;
				this.ClearTeamPoints(plantingBombRuntimeData);
				return true;
			}
		}
		return false;
	}

	// Token: 0x06000470 RID: 1136 RVA: 0x000122EE File Offset: 0x000104EE
	private void Respawn(Kit_IngameMain main)
	{
		PhotonNetwork.RaiseEvent(Kit_EventIDs.resetRoundEvent, null, new RaiseEventOptions
		{
			Receivers = ReceiverGroup.All
		}, SendOptions.SendReliable);
	}

	// Token: 0x06000471 RID: 1137 RVA: 0x00014180 File Offset: 0x00012380
	private bool CheckFinalWiner(Kit_IngameMain main)
	{
		if (main.gameModeStage < 2)
		{
			if (main.currentGameModeRuntimeData == null || main.currentGameModeRuntimeData.GetType() != typeof(PlantingBombRuntimeData))
			{
				main.currentGameModeRuntimeData = new PlantingBombRuntimeData
				{
					teamPoints = new int[Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, this.maximumAmountOfTeams)],
					teamSuperPoints = new int[Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, this.maximumAmountOfTeams)]
				};
			}
			PlantingBombRuntimeData plantingBombRuntimeData = main.currentGameModeRuntimeData as PlantingBombRuntimeData;
			for (int i = 0; i < plantingBombRuntimeData.teamPoints.Length; i++)
			{
				if (plantingBombRuntimeData.teamSuperPoints[i] == 2 || this.IsTeamAlone(i))
				{
					main.EndGame(i, plantingBombRuntimeData.teamPoints);
					main.timer = this.endGameTime;
					main.gameModeStage = 2;
					return true;
				}
			}
		}
		return false;
	}

	// Token: 0x06000472 RID: 1138 RVA: 0x00014268 File Offset: 0x00012468
	private Kit_PlayerBehaviour FindPBByID(int id, bool isBot)
	{
		foreach (Kit_PlayerBehaviour kit_PlayerBehaviour in UnityEngine.Object.FindObjectsOfType<Kit_PlayerBehaviour>())
		{
			if (kit_PlayerBehaviour.id == id && kit_PlayerBehaviour.isBot == isBot)
			{
				return kit_PlayerBehaviour;
			}
		}
		return null;
	}

	// Token: 0x06000473 RID: 1139 RVA: 0x000142A4 File Offset: 0x000124A4
	private void ClearTeamPoints(PlantingBombRuntimeData runtimeData)
	{
		for (int i = 0; i < runtimeData.teamPoints.Length; i++)
		{
			runtimeData.teamPoints[i] = 0;
		}
	}

	// Token: 0x06000474 RID: 1140 RVA: 0x000142CD File Offset: 0x000124CD
	private bool IsTeamAlone(int team)
	{
		if (!TournamentsController.Instance.IsItTournament)
		{
			return false;
		}
		if (team == 0)
		{
			if (this.playersInTeamTwo <= 0)
			{
				return true;
			}
		}
		else if (team == 1 && this.playersInTeamOne <= 0)
		{
			return true;
		}
		return false;
	}

	// Token: 0x06000475 RID: 1141 RVA: 0x000142FC File Offset: 0x000124FC
	private void CompleteProcessor(Kit_IngameMain main, Kit_PlayerBehaviour pb, bool timeOut = false)
	{
		bool flag = timeOut || this.CheckRoundWiner(main, pb);
		bool flag2 = this.CheckSuperRoundWiner(main);
		bool flag3 = this.CheckFinalWiner(main);
		if (flag)
		{
			if (flag2)
			{
				if (!flag3)
				{
					Debug.Log("CallChangeSides");
					PhotonNetwork.RaiseEvent(Kit_EventIDs.changeTeamSpawnEvent, null, new RaiseEventOptions
					{
						Receivers = ReceiverGroup.All
					}, SendOptions.SendReliable);
				}
			}
			else
			{
				PhotonNetwork.RaiseEvent(Kit_EventIDs.roundCompleteEvent, null, new RaiseEventOptions
				{
					Receivers = ReceiverGroup.All
				}, SendOptions.SendReliable);
			}
			if (!flag3)
			{
				this._isEndingRound = true;
				this._waitProcessBeforeRespawn.StartProcess(delegate
				{
					this.Respawn(main);
					this._isEndingRound = false;
				}, this._waitTime);
			}
		}
	}

	// Token: 0x06000476 RID: 1142 RVA: 0x000143C0 File Offset: 0x000125C0
	public override bool AreEnoughPlayersThere(Kit_IngameMain main)
	{
		if (main && main.currentBotManager && main.currentBotManager.bots.Count > 0)
		{
			return true;
		}
		if (PhotonNetwork.CurrentRoom != null && PhotonNetwork.CurrentRoom.CustomProperties["lobby"] != null && (bool)PhotonNetwork.CurrentRoom.CustomProperties["lobby"])
		{
			return PhotonNetwork.PlayerList.Length >= main.currentGameModeBehaviour.lobbyMinimumPlayersNeeded;
		}
		return PhotonNetwork.CurrentRoom != null && PhotonNetwork.PlayerList.Length >= main.currentGameModeBehaviour.traditionalPlayerNeeded[(int)PhotonNetwork.CurrentRoom.CustomProperties["playerNeeded"]];
	}

	// Token: 0x06000477 RID: 1143 RVA: 0x00012525 File Offset: 0x00010725
	public override bool ArePlayersEnemies(Kit_PlayerBehaviour playerOne, Kit_PlayerBehaviour playerTwo)
	{
		return playerOne.myTeam != playerTwo.myTeam;
	}

	// Token: 0x06000478 RID: 1144 RVA: 0x00014484 File Offset: 0x00012684
	public override bool ArePlayersEnemies(Kit_IngameMain main, int playerOneID, bool playerOneBot, int playerTwoID, bool playerTwoBot, bool canKillSelf = false)
	{
		if (playerTwoBot && playerOneBot && playerOneID == playerTwoID && canKillSelf)
		{
			return true;
		}
		int num = -1;
		int num2 = -2;
		if (playerOneBot)
		{
			Kit_Bot botWithID = main.currentBotManager.GetBotWithID(playerOneID);
			if (botWithID != null)
			{
				num = botWithID.team;
			}
		}
		else
		{
			Player player = Kit_PhotonPlayerExtensions.Find(playerOneID);
			if (player != null)
			{
				num = (int)player.CustomProperties["team"];
			}
		}
		if (playerTwoBot)
		{
			Kit_Bot botWithID2 = main.currentBotManager.GetBotWithID(playerTwoID);
			if (botWithID2 != null)
			{
				num2 = botWithID2.team;
			}
		}
		else
		{
			Player player2 = Kit_PhotonPlayerExtensions.Find(playerTwoID);
			if (player2 != null)
			{
				num2 = (int)player2.CustomProperties["team"];
			}
		}
		return num != num2;
	}

	// Token: 0x06000479 RID: 1145 RVA: 0x00014534 File Offset: 0x00012734
	public override bool ArePlayersEnemies(Kit_IngameMain main, int playerOneID, bool playerOneBot, Kit_PlayerBehaviour playerTwo, bool canKillSelf)
	{
		if (playerTwo.isBot && playerOneBot && playerOneID == playerTwo.botId && canKillSelf)
		{
			return true;
		}
		int num = -1;
		if (playerOneBot)
		{
			Kit_Bot botWithID = main.currentBotManager.GetBotWithID(playerOneID);
			if (botWithID != null)
			{
				num = botWithID.team;
			}
		}
		else
		{
			Player player = Kit_PhotonPlayerExtensions.Find(playerOneID);
			if (player != null)
			{
				num = (int)player.CustomProperties["team"];
			}
		}
		return num != playerTwo.myTeam;
	}

	// Token: 0x0600047A RID: 1146 RVA: 0x000145AC File Offset: 0x000127AC
	public override bool AreWeEnemies(Kit_IngameMain main, bool botEnemy, int enemyId)
	{
		if (!botEnemy && enemyId == PhotonNetwork.LocalPlayer.ActorNumber)
		{
			return true;
		}
		int num;
		if (botEnemy)
		{
			Kit_Bot botWithID = main.currentBotManager.GetBotWithID(enemyId);
			if (botWithID == null)
			{
				return false;
			}
			num = botWithID.team;
		}
		else
		{
			num = (int)Kit_PhotonPlayerExtensions.Find(enemyId).CustomProperties["team"];
		}
		return main.assignedTeamID != num;
	}

	// Token: 0x0600047B RID: 1147 RVA: 0x000126A6 File Offset: 0x000108A6
	public override bool CanControlPlayer(Kit_IngameMain main)
	{
		return (!this.AreEnoughPlayersThere(main) && !main.hasGameModeStarted) || main.gameModeStage == 1;
	}

	// Token: 0x0600047C RID: 1148 RVA: 0x00014614 File Offset: 0x00012814
	public override bool CanJoinTeam(Kit_IngameMain main, Player player, int team)
	{
		int maxPlayers = (int)PhotonNetwork.CurrentRoom.MaxPlayers;
		if (team == 0 && this.playersInTeamOne >= maxPlayers / 2)
		{
			return false;
		}
		if (team == 1 && this.playersInTeamTwo >= maxPlayers / 2)
		{
			return false;
		}
		if (team == 0)
		{
			if (this.playersInTeamOne - this.playersInTeamTwo > this.maxTeamDifference)
			{
				return false;
			}
		}
		else if (team == 1 && this.playersInTeamTwo - this.playersInTeamOne > this.maxTeamDifference)
		{
			return false;
		}
		return true;
	}

	// Token: 0x0600047D RID: 1149 RVA: 0x00014684 File Offset: 0x00012884
	public override bool CanSpawn(Kit_IngameMain main, Player player)
	{
		if (main.gameModeStage < 2 && player.CustomProperties["team"] != null && player.CustomProperties["team"].GetType() == typeof(int))
		{
			int num = (int)player.CustomProperties["team"];
			if (num >= 0 && num < main.gameInformation.allPvpTeams.Length)
			{
				return true;
			}
		}
		return false;
	}

	// Token: 0x0600047E RID: 1150 RVA: 0x000146FF File Offset: 0x000128FF
	public override bool CanStartVote(Kit_IngameMain main)
	{
		return (!this.AreEnoughPlayersThere(main) && !main.hasGameModeStarted) || (main.gameModeStage == 1 && main.timer > this.votingThreshold);
	}

	// Token: 0x0600047F RID: 1151 RVA: 0x00014730 File Offset: 0x00012930
	public override void GameModeBeginMiddle(Kit_IngameMain main)
	{
		PhotonNetwork.RaiseEvent(3, null, new RaiseEventOptions
		{
			Receivers = ReceiverGroup.All
		}, SendOptions.SendReliable);
		if (main.currentGameModeRuntimeData != null && main.currentGameModeRuntimeData.GetType() == typeof(PlantingBombRuntimeData))
		{
			PlantingBombRuntimeData plantingBombRuntimeData = main.currentGameModeRuntimeData as PlantingBombRuntimeData;
			for (int i = 0; i < plantingBombRuntimeData.teamPoints.Length; i++)
			{
				plantingBombRuntimeData.teamPoints[i] = 0;
			}
		}
		Kit_WeaponSpawner[] array = UnityEngine.Object.FindObjectsOfType<Kit_WeaponSpawner>();
		for (int j = 0; j < array.Length; j++)
		{
			array[j].GameModeBeginMiddle();
		}
		Kit_AmmoSpawner[] array2 = UnityEngine.Object.FindObjectsOfType<Kit_AmmoSpawner>();
		for (int k = 0; k < array2.Length; k++)
		{
			array2[k].GameModeBeginMiddle();
		}
	}

	// Token: 0x06000480 RID: 1152 RVA: 0x000147E4 File Offset: 0x000129E4
	public override void GamemodeSetup(Kit_IngameMain main)
	{
		Kit_PlayerSpawn[] array = UnityEngine.Object.FindObjectsOfType<Kit_PlayerSpawn>();
		if (array.Length == 0)
		{
			throw new Exception("This scene has no spawns.");
		}
		List<Kit_PlayerSpawn> list = new List<Kit_PlayerSpawn>();
		int num = 0;
		for (int i = 0; i < array.Length; i++)
		{
			int num2 = i;
			if (array[num2].gameModes.Contains(this))
			{
				list.Add(array[num2]);
				if (array[num2].spawnGroupID > num)
				{
					num = array[num2].spawnGroupID;
				}
			}
		}
		main.internalSpawns = new List<InternalSpawns>();
		for (int j = 0; j < num + 1; j++)
		{
			main.internalSpawns.Add(null);
		}
		for (int k = 0; k < main.internalSpawns.Count; k++)
		{
			int num3 = k;
			main.internalSpawns[num3] = new InternalSpawns();
			main.internalSpawns[num3].spawns = new List<Kit_PlayerSpawn>();
			for (int l = 0; l < list.Count; l++)
			{
				int index = l;
				if (list[index].spawnGroupID == num3)
				{
					main.internalSpawns[num3].spawns.Add(list[index]);
				}
			}
		}
		main.gameModeStage = 0;
		main.timer = this.preGameTime;
		main.currentGameModeRuntimeData = new PlantingBombRuntimeData
		{
			teamPoints = new int[Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, this.maximumAmountOfTeams)],
			teamSuperPoints = new int[Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, this.maximumAmountOfTeams)],
			teamCommonPoints = new int[Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, this.maximumAmountOfTeams)]
		};
		main.SetupAutoSpawnAndBotManager(false);
		this._waitProcessBeforeRespawn = new WaitProcess();
		this._waitProcessinRespawn = new WaitProcess();
		this._layers = new int[]
		{
			1,
			2
		};
		this._bombSpawner = UnityEngine.Object.FindObjectOfType<BombSpawner>();
		this._bombView = this._bombSpawner.SpawnBomb(this);
		this._bombSpawner.SpawnBombPlace(this);
		this._defenderTeam = 0;
		this._terroristTeam = 1;
		this._bombSpawner.SetTeam(this._terroristTeam);
		this._main = main;
		this._isBombSetuped = false;
		this._isEndingRound = false;
		this._currentRoundCount = (int)PhotonNetwork.CurrentRoom.CustomProperties["roundCount"];
		this._streamReadTimer = 0f;
	}

	// Token: 0x06000481 RID: 1153 RVA: 0x00005F78 File Offset: 0x00004178
	public override void GameModeUpdate(Kit_IngameMain main)
	{
	}

	// Token: 0x06000482 RID: 1154 RVA: 0x00014A58 File Offset: 0x00012C58
	public override Transform GetSpawn(Kit_IngameMain main, Player player)
	{
		int num = 0;
		Transform transform = null;
		while (!transform && num < 10)
		{
			int num2;
			if (main.gameModeStage == 0)
			{
				num2 = this.teamsInitialSpawnLayer[Mathf.Clamp(main.assignedTeamID, 0, this.teamsInitialSpawnLayer.Length - 1)];
			}
			else
			{
				num2 = this.teamsGameplaySpawnLayer[Mathf.Clamp(main.assignedTeamID, 0, this.teamsGameplaySpawnLayer.Length - 1)];
			}
			num2 = this._layers[num2 - 1];
			Transform transform2 = main.internalSpawns[num2].spawns[UnityEngine.Random.Range(0, main.internalSpawns[num2].spawns.Count)].transform;
			if (transform2 && this.spawnSystemToUse.CheckSpawnPosition(main, transform2, player))
			{
				transform = transform2;
				break;
			}
			num++;
		}
		if (!transform)
		{
			int num3;
			if (main.gameModeStage == 0)
			{
				num3 = this.teamsInitialSpawnLayer[Mathf.Clamp(main.assignedTeamID, 0, this.teamsInitialSpawnLayer.Length - 1)];
			}
			else
			{
				num3 = this.teamsGameplaySpawnLayer[Mathf.Clamp(main.assignedTeamID, 0, this.teamsGameplaySpawnLayer.Length - 1)];
			}
			num3 = this._layers[num3 - 1];
			return main.internalSpawns[num3].spawns[UnityEngine.Random.Range(0, main.internalSpawns[num3].spawns.Count)].transform;
		}
		return transform;
	}

	// Token: 0x06000483 RID: 1155 RVA: 0x00014BCC File Offset: 0x00012DCC
	public override Transform GetSpawn(Kit_IngameMain main, Kit_Bot bot)
	{
		int num = 0;
		Transform transform = null;
		while (!transform && num < 10)
		{
			int num2;
			if (main.gameModeStage == 0)
			{
				num2 = this.teamsInitialSpawnLayer[Mathf.Clamp(bot.team, 0, this.teamsInitialSpawnLayer.Length - 1)];
			}
			else
			{
				num2 = this.teamsGameplaySpawnLayer[Mathf.Clamp(bot.team, 0, this.teamsGameplaySpawnLayer.Length - 1)];
			}
			num2 = this._layers[num2 - 1];
			Transform transform2 = main.internalSpawns[num2].spawns[UnityEngine.Random.Range(0, main.internalSpawns[num2].spawns.Count)].transform;
			if (transform2 && this.spawnSystemToUse.CheckSpawnPosition(main, transform2, bot))
			{
				transform = transform2;
				break;
			}
			num++;
		}
		if (transform == null)
		{
			int num3;
			if (main.gameModeStage == 0)
			{
				num3 = this.teamsInitialSpawnLayer[Mathf.Clamp(bot.team, 0, this.teamsInitialSpawnLayer.Length - 1)];
			}
			else
			{
				num3 = this.teamsGameplaySpawnLayer[Mathf.Clamp(bot.team, 0, this.teamsGameplaySpawnLayer.Length - 1)];
			}
			num3 = this._layers[num3 - 1];
			return main.internalSpawns[num3].spawns[UnityEngine.Random.Range(0, main.internalSpawns[num3].spawns.Count)].transform;
		}
		return transform;
	}

	// Token: 0x06000484 RID: 1156 RVA: 0x00012D36 File Offset: 0x00010F36
	public override Spectateable GetSpectateable(Kit_IngameMain main)
	{
		if (main.assignedTeamID >= 0)
		{
			return Spectateable.Friendlies;
		}
		return Spectateable.All;
	}

	// Token: 0x06000485 RID: 1157 RVA: 0x00014D40 File Offset: 0x00012F40
	public override void OnPhotonSerializeView(Kit_IngameMain main, PhotonStream stream, PhotonMessageInfo info)
	{
		if (main.currentGameModeRuntimeData == null || main.currentGameModeRuntimeData.GetType() != typeof(PlantingBombRuntimeData))
		{
			main.currentGameModeRuntimeData = new PlantingBombRuntimeData
			{
				teamPoints = new int[Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, main.currentGameModeBehaviour.maximumAmountOfTeams)]
			};
		}
		PlantingBombRuntimeData plantingBombRuntimeData = main.currentGameModeRuntimeData as PlantingBombRuntimeData;
		if (this._streamReadTimer < 0.1f)
		{
			this._streamReadTimer += Time.deltaTime;
		}
		if (stream.IsWriting)
		{
			for (int i = 0; i < plantingBombRuntimeData.teamPoints.Length; i++)
			{
				stream.SendNext(plantingBombRuntimeData.teamPoints[i]);
				stream.SendNext(plantingBombRuntimeData.teamSuperPoints[i]);
				stream.SendNext(plantingBombRuntimeData.teamCommonPoints[i]);
			}
			stream.SendNext(this._isBombSetuped);
			stream.SendNext(this._defenderTeam);
			stream.SendNext(this._terroristTeam);
			for (int j = 0; j < this._layers.Length; j++)
			{
				stream.SendNext(this._layers[j]);
			}
			stream.SendNext(this._isEndingRound);
			return;
		}
		if (this._streamReadTimer >= 0.1f)
		{
			for (int k = 0; k < plantingBombRuntimeData.teamPoints.Length; k++)
			{
				plantingBombRuntimeData.teamPoints[k] = (int)stream.ReceiveNext();
				plantingBombRuntimeData.teamSuperPoints[k] = (int)stream.ReceiveNext();
				plantingBombRuntimeData.teamCommonPoints[k] = (int)stream.ReceiveNext();
			}
			this._isBombSetuped = (bool)stream.ReceiveNext();
			this._defenderTeam = (int)stream.ReceiveNext();
			this._terroristTeam = (int)stream.ReceiveNext();
			for (int l = 0; l < this._layers.Length; l++)
			{
				this._layers[l] = (int)stream.ReceiveNext();
			}
			this._isEndingRound = (bool)stream.ReceiveNext();
		}
	}

	// Token: 0x06000486 RID: 1158 RVA: 0x00014F68 File Offset: 0x00013168
	public override void PlayerDied(Kit_IngameMain main, bool botKiller, int killer, bool botKilled, int killed)
	{
		if (main.currentGameModeRuntimeData == null || main.currentGameModeRuntimeData.GetType() != typeof(PlantingBombRuntimeData))
		{
			main.currentGameModeRuntimeData = new PlantingBombRuntimeData
			{
				teamPoints = new int[Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, this.maximumAmountOfTeams)]
			};
		}
		object currentGameModeRuntimeData = main.currentGameModeRuntimeData;
		if (botKiller)
		{
			if (main.currentBotManager && (!botKilled || killed != killer))
			{
				Kit_Bot botWithID = main.currentBotManager.GetBotWithID(killer);
				if (botWithID != null)
				{
					int team = botWithID.team;
				}
			}
		}
		else if (botKilled || killed != killer)
		{
			Player player = null;
			for (int i = 0; i < PhotonNetwork.PlayerList.Length; i++)
			{
				if (PhotonNetwork.PlayerList[i].ActorNumber == killer)
				{
					player = PhotonNetwork.PlayerList[i];
					break;
				}
			}
			if (player != null)
			{
				int num = (int)player.CustomProperties["team"];
			}
		}
		if (this.AreEnoughPlayersThere(this._main) && main.gameModeStage < 2)
		{
			this.CompleteProcessor(main, this.FindPBByID(killed, botKilled), false);
		}
	}

	// Token: 0x06000487 RID: 1159 RVA: 0x0001507C File Offset: 0x0001327C
	public override void TimeRunOut(Kit_IngameMain main)
	{
		if (main.currentGameModeRuntimeData == null || main.currentGameModeRuntimeData.GetType() != typeof(PlantingBombRuntimeData))
		{
			main.currentGameModeRuntimeData = new PlantingBombRuntimeData
			{
				teamPoints = new int[Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, this.maximumAmountOfTeams)]
			};
		}
		PlantingBombRuntimeData plantingBombRuntimeData = main.currentGameModeRuntimeData as PlantingBombRuntimeData;
		if (main.gameModeStage == 0)
		{
			if (!this.CheckFinalWiner(main))
			{
				if (Kit_GameSettings.currentNetworkingMode == KitNetworkingMode.Traditional)
				{
					main.timer = (float)main.currentGameModeBehaviour.traditionalDurations[Kit_GameSettings.gameLength];
				}
				else if (Kit_GameSettings.currentNetworkingMode == KitNetworkingMode.Lobby)
				{
					main.timer = (float)main.currentGameModeBehaviour.lobbyGameDuration;
				}
				main.gameModeStage = 1;
				return;
			}
		}
		else if (main.gameModeStage == 1)
		{
			if (!this._isEndingRound)
			{
				int num = this._isBombSetuped ? this._terroristTeam : this._defenderTeam;
				plantingBombRuntimeData.teamPoints[num]++;
				plantingBombRuntimeData.teamCommonPoints[num]++;
				this.CompleteProcessor(main, null, true);
				if (PhotonNetwork.IsMasterClient)
				{
					PhotonNetwork.RaiseEvent(Kit_EventIDs.bombExplosionEvent, null, new RaiseEventOptions
					{
						Receivers = ReceiverGroup.All
					}, SendOptions.SendReliable);
					return;
				}
			}
		}
		else if (main.gameModeStage == 2)
		{
			if (TournamentsController.Instance.IsItTournament)
			{
				main.Disconnect();
				return;
			}
			if (main.currentVictoryScreen)
			{
				PhotonNetwork.Destroy(main.currentVictoryScreen.photonView);
			}
			if (Kit_GameSettings.currentNetworkingMode == KitNetworkingMode.Traditional)
			{
				main.timer = this.mapVotingTime;
				main.gameModeStage = 3;
				main.OpenVotingMenu();
				main.DeleteAllPlayers();
				return;
			}
			if (Kit_GameSettings.currentNetworkingMode == KitNetworkingMode.Lobby)
			{
				main.DeleteAllPlayers();
				main.gameModeStage = 5;
				Kit_SceneSyncer.instance.LoadScene("MainMenu");
				return;
			}
		}
		else if (main.gameModeStage == 3)
		{
			main.gameModeStage = 4;
			Hashtable customProperties = PhotonNetwork.CurrentRoom.CustomProperties;
			MapGameModeCombo comboWithMostVotes = main.currentMapVoting.GetComboWithMostVotes();
			PhotonNetwork.Destroy(main.currentMapVoting.gameObject);
			customProperties["gameMode"] = comboWithMostVotes.gameMode;
			customProperties["map"] = comboWithMostVotes.map;
			PhotonNetwork.CurrentRoom.SetCustomProperties(customProperties, null, null);
			Kit_SceneSyncer.instance.LoadScene(main.gameInformation.allPvpGameModes[comboWithMostVotes.gameMode].traditionalMaps[comboWithMostVotes.map].sceneName);
		}
	}

	// Token: 0x06000488 RID: 1160 RVA: 0x000152F0 File Offset: 0x000134F0
	public void ChangeTeamSpawnConditions()
	{
		int num = this._layers.Length;
		int[] array = new int[num];
		for (int i = 0; i < num; i++)
		{
			array[i] = this._layers[num - i - 1];
		}
		for (int j = 0; j < num; j++)
		{
			this._layers[j] = array[j];
		}
		this._defenderTeam = ((this._defenderTeam == 0) ? 1 : 0);
		this._terroristTeam = ((this._terroristTeam == 1) ? 0 : 1);
	}

	// Token: 0x17000059 RID: 89
	// (get) Token: 0x06000489 RID: 1161 RVA: 0x00015364 File Offset: 0x00013564
	public int[] Rounds
	{
		get
		{
			return this.roundsCount;
		}
	}

	// Token: 0x04000362 RID: 866
	public int[] roundsCount;

	// Token: 0x04000363 RID: 867
	public float BombTimer = 60f;

	// Token: 0x04000364 RID: 868
	[Tooltip("The maximum amount of difference the teams can have in player count")]
	public int maxTeamDifference = 2;

	// Token: 0x04000365 RID: 869
	public float votingThreshold = 30f;

	// Token: 0x04000366 RID: 870
	[Header("Times")]
	public float preGameTime = 20f;

	// Token: 0x04000367 RID: 871
	public float endGameTime = 10f;

	// Token: 0x04000368 RID: 872
	public float mapVotingTime = 20f;

	// Token: 0x04000369 RID: 873
	[Tooltip("Spawn layer used for the teams during countdown")]
	[Header("Spawns")]
	public int[] teamsInitialSpawnLayer;

	// Token: 0x0400036A RID: 874
	[Tooltip("Spawn layer used for teams during gameplay")]
	public int[] teamsGameplaySpawnLayer;

	// Token: 0x0400036C RID: 876
	private WaitProcess _waitProcessBeforeRespawn;

	// Token: 0x0400036D RID: 877
	private float _waitTime = 5f;

	// Token: 0x0400036E RID: 878
	private WaitProcess _waitProcessinRespawn;

	// Token: 0x0400036F RID: 879
	private float _waitTimeinRespawn;

	// Token: 0x04000370 RID: 880
	private int[] _layers;

	// Token: 0x04000371 RID: 881
	private int _defenderTeam;

	// Token: 0x04000372 RID: 882
	private int _terroristTeam = 1;

	// Token: 0x04000373 RID: 883
	private bool _isEndingRound;

	// Token: 0x04000374 RID: 884
	private BombSpawner _bombSpawner;

	// Token: 0x04000375 RID: 885
	private Kit_IngameMain _main;

	// Token: 0x04000376 RID: 886
	private bool _isBombSetuped;

	// Token: 0x04000377 RID: 887
	private BombView _bombView;

	// Token: 0x04000378 RID: 888
	private BombPlace _bombPlace;

	// Token: 0x04000379 RID: 889
	private int _currentRoundCount;

	// Token: 0x0400037A RID: 890
	private float _streamReadTimer;
}
﻿using System;
using System.Collections.Generic;
using System.Linq;
using ExitGames.Client.Photon;
using MarsFPSKit;
using MarsFPSKit.Spectating;
using Photon.Pun;
using Photon.Realtime;
using UnityEngine;

// Token: 0x020000B5 RID: 181
[CreateAssetMenu(menuName = "MarsFPSKit/Gamemodes/Team Deathmatch Rounds Logic")]
public class PVPGMBTeamDeathmatchRounds : Kit_PvP_GameModeBase, IRounds
{
	// Token: 0x17000050 RID: 80
	// (get) Token: 0x06000429 RID: 1065 RVA: 0x00012108 File Offset: 0x00010308
	private int playersInTeamOne
	{
		get
		{
			int num = 0;
			for (int i = 0; i < PhotonNetwork.PlayerList.Length; i++)
			{
				if (PhotonNetwork.PlayerList[i].CustomProperties["team"] != null && (int)PhotonNetwork.PlayerList[i].CustomProperties["team"] == 0)
				{
					num++;
				}
			}
			return num;
		}
	}

	// Token: 0x17000051 RID: 81
	// (get) Token: 0x0600042A RID: 1066 RVA: 0x00012164 File Offset: 0x00010364
	private int playersInTeamTwo
	{
		get
		{
			int num = 0;
			for (int i = 0; i < PhotonNetwork.PlayerList.Length; i++)
			{
				if (PhotonNetwork.PlayerList[i].CustomProperties["team"] != null && (int)PhotonNetwork.PlayerList[i].CustomProperties["team"] == 1)
				{
					num++;
				}
			}
			return num;
		}
	}

	// Token: 0x0600042B RID: 1067 RVA: 0x000121C0 File Offset: 0x000103C0
	private void CheckRoundWiner(Kit_IngameMain main, Kit_PlayerBehaviour pb)
	{
		if (this._isEndingRound)
		{
			return;
		}
		if (this.IsTeamDead(main, 0, pb))
		{
			(main.currentGameModeRuntimeData as TeamDeathmatchRoundsRuntimeData).teamPoints[1]++;
			if (!this.CheckFinalWiner(main))
			{
				this._isEndingRound = true;
				this._waitProcessBeforeRespawn.StartProcess(delegate
				{
					this.Respawn(main);
					this._isEndingRound = false;
				}, this._waitTime);
				return;
			}
		}
		else if (this.IsTeamDead(main, 1, pb))
		{
			(main.currentGameModeRuntimeData as TeamDeathmatchRoundsRuntimeData).teamPoints[0]++;
			if (!this.CheckFinalWiner(main))
			{
				this._isEndingRound = true;
				this._waitProcessBeforeRespawn.StartProcess(delegate
				{
					this.Respawn(main);
					this._isEndingRound = false;
				}, this._waitTime);
			}
		}
	}

	// Token: 0x0600042C RID: 1068 RVA: 0x000122B4 File Offset: 0x000104B4
	private bool IsTeamDead(Kit_IngameMain main, int team, Kit_PlayerBehaviour pb)
	{
		Kit_PlayerBehaviour[] array = UnityEngine.Object.FindObjectsOfType<Kit_PlayerBehaviour>();
		for (int i = 0; i < array.Length; i++)
		{
			if (array[i].myTeam == team && array[i] != pb)
			{
				return false;
			}
		}
		return true;
	}

	// Token: 0x0600042D RID: 1069 RVA: 0x000122EE File Offset: 0x000104EE
	private void Respawn(Kit_IngameMain main)
	{
		PhotonNetwork.RaiseEvent(Kit_EventIDs.resetRoundEvent, null, new RaiseEventOptions
		{
			Receivers = ReceiverGroup.All
		}, SendOptions.SendReliable);
	}

	// Token: 0x0600042E RID: 1070 RVA: 0x00012310 File Offset: 0x00010510
	private bool CheckFinalWiner(Kit_IngameMain main)
	{
		if (main.gameModeStage < 2)
		{
			if (main.currentGameModeRuntimeData == null || main.currentGameModeRuntimeData.GetType() != typeof(TeamDeathmatchRoundsRuntimeData))
			{
				main.currentGameModeRuntimeData = new TeamDeathmatchRoundsRuntimeData
				{
					teamPoints = new int[Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, this.maximumAmountOfTeams)]
				};
			}
			TeamDeathmatchRoundsRuntimeData teamDeathmatchRoundsRuntimeData = main.currentGameModeRuntimeData as TeamDeathmatchRoundsRuntimeData;
			for (int i = 0; i < teamDeathmatchRoundsRuntimeData.teamPoints.Length; i++)
			{
				if (teamDeathmatchRoundsRuntimeData.teamPoints[i] >= this.roundsCount[this._currentRoundsCount] || this.IsTeamAlone(i))
				{
					main.EndGame(i, teamDeathmatchRoundsRuntimeData.teamPoints);
					main.timer = this.endGameTime;
					main.gameModeStage = 2;
					return true;
				}
			}
		}
		return false;
	}

	// Token: 0x0600042F RID: 1071 RVA: 0x000123E0 File Offset: 0x000105E0
	private Kit_PlayerBehaviour FindPBByID(int id, bool isBot)
	{
		foreach (Kit_PlayerBehaviour kit_PlayerBehaviour in UnityEngine.Object.FindObjectsOfType<Kit_PlayerBehaviour>())
		{
			if (kit_PlayerBehaviour.id == id && kit_PlayerBehaviour.isBot == isBot)
			{
				return kit_PlayerBehaviour;
			}
		}
		return null;
	}

	// Token: 0x06000430 RID: 1072 RVA: 0x0001241C File Offset: 0x0001061C
	private void ClearTeamPoints(TeamDeathmatchRoundsRuntimeData runtimeData)
	{
		for (int i = 0; i < runtimeData.teamPoints.Length; i++)
		{
			runtimeData.teamPoints[i] = 0;
		}
	}

	// Token: 0x06000431 RID: 1073 RVA: 0x00012445 File Offset: 0x00010645
	private bool IsTeamAlone(int team)
	{
		if (!TournamentsController.Instance.IsItTournament)
		{
			return false;
		}
		if (team == 0)
		{
			if (this.playersInTeamTwo <= 0)
			{
				return true;
			}
		}
		else if (team == 1 && this.playersInTeamOne <= 0)
		{
			return true;
		}
		return false;
	}

	// Token: 0x06000432 RID: 1074 RVA: 0x00012474 File Offset: 0x00010674
	public override bool AreEnoughPlayersThere(Kit_IngameMain main)
	{
		if (main && main.currentBotManager && main.currentBotManager.bots.Count > 0)
		{
			return true;
		}
		if (PhotonNetwork.CurrentRoom.CustomProperties["lobby"] != null && (bool)PhotonNetwork.CurrentRoom.CustomProperties["lobby"])
		{
			return PhotonNetwork.PlayerList.Length >= main.currentGameModeBehaviour.lobbyMinimumPlayersNeeded;
		}
		return PhotonNetwork.PlayerList.Length >= main.currentGameModeBehaviour.traditionalPlayerNeeded[(int)PhotonNetwork.CurrentRoom.CustomProperties["playerNeeded"]];
	}

	// Token: 0x06000433 RID: 1075 RVA: 0x00012525 File Offset: 0x00010725
	public override bool ArePlayersEnemies(Kit_PlayerBehaviour playerOne, Kit_PlayerBehaviour playerTwo)
	{
		return playerOne.myTeam != playerTwo.myTeam;
	}

	// Token: 0x06000434 RID: 1076 RVA: 0x00012538 File Offset: 0x00010738
	public override bool ArePlayersEnemies(Kit_IngameMain main, int playerOneID, bool playerOneBot, int playerTwoID, bool playerTwoBot, bool canKillSelf = false)
	{
		if (playerTwoBot && playerOneBot && playerOneID == playerTwoID && canKillSelf)
		{
			return true;
		}
		int num;
		if (playerOneBot)
		{
			num = main.currentBotManager.GetBotWithID(playerOneID).team;
		}
		else
		{
			num = (int)Kit_PhotonPlayerExtensions.Find(playerOneID).CustomProperties["team"];
		}
		int num2;
		if (playerTwoBot)
		{
			num2 = main.currentBotManager.GetBotWithID(playerTwoID).team;
		}
		else
		{
			num2 = (int)Kit_PhotonPlayerExtensions.Find(playerTwoID).CustomProperties["team"];
		}
		return num != num2;
	}

	// Token: 0x06000435 RID: 1077 RVA: 0x000125D0 File Offset: 0x000107D0
	public override bool ArePlayersEnemies(Kit_IngameMain main, int playerOneID, bool playerOneBot, Kit_PlayerBehaviour playerTwo, bool canKillSelf)
	{
		if (playerTwo.isBot && playerOneBot && playerOneID == playerTwo.botId && canKillSelf)
		{
			return true;
		}
		int num;
		if (playerOneBot)
		{
			num = main.currentBotManager.GetBotWithID(playerOneID).team;
		}
		else
		{
			num = (int)Kit_PhotonPlayerExtensions.Find(playerOneID).CustomProperties["team"];
		}
		return num != playerTwo.myTeam;
	}

	// Token: 0x06000436 RID: 1078 RVA: 0x00012640 File Offset: 0x00010840
	public override bool AreWeEnemies(Kit_IngameMain main, bool botEnemy, int enemyId)
	{
		if (!botEnemy && enemyId == PhotonNetwork.LocalPlayer.ActorNumber)
		{
			return true;
		}
		int num;
		if (botEnemy)
		{
			Kit_Bot botWithID = main.currentBotManager.GetBotWithID(enemyId);
			if (botWithID == null)
			{
				return false;
			}
			num = botWithID.team;
		}
		else
		{
			num = (int)Kit_PhotonPlayerExtensions.Find(enemyId).CustomProperties["team"];
		}
		return main.assignedTeamID != num;
	}

	// Token: 0x06000437 RID: 1079 RVA: 0x000126A6 File Offset: 0x000108A6
	public override bool CanControlPlayer(Kit_IngameMain main)
	{
		return (!this.AreEnoughPlayersThere(main) && !main.hasGameModeStarted) || main.gameModeStage == 1;
	}

	// Token: 0x06000438 RID: 1080 RVA: 0x000126C4 File Offset: 0x000108C4
	public override bool CanJoinTeam(Kit_IngameMain main, Player player, int team)
	{
		int maxPlayers = (int)PhotonNetwork.CurrentRoom.MaxPlayers;
		if (team == 0 && this.playersInTeamOne >= maxPlayers / 2)
		{
			return false;
		}
		if (team == 1 && this.playersInTeamTwo >= maxPlayers / 2)
		{
			return false;
		}
		if (team == 0)
		{
			if (this.playersInTeamOne - this.playersInTeamTwo > this.maxTeamDifference)
			{
				return false;
			}
		}
		else if (team == 1 && this.playersInTeamTwo - this.playersInTeamOne > this.maxTeamDifference)
		{
			return false;
		}
		return true;
	}

	// Token: 0x06000439 RID: 1081 RVA: 0x00012734 File Offset: 0x00010934
	public override bool CanSpawn(Kit_IngameMain main, Player player)
	{
		if (main.gameModeStage < 2 && player.CustomProperties["team"] != null && player.CustomProperties["team"].GetType() == typeof(int))
		{
			int num = (int)player.CustomProperties["team"];
			if (num >= 0 && num < main.gameInformation.allPvpTeams.Length)
			{
				return true;
			}
		}
		return false;
	}

	// Token: 0x0600043A RID: 1082 RVA: 0x000127AF File Offset: 0x000109AF
	public override bool CanStartVote(Kit_IngameMain main)
	{
		return (!this.AreEnoughPlayersThere(main) && !main.hasGameModeStarted) || (main.gameModeStage == 1 && main.timer > this.votingThreshold);
	}

	// Token: 0x0600043B RID: 1083 RVA: 0x000127E0 File Offset: 0x000109E0
	public override void GameModeBeginMiddle(Kit_IngameMain main)
	{
		PhotonNetwork.RaiseEvent(3, null, new RaiseEventOptions
		{
			Receivers = ReceiverGroup.All
		}, SendOptions.SendReliable);
		if (main.currentGameModeRuntimeData != null && main.currentGameModeRuntimeData.GetType() == typeof(TeamDeathmatchRoundsRuntimeData))
		{
			TeamDeathmatchRoundsRuntimeData teamDeathmatchRoundsRuntimeData = main.currentGameModeRuntimeData as TeamDeathmatchRoundsRuntimeData;
			for (int i = 0; i < teamDeathmatchRoundsRuntimeData.teamPoints.Length; i++)
			{
				teamDeathmatchRoundsRuntimeData.teamPoints[i] = 0;
			}
		}
		Kit_WeaponSpawner[] array = UnityEngine.Object.FindObjectsOfType<Kit_WeaponSpawner>();
		for (int j = 0; j < array.Length; j++)
		{
			array[j].GameModeBeginMiddle();
		}
		Kit_AmmoSpawner[] array2 = UnityEngine.Object.FindObjectsOfType<Kit_AmmoSpawner>();
		for (int k = 0; k < array2.Length; k++)
		{
			array2[k].GameModeBeginMiddle();
		}
	}

	// Token: 0x0600043C RID: 1084 RVA: 0x00012894 File Offset: 0x00010A94
	public override void GamemodeSetup(Kit_IngameMain main)
	{
		Kit_PlayerSpawn[] array = UnityEngine.Object.FindObjectsOfType<Kit_PlayerSpawn>();
		if (array.Length == 0)
		{
			throw new Exception("This scene has no spawns.");
		}
		List<Kit_PlayerSpawn> list = new List<Kit_PlayerSpawn>();
		int num = 0;
		for (int i = 0; i < array.Length; i++)
		{
			int num2 = i;
			if (array[num2].gameModes.Contains(this))
			{
				list.Add(array[num2]);
				if (array[num2].spawnGroupID > num)
				{
					num = array[num2].spawnGroupID;
				}
			}
		}
		main.internalSpawns = new List<InternalSpawns>();
		for (int j = 0; j < num + 1; j++)
		{
			main.internalSpawns.Add(null);
		}
		for (int k = 0; k < main.internalSpawns.Count; k++)
		{
			int num3 = k;
			main.internalSpawns[num3] = new InternalSpawns();
			main.internalSpawns[num3].spawns = new List<Kit_PlayerSpawn>();
			for (int l = 0; l < list.Count; l++)
			{
				int index = l;
				if (list[index].spawnGroupID == num3)
				{
					main.internalSpawns[num3].spawns.Add(list[index]);
				}
			}
		}
		main.gameModeStage = 0;
		main.timer = this.preGameTime;
		main.currentGameModeRuntimeData = new TeamDeathmatchRoundsRuntimeData
		{
			teamPoints = new int[Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, this.maximumAmountOfTeams)]
		};
		main.SetupAutoSpawnAndBotManager(false);
		this._waitProcessBeforeRespawn = new WaitProcess();
		this._waitProcessinRespawn = new WaitProcess();
		this._layers = new int[]
		{
			1,
			2
		};
		this._isEndingRound = false;
		this._currentRoundsCount = (int)PhotonNetwork.CurrentRoom.CustomProperties["roundCount"];
	}

	// Token: 0x0600043D RID: 1085 RVA: 0x00005F78 File Offset: 0x00004178
	public override void GameModeUpdate(Kit_IngameMain main)
	{
	}

	// Token: 0x0600043E RID: 1086 RVA: 0x00012A5C File Offset: 0x00010C5C
	public override Transform GetSpawn(Kit_IngameMain main, Player player)
	{
		int num = 0;
		Transform transform = null;
		while (!transform && num < 10)
		{
			int num2;
			if (main.gameModeStage == 0)
			{
				num2 = this.teamsInitialSpawnLayer[Mathf.Clamp(main.assignedTeamID, 0, this.teamsInitialSpawnLayer.Length - 1)];
			}
			else
			{
				num2 = this.teamsGameplaySpawnLayer[Mathf.Clamp(main.assignedTeamID, 0, this.teamsGameplaySpawnLayer.Length - 1)];
			}
			num2 = this._layers[num2 - 1];
			Transform transform2 = main.internalSpawns[num2].spawns[UnityEngine.Random.Range(0, main.internalSpawns[num2].spawns.Count)].transform;
			if (transform2 && this.spawnSystemToUse.CheckSpawnPosition(main, transform2, player))
			{
				transform = transform2;
				break;
			}
			num++;
		}
		if (!transform)
		{
			int index;
			if (main.gameModeStage == 0)
			{
				index = this.teamsInitialSpawnLayer[Mathf.Clamp(main.assignedTeamID, 0, this.teamsInitialSpawnLayer.Length - 1)];
			}
			else
			{
				index = this.teamsGameplaySpawnLayer[Mathf.Clamp(main.assignedTeamID, 0, this.teamsGameplaySpawnLayer.Length - 1)];
			}
			return main.internalSpawns[index].spawns[UnityEngine.Random.Range(0, main.internalSpawns[index].spawns.Count)].transform;
		}
		return transform;
	}

	// Token: 0x0600043F RID: 1087 RVA: 0x00012BC4 File Offset: 0x00010DC4
	public override Transform GetSpawn(Kit_IngameMain main, Kit_Bot bot)
	{
		int num = 0;
		Transform transform = null;
		while (!transform && num < 10)
		{
			int num2;
			if (main.gameModeStage == 0)
			{
				num2 = this.teamsInitialSpawnLayer[Mathf.Clamp(bot.team, 0, this.teamsInitialSpawnLayer.Length - 1)];
			}
			else
			{
				num2 = this.teamsGameplaySpawnLayer[Mathf.Clamp(bot.team, 0, this.teamsGameplaySpawnLayer.Length - 1)];
			}
			num2 = this._layers[num2 - 1];
			Transform transform2 = main.internalSpawns[num2].spawns[UnityEngine.Random.Range(0, main.internalSpawns[num2].spawns.Count)].transform;
			if (transform2 && this.spawnSystemToUse.CheckSpawnPosition(main, transform2, bot))
			{
				transform = transform2;
				break;
			}
			num++;
		}
		if (!transform)
		{
			int num3;
			if (main.gameModeStage == 0)
			{
				num3 = this.teamsInitialSpawnLayer[Mathf.Clamp(bot.team, 0, this.teamsInitialSpawnLayer.Length - 1)];
			}
			else
			{
				num3 = this.teamsGameplaySpawnLayer[Mathf.Clamp(bot.team, 0, this.teamsGameplaySpawnLayer.Length - 1)];
			}
			num3 = this._layers[num3 - 1];
			return main.internalSpawns[num3].spawns[UnityEngine.Random.Range(0, main.internalSpawns[num3].spawns.Count)].transform;
		}
		return transform;
	}

	// Token: 0x06000440 RID: 1088 RVA: 0x00012D36 File Offset: 0x00010F36
	public override Spectateable GetSpectateable(Kit_IngameMain main)
	{
		if (main.assignedTeamID >= 0)
		{
			return Spectateable.Friendlies;
		}
		return Spectateable.All;
	}

	// Token: 0x06000441 RID: 1089 RVA: 0x00012D44 File Offset: 0x00010F44
	public override void OnPhotonSerializeView(Kit_IngameMain main, PhotonStream stream, PhotonMessageInfo info)
	{
		if (main.currentGameModeRuntimeData == null || main.currentGameModeRuntimeData.GetType() != typeof(TeamDeathmatchRoundsRuntimeData))
		{
			main.currentGameModeRuntimeData = new TeamDeathmatchRoundsRuntimeData
			{
				teamPoints = new int[Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, main.currentGameModeBehaviour.maximumAmountOfTeams)]
			};
		}
		TeamDeathmatchRoundsRuntimeData teamDeathmatchRoundsRuntimeData = main.currentGameModeRuntimeData as TeamDeathmatchRoundsRuntimeData;
		if (stream.IsWriting)
		{
			for (int i = 0; i < teamDeathmatchRoundsRuntimeData.teamPoints.Length; i++)
			{
				stream.SendNext(teamDeathmatchRoundsRuntimeData.teamPoints[i]);
			}
			stream.SendNext(this._defenderTeam);
			stream.SendNext(this._terroristTeam);
			for (int j = 0; j < this._layers.Length; j++)
			{
				stream.SendNext(this._layers[j]);
			}
			return;
		}
		for (int k = 0; k < teamDeathmatchRoundsRuntimeData.teamPoints.Length; k++)
		{
			teamDeathmatchRoundsRuntimeData.teamPoints[k] = (int)stream.ReceiveNext();
		}
		this._defenderTeam = (int)stream.ReceiveNext();
		this._terroristTeam = (int)stream.ReceiveNext();
		for (int l = 0; l < this._layers.Length; l++)
		{
			this._layers[l] = (int)stream.ReceiveNext();
		}
	}

	// Token: 0x06000442 RID: 1090 RVA: 0x00012EA8 File Offset: 0x000110A8
	public override void PlayerDied(Kit_IngameMain main, bool botKiller, int killer, bool botKilled, int killed)
	{
		if (main.currentGameModeRuntimeData == null || main.currentGameModeRuntimeData.GetType() != typeof(TeamDeathmatchRoundsRuntimeData))
		{
			main.currentGameModeRuntimeData = new TeamDeathmatchRoundsRuntimeData
			{
				teamPoints = new int[Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, this.maximumAmountOfTeams)]
			};
		}
		object currentGameModeRuntimeData = main.currentGameModeRuntimeData;
		if (botKiller)
		{
			if (main.currentBotManager && (!botKilled || killed != killer))
			{
				Kit_Bot botWithID = main.currentBotManager.GetBotWithID(killer);
				if (botWithID != null)
				{
					int team = botWithID.team;
				}
			}
		}
		else if (botKilled || killed != killer)
		{
			Player player = null;
			for (int i = 0; i < PhotonNetwork.PlayerList.Length; i++)
			{
				if (PhotonNetwork.PlayerList[i].ActorNumber == killer)
				{
					player = PhotonNetwork.PlayerList[i];
					break;
				}
			}
			if (player != null)
			{
				int num = (int)player.CustomProperties["team"];
			}
		}
		this.CheckRoundWiner(main, this.FindPBByID(killed, botKilled));
	}

	// Token: 0x06000443 RID: 1091 RVA: 0x00012FA4 File Offset: 0x000111A4
	public override void TimeRunOut(Kit_IngameMain main)
	{
		if (main.currentGameModeRuntimeData == null || main.currentGameModeRuntimeData.GetType() != typeof(TeamDeathmatchRoundsRuntimeData))
		{
			TeamDeathmatchRoundsRuntimeData teamDeathmatchRoundsRuntimeData = new TeamDeathmatchRoundsRuntimeData();
			teamDeathmatchRoundsRuntimeData.teamPoints = new int[Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, this.maximumAmountOfTeams)];
			main.currentGameModeRuntimeData = teamDeathmatchRoundsRuntimeData;
		}
		TeamDeathmatchRoundsRuntimeData teamDeathmatchRoundsRuntimeData2 = main.currentGameModeRuntimeData as TeamDeathmatchRoundsRuntimeData;
		if (main.gameModeStage == 0)
		{
			if (!this.CheckFinalWiner(main))
			{
				if (Kit_GameSettings.currentNetworkingMode == KitNetworkingMode.Traditional)
				{
					main.timer = (float)main.currentGameModeBehaviour.traditionalDurations[Kit_GameSettings.gameLength];
				}
				else if (Kit_GameSettings.currentNetworkingMode == KitNetworkingMode.Lobby)
				{
					main.timer = (float)main.currentGameModeBehaviour.lobbyGameDuration;
				}
				main.gameModeStage = 1;
				return;
			}
		}
		else if (main.gameModeStage == 1)
		{
			if (!this._isEndingRound)
			{
				if (this.playersInTeamOne >= this.playersInTeamTwo)
				{
					teamDeathmatchRoundsRuntimeData2.teamPoints[this._defenderTeam]++;
				}
				else
				{
					teamDeathmatchRoundsRuntimeData2.teamPoints[this._terroristTeam]++;
				}
				if (!this.CheckFinalWiner(main))
				{
					this._isEndingRound = true;
					this._waitProcessBeforeRespawn.StartProcess(delegate
					{
						this.Respawn(main);
						this._isEndingRound = false;
					}, this._waitTime);
					return;
				}
			}
		}
		else if (main.gameModeStage == 2)
		{
			if (TournamentsController.Instance.IsItTournament)
			{
				main.Disconnect();
				return;
			}
			if (main.currentVictoryScreen)
			{
				PhotonNetwork.Destroy(main.currentVictoryScreen.photonView);
			}
			if (Kit_GameSettings.currentNetworkingMode == KitNetworkingMode.Traditional)
			{
				main.timer = this.mapVotingTime;
				main.gameModeStage = 3;
				main.OpenVotingMenu();
				main.DeleteAllPlayers();
				return;
			}
			if (Kit_GameSettings.currentNetworkingMode == KitNetworkingMode.Lobby)
			{
				main.DeleteAllPlayers();
				main.gameModeStage = 5;
				Kit_SceneSyncer.instance.LoadScene("MainMenu");
				return;
			}
		}
		else if (main.gameModeStage == 3)
		{
			main.gameModeStage = 4;
			Hashtable customProperties = PhotonNetwork.CurrentRoom.CustomProperties;
			MapGameModeCombo comboWithMostVotes = main.currentMapVoting.GetComboWithMostVotes();
			PhotonNetwork.Destroy(main.currentMapVoting.gameObject);
			customProperties["gameMode"] = comboWithMostVotes.gameMode;
			customProperties["map"] = comboWithMostVotes.map;
			PhotonNetwork.CurrentRoom.SetCustomProperties(customProperties, null, null);
			Kit_SceneSyncer.instance.LoadScene(main.gameInformation.allPvpGameModes[comboWithMostVotes.gameMode].traditionalMaps[comboWithMostVotes.map].sceneName);
		}
	}

	// Token: 0x06000444 RID: 1092 RVA: 0x000132C0 File Offset: 0x000114C0
	public void ChangeTeamSpawnConditions()
	{
		int num = this._layers.Length;
		int[] array = new int[num];
		for (int i = 0; i < num; i++)
		{
			array[i] = this._layers[num - i - 1];
		}
		for (int j = 0; j < num; j++)
		{
			this._layers[j] = array[j];
		}
		this._defenderTeam = ((this._defenderTeam == 0) ? 1 : 0);
		this._terroristTeam = ((this._terroristTeam == 1) ? 0 : 1);
	}

	// Token: 0x17000052 RID: 82
	// (get) Token: 0x06000445 RID: 1093 RVA: 0x00013334 File Offset: 0x00011534
	public int[] Rounds
	{
		get
		{
			return this.roundsCount;
		}
	}

	// Token: 0x04000328 RID: 808
	public int[] roundsCount;

	// Token: 0x04000329 RID: 809
	[Tooltip("The maximum amount of difference the teams can have in player count")]
	public int maxTeamDifference = 2;

	// Token: 0x0400032A RID: 810
	public float votingThreshold = 30f;

	// Token: 0x0400032B RID: 811
	[Header("Times")]
	public float preGameTime = 20f;

	// Token: 0x0400032C RID: 812
	public float endGameTime = 10f;

	// Token: 0x0400032D RID: 813
	public float mapVotingTime = 20f;

	// Token: 0x0400032E RID: 814
	[Tooltip("Spawn layer used for the teams during countdown")]
	[Header("Spawns")]
	public int[] teamsInitialSpawnLayer;

	// Token: 0x0400032F RID: 815
	[Tooltip("Spawn layer used for teams during gameplay")]
	public int[] teamsGameplaySpawnLayer;

	// Token: 0x04000330 RID: 816
	private WaitProcess _waitProcessBeforeRespawn;

	// Token: 0x04000331 RID: 817
	private float _waitTime = 5f;

	// Token: 0x04000332 RID: 818
	private WaitProcess _waitProcessinRespawn;

	// Token: 0x04000333 RID: 819
	private float _waitTimeinRespawn;

	// Token: 0x04000334 RID: 820
	private bool _isRespawning;

	// Token: 0x04000335 RID: 821
	private int[] _layers;

	// Token: 0x04000336 RID: 822
	private int _defenderTeam;

	// Token: 0x04000337 RID: 823
	private int _terroristTeam = 1;

	// Token: 0x04000338 RID: 824
	private bool _isEndingRound;

	// Token: 0x04000339 RID: 825
	private int _currentRoundsCount;
}
﻿using System;
using UnityEngine;

// Token: 0x02000089 RID: 137
[Serializable]
public class RarityContent
{
	// Token: 0x04000284 RID: 644
	public string Name;

	// Token: 0x04000285 RID: 645
	public RarityType Rarity;

	// Token: 0x04000286 RID: 646
	public Sprite Background;

	// Token: 0x04000287 RID: 647
	public Sprite RarityImage;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x0200008A RID: 138
[CreateAssetMenu(menuName = "Banks/RarityContentBank")]
public class RarityContentBank : ScriptableObject
{
	// Token: 0x06000353 RID: 851 RVA: 0x0000FED4 File Offset: 0x0000E0D4
	public RarityContent GetRarityContent(RarityType rarityType)
	{
		for (int i = 0; i < this.Rarities.Count; i++)
		{
			if (this.Rarities[i].Rarity == rarityType)
			{
				return this.Rarities[i];
			}
		}
		return null;
	}

	// Token: 0x04000288 RID: 648
	public List<RarityContent> Rarities;
}
﻿using System;

// Token: 0x0200006F RID: 111
public enum RarityType
{
	// Token: 0x04000220 RID: 544
	Standard,
	// Token: 0x04000221 RID: 545
	Ellite,
	// Token: 0x04000222 RID: 546
	Epic
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using Photon.Pun;
using Photon.Realtime;
using Photon.Voice.PUN;
using UnityEngine;

// Token: 0x02000062 RID: 98
public class RegionController : IConnectionCallbacks
{
	// Token: 0x14000006 RID: 6
	// (add) Token: 0x06000289 RID: 649 RVA: 0x0000D844 File Offset: 0x0000BA44
	// (remove) Token: 0x0600028A RID: 650 RVA: 0x0000D87C File Offset: 0x0000BA7C
	public event Action<int> OnRegionChanged = delegate(int <p0>)
	{
	};

	// Token: 0x17000025 RID: 37
	// (get) Token: 0x0600028B RID: 651 RVA: 0x0000D8B1 File Offset: 0x0000BAB1
	public static RegionController Instance
	{
		get
		{
			if (RegionController._instance == null)
			{
				RegionController._instance = new RegionController();
			}
			return RegionController._instance;
		}
	}

	// Token: 0x0600028C RID: 652 RVA: 0x0000D8CC File Offset: 0x0000BACC
	private RegionController()
	{
		PhotonNetwork.AddCallbackTarget(this);
	}

	// Token: 0x0600028D RID: 653 RVA: 0x0000D928 File Offset: 0x0000BB28
	public void Initialize()
	{
		this._game = Resources.Load<Kit_GameInformation>("Game");
		this._defaultRegion = this._game.defaultRegion;
		if (PlayerPrefs.HasKey("region"))
		{
			this._currentRegion = PlayerPrefs.GetString("region");
		}
		PhotonNetwork.NetworkingClient.AppVersion = PhotonNetwork.PhotonServerSettings.AppSettings.AppVersion;
		PhotonNetwork.NetworkingClient.AppId = PhotonNetwork.PhotonServerSettings.AppSettings.AppIdRealtime;
	}

	// Token: 0x0600028E RID: 654 RVA: 0x0000D9A4 File Offset: 0x0000BBA4
	public void ChangeRegion(int to)
	{
		if (to != this.GetRegionIndex(this._currentRegion))
		{
			this._currentRegion = this._game.allRegions[to].token;
			PlayerPrefs.SetString("region", this._currentRegion);
			if (PhotonNetwork.IsConnected)
			{
				this._reconnectUponDisconnect = true;
				PhotonNetwork.Disconnect();
				return;
			}
			PhotonNetwork.AuthValues = LoginController.Instance.GetAuthenticationValues();
			PhotonNetwork.ConnectToRegion(this._currentRegion);
			PhotonVoiceNetwork.Instance.ConnectUsingSettings(PhotonNetwork.PhotonServerSettings.AppSettings);
		}
	}

	// Token: 0x0600028F RID: 655 RVA: 0x0000DA2C File Offset: 0x0000BC2C
	public void OnLoggedIn()
	{
		if (!(this._currentRegion == ""))
		{
			PhotonNetwork.AuthValues = LoginController.Instance.GetAuthenticationValues();
			PhotonNetwork.ConnectToRegion(this._currentRegion);
			PhotonVoiceNetwork.Instance.ConnectUsingSettings(PhotonNetwork.PhotonServerSettings.AppSettings);
			return;
		}
		if (this.useBestRegionAsDefault)
		{
			PhotonNetwork.AuthValues = LoginController.Instance.GetAuthenticationValues();
			PhotonNetwork.ConnectToBestCloudServer();
			PhotonVoiceNetwork.Instance.ConnectUsingSettings(PhotonNetwork.PhotonServerSettings.AppSettings);
			return;
		}
		PhotonNetwork.AuthValues = LoginController.Instance.GetAuthenticationValues();
		PhotonNetwork.ConnectToRegion(this._defaultRegion);
		PhotonVoiceNetwork.Instance.ConnectUsingSettings(PhotonNetwork.PhotonServerSettings.AppSettings);
	}

	// Token: 0x06000290 RID: 656 RVA: 0x0000DAE0 File Offset: 0x0000BCE0
	public void GameStartedNotConnected()
	{
		if (!(this._currentRegion == ""))
		{
			PhotonNetwork.AuthValues = LoginController.Instance.GetAuthenticationValues();
			PhotonNetwork.ConnectToRegion(this._currentRegion);
			PhotonVoiceNetwork.Instance.ConnectUsingSettings(PhotonNetwork.PhotonServerSettings.AppSettings);
			return;
		}
		if (this.useBestRegionAsDefault)
		{
			PhotonNetwork.AuthValues = LoginController.Instance.GetAuthenticationValues();
			PhotonNetwork.ConnectToBestCloudServer();
			PhotonVoiceNetwork.Instance.ConnectUsingSettings(PhotonNetwork.PhotonServerSettings.AppSettings);
			return;
		}
		PhotonNetwork.AuthValues = LoginController.Instance.GetAuthenticationValues();
		PhotonNetwork.ConnectToRegion(this._defaultRegion);
		PhotonVoiceNetwork.Instance.ConnectUsingSettings(PhotonNetwork.PhotonServerSettings.AppSettings);
	}

	// Token: 0x06000291 RID: 657 RVA: 0x0000DB94 File Offset: 0x0000BD94
	public int GetRegionIndex(string token)
	{
		int result = 0;
		Kit_RegionInformation[] allRegions = this._game.allRegions;
		for (int i = 0; i < allRegions.Length; i++)
		{
			if (allRegions[i].token.Equals(token) || (allRegions[i].token + "/*").Equals(token ?? ""))
			{
				result = i;
			}
		}
		return result;
	}

	// Token: 0x06000292 RID: 658 RVA: 0x0000DBF3 File Offset: 0x0000BDF3
	public int GetCurrentRegionIndex()
	{
		return this.GetRegionIndex(this._currentRegion);
	}

	// Token: 0x06000293 RID: 659 RVA: 0x00005F78 File Offset: 0x00004178
	public void OnConnected()
	{
	}

	// Token: 0x06000294 RID: 660 RVA: 0x0000DC01 File Offset: 0x0000BE01
	public void OnConnectedToMaster()
	{
		PhotonNetwork.JoinLobby();
		this._currentRegion = PhotonNetwork.CloudRegion;
		Debug.Log("[Region Screen] Connected to region: " + this._currentRegion);
		this.OnRegionChanged(this.GetRegionIndex(this._currentRegion));
	}

	// Token: 0x06000295 RID: 661 RVA: 0x0000DC40 File Offset: 0x0000BE40
	public void OnDisconnected(DisconnectCause cause)
	{
		if (this._reconnectUponDisconnect)
		{
			PhotonNetwork.AuthValues = LoginController.Instance.GetAuthenticationValues();
			PhotonNetwork.ConnectToRegion(this._currentRegion);
			PhotonVoiceNetwork.Instance.ConnectUsingSettings(PhotonNetwork.PhotonServerSettings.AppSettings);
			this._reconnectUponDisconnect = false;
		}
	}

	// Token: 0x06000296 RID: 662 RVA: 0x00005F78 File Offset: 0x00004178
	public void OnRegionListReceived(RegionHandler regionHandler)
	{
	}

	// Token: 0x06000297 RID: 663 RVA: 0x00005F78 File Offset: 0x00004178
	public void OnCustomAuthenticationResponse(Dictionary<string, object> data)
	{
	}

	// Token: 0x06000298 RID: 664 RVA: 0x00005F78 File Offset: 0x00004178
	public void OnCustomAuthenticationFailed(string debugMessage)
	{
	}

	// Token: 0x040001C3 RID: 451
	private Kit_GameInformation _game;

	// Token: 0x040001C4 RID: 452
	private string _currentRegion = "";

	// Token: 0x040001C5 RID: 453
	private string _defaultRegion = "";

	// Token: 0x040001C6 RID: 454
	private bool _reconnectUponDisconnect;

	// Token: 0x040001C7 RID: 455
	private bool useBestRegionAsDefault = true;

	// Token: 0x040001C8 RID: 456
	private MenuController _menuController;

	// Token: 0x040001C9 RID: 457
	private static RegionController _instance;
}
﻿using System;
using System.Collections;
using UnityEngine;
using UnityEngine.Networking;

// Token: 0x02000096 RID: 150
public class Requester : MonoBehaviour
{
	// Token: 0x06000381 RID: 897 RVA: 0x000104F3 File Offset: 0x0000E6F3
	public void GetRequestText(string api, WWWForm requestFields, Action<string> callback)
	{
		base.StartCoroutine(this.GetData(api, requestFields, callback));
	}

	// Token: 0x06000382 RID: 898 RVA: 0x00010505 File Offset: 0x0000E705
	private IEnumerator CallAPI(string Url, WWWForm requestFields, Action<string> callback)
	{
		using (UnityWebRequest request = UnityWebRequest.Post(Url, requestFields))
		{
			yield return request.Send();
			if (request.isNetworkError)
			{
				Debug.Log("network problem: " + request.error);
			}
			else if (request.responseCode != 200L)
			{
				Debug.Log(string.Format("response error: {0}", request.responseCode));
			}
			else
			{
				callback(request.downloadHandler.text);
			}
		}
		UnityWebRequest request = null;
		yield break;
		yield break;
	}

	// Token: 0x06000383 RID: 899 RVA: 0x00010522 File Offset: 0x0000E722
	private IEnumerator GetData(string API, WWWForm requestFields, Action<string> callback)
	{
		return this.CallAPI(API, requestFields, callback);
	}

	// Token: 0x06000384 RID: 900 RVA: 0x0001052D File Offset: 0x0000E72D
	public void GetRequestImage(string api, Action<string, Texture2D> callback)
	{
		base.StartCoroutine(this.GetDataImage(api, callback));
	}

	// Token: 0x06000385 RID: 901 RVA: 0x0001053E File Offset: 0x0000E73E
	private IEnumerator CallAPIImage(string url, Action<string, Texture2D> callback)
	{
		using (UnityWebRequest request = UnityWebRequestTexture.GetTexture(url))
		{
			yield return request.Send();
			if (request.isNetworkError)
			{
				Debug.Log("network problem: " + request.error);
			}
			else if (request.responseCode != 200L)
			{
				Debug.Log(string.Format("response error: {0}", request.responseCode));
			}
			else
			{
				callback(url, DownloadHandlerTexture.GetContent(request));
			}
		}
		UnityWebRequest request = null;
		yield break;
		yield break;
	}

	// Token: 0x06000386 RID: 902 RVA: 0x00010554 File Offset: 0x0000E754
	private IEnumerator GetDataImage(string API, Action<string, Texture2D> callback)
	{
		return this.CallAPIImage(API, callback);
	}
}
﻿using System;
using UnityEngine;

// Token: 0x0200003A RID: 58
public class ResetData : MonoBehaviour
{
	// Token: 0x0600015E RID: 350 RVA: 0x00007E72 File Offset: 0x00006072
	public void ResetAllValues()
	{
		Debug.Log("Delette All");
		PlayerPrefs.DeleteAll();
	}
}
﻿using System;
using System.Collections;
using UnityEngine;

// Token: 0x02000019 RID: 25
public class RigidbodyCoroutineHelper : MonoBehaviour
{
	// Token: 0x060000D6 RID: 214 RVA: 0x00005881 File Offset: 0x00003A81
	public IEnumerator AddForceNextFrame(Vector3 force)
	{
		yield return new WaitForEndOfFrame();
		base.GetComponent<Rigidbody>().AddForce(force);
		UnityEngine.Object.Destroy(this);
		yield break;
	}
}
﻿using System;
using UnityEngine;

// Token: 0x0200001A RID: 26
public static class RigidbodyExtensions
{
	// Token: 0x060000D8 RID: 216 RVA: 0x00005897 File Offset: 0x00003A97
	public static void AddForceNextFrame(this Rigidbody body, Vector3 force)
	{
		body.gameObject.AddComponent<RigidbodyCoroutineHelper>().StartCoroutine("AddForceNextFrame", force);
	}
}
﻿using System;
using DG.Tweening;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000CC RID: 204
public class RoundCompleteAnimation : MonoBehaviour
{
	// Token: 0x060004EA RID: 1258 RVA: 0x00017074 File Offset: 0x00015274
	private void OnEnable()
	{
		this._startTextScale = this._textTransform.localScale;
	}

	// Token: 0x060004EB RID: 1259 RVA: 0x00017088 File Offset: 0x00015288
	public void ShowRoundComplete()
	{
		this._mainSequence = DOTween.Sequence();
		this._canvasGroup.alpha = 1f;
		this._mainSequence.Append(this._backgroundImage.DOFade(0f, this._backgroundDuration)).Join(this._textTransform.DOScale(1f, this._textDownDuration)).AppendInterval(this._freezeDureation).AppendCallback(new TweenCallback(this.End));
	}

	// Token: 0x060004EC RID: 1260 RVA: 0x00017109 File Offset: 0x00015309
	public void HideRoundComplete()
	{
		this.End();
	}

	// Token: 0x060004ED RID: 1261 RVA: 0x00017111 File Offset: 0x00015311
	private void End()
	{
		this._canvasGroup.alpha = 0f;
		this.SetStart();
		this._mainSequence.Kill(false);
	}

	// Token: 0x060004EE RID: 1262 RVA: 0x00017135 File Offset: 0x00015335
	private void SetStart()
	{
		this._backgroundImage.color = Color.white;
		this._textTransform.localScale = this._startTextScale;
	}

	// Token: 0x04000454 RID: 1108
	[SerializeField]
	private CanvasGroup _canvasGroup;

	// Token: 0x04000455 RID: 1109
	[SerializeField]
	private Image _backgroundImage;

	// Token: 0x04000456 RID: 1110
	[SerializeField]
	private RectTransform _textTransform;

	// Token: 0x04000457 RID: 1111
	[SerializeField]
	private float _backgroundDuration;

	// Token: 0x04000458 RID: 1112
	[SerializeField]
	private float _textDownDuration;

	// Token: 0x04000459 RID: 1113
	[SerializeField]
	private float _freezeDureation;

	// Token: 0x0400045A RID: 1114
	private Vector3 _startTextScale;

	// Token: 0x0400045B RID: 1115
	private Sequence _mainSequence;
}
﻿using System;
using UnityEngine;

// Token: 0x02000063 RID: 99
public class SceneDriver : MonoBehaviour
{
	// Token: 0x14000007 RID: 7
	// (add) Token: 0x06000299 RID: 665 RVA: 0x0000DC8C File Offset: 0x0000BE8C
	// (remove) Token: 0x0600029A RID: 666 RVA: 0x0000DCC4 File Offset: 0x0000BEC4
	public event Action OnUpdate = delegate()
	{
	};

	// Token: 0x0600029B RID: 667 RVA: 0x0000DCF9 File Offset: 0x0000BEF9
	private void Update()
	{
		this.OnUpdate();
	}
}
﻿using System;
using System.Collections.Generic;
using System.Linq;
using MarsFPSKit;
using MarsFPSKit.Scoreboard;
using Photon.Pun;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000EC RID: 236
public class ScoreboardView : MonoBehaviour
{
	// Token: 0x060005F9 RID: 1529 RVA: 0x0001B488 File Offset: 0x00019688
	private void Update()
	{
		if (this._update && this._parentyScreen.interactable && Time.time > this.lastRedraw)
		{
			this.Redraw();
		}
	}

	// Token: 0x060005FA RID: 1530 RVA: 0x0001B4B4 File Offset: 0x000196B4
	public void Redraw()
	{
		this.lastRedraw = Time.time + this.redrawFrequency;
		for (int i = 0; i < this.rt_ScoreboardEntries.Count; i++)
		{
			this.rt_ScoreboardEntries[i].used = false;
		}
		for (int j = 0; j < PhotonNetwork.PlayerList.Length; j++)
		{
			if (this.rt_ScoreboardEntries.Count > j)
			{
				this.rt_ScoreboardEntries[j].name = PhotonNetwork.PlayerList[j].NickName;
				if (PhotonNetwork.PlayerList[j].CustomProperties["team"] != null)
				{
					this.rt_ScoreboardEntries[j].team = (int)PhotonNetwork.PlayerList[j].CustomProperties["team"];
				}
				else
				{
					this.rt_ScoreboardEntries[j].team = -1;
				}
				if (PhotonNetwork.PlayerList[j].CustomProperties["kills"] != null)
				{
					this.rt_ScoreboardEntries[j].kills = (int)PhotonNetwork.PlayerList[j].CustomProperties["kills"];
				}
				else
				{
					this.rt_ScoreboardEntries[j].kills = 0;
				}
				if (PhotonNetwork.PlayerList[j].CustomProperties["assists"] != null)
				{
					this.rt_ScoreboardEntries[j].assists = (int)PhotonNetwork.PlayerList[j].CustomProperties["assists"];
				}
				else
				{
					this.rt_ScoreboardEntries[j].assists = 0;
				}
				if (PhotonNetwork.PlayerList[j].CustomProperties["deaths"] != null)
				{
					this.rt_ScoreboardEntries[j].deaths = (int)PhotonNetwork.PlayerList[j].CustomProperties["deaths"];
				}
				else
				{
					this.rt_ScoreboardEntries[j].deaths = 0;
				}
				if (PhotonNetwork.PlayerList[j].CustomProperties["ping"] != null)
				{
					this.rt_ScoreboardEntries[j].ping = (int)PhotonNetwork.PlayerList[j].CustomProperties["ping"];
				}
				else
				{
					this.rt_ScoreboardEntries[j].ping = 0;
				}
				this.rt_ScoreboardEntries[j].used = true;
				this.rt_ScoreboardEntries[j].isAlive = this.IsPlayerByIDActive(PhotonNetwork.PlayerList[j].ActorNumber);
				this.rt_ScoreboardEntries[j].isBomb = this.IsPlayerByIDHasBomb(PhotonNetwork.PlayerList[j].ActorNumber);
			}
			else
			{
				Kit_ScoreboardHelper kit_ScoreboardHelper = new Kit_ScoreboardHelper();
				kit_ScoreboardHelper.name = PhotonNetwork.PlayerList[j].NickName;
				if (PhotonNetwork.PlayerList[j].CustomProperties["team"] != null)
				{
					kit_ScoreboardHelper.team = (int)PhotonNetwork.PlayerList[j].CustomProperties["team"];
				}
				else
				{
					kit_ScoreboardHelper.team = -1;
				}
				if (PhotonNetwork.PlayerList[j].CustomProperties["kills"] != null)
				{
					kit_ScoreboardHelper.kills = (int)PhotonNetwork.PlayerList[j].CustomProperties["kills"];
				}
				else
				{
					kit_ScoreboardHelper.kills = 0;
				}
				if (PhotonNetwork.PlayerList[j].CustomProperties["assists"] != null)
				{
					kit_ScoreboardHelper.assists = (int)PhotonNetwork.PlayerList[j].CustomProperties["assists"];
				}
				else
				{
					kit_ScoreboardHelper.assists = 0;
				}
				if (PhotonNetwork.PlayerList[j].CustomProperties["deaths"] != null)
				{
					kit_ScoreboardHelper.deaths = (int)PhotonNetwork.PlayerList[j].CustomProperties["deaths"];
				}
				else
				{
					kit_ScoreboardHelper.deaths = 0;
				}
				if (PhotonNetwork.PlayerList[j].CustomProperties["ping"] != null)
				{
					kit_ScoreboardHelper.ping = (int)PhotonNetwork.PlayerList[j].CustomProperties["ping"];
				}
				else
				{
					kit_ScoreboardHelper.ping = 0;
				}
				kit_ScoreboardHelper.used = true;
				kit_ScoreboardHelper.isAlive = this.IsPlayerByIDActive(PhotonNetwork.PlayerList[j].ActorNumber);
				kit_ScoreboardHelper.isBomb = this.IsPlayerByIDHasBomb(PhotonNetwork.PlayerList[j].ActorNumber);
				this.rt_ScoreboardEntries.Add(kit_ScoreboardHelper);
			}
		}
		if (this.main.currentBotManager)
		{
			for (int k = 0; k < this.main.currentBotManager.bots.Count; k++)
			{
				if (this.rt_ScoreboardEntries.Count > k + PhotonNetwork.PlayerList.Length)
				{
					this.rt_ScoreboardEntries[k + PhotonNetwork.PlayerList.Length].name = this.main.currentBotManager.bots[k].name;
					this.rt_ScoreboardEntries[k + PhotonNetwork.PlayerList.Length].team = this.main.currentBotManager.bots[k].team;
					this.rt_ScoreboardEntries[k + PhotonNetwork.PlayerList.Length].kills = this.main.currentBotManager.bots[k].kills;
					this.rt_ScoreboardEntries[k + PhotonNetwork.PlayerList.Length].assists = this.main.currentBotManager.bots[k].assists;
					this.rt_ScoreboardEntries[k + PhotonNetwork.PlayerList.Length].deaths = this.main.currentBotManager.bots[k].deaths;
					this.rt_ScoreboardEntries[k + PhotonNetwork.PlayerList.Length].ping = 0;
					this.rt_ScoreboardEntries[k + PhotonNetwork.PlayerList.Length].used = true;
					this.rt_ScoreboardEntries[k + PhotonNetwork.PlayerList.Length].isAlive = this.main.currentBotManager.IsBotAlive(this.main.currentBotManager.bots[k]);
					this.rt_ScoreboardEntries[k + PhotonNetwork.PlayerList.Length].isBomb = false;
				}
				else
				{
					Kit_ScoreboardHelper kit_ScoreboardHelper2 = new Kit_ScoreboardHelper();
					kit_ScoreboardHelper2.name = this.main.currentBotManager.bots[k].name;
					kit_ScoreboardHelper2.team = this.main.currentBotManager.bots[k].team;
					kit_ScoreboardHelper2.kills = this.main.currentBotManager.bots[k].kills;
					kit_ScoreboardHelper2.assists = this.main.currentBotManager.bots[k].assists;
					kit_ScoreboardHelper2.deaths = this.main.currentBotManager.bots[k].deaths;
					kit_ScoreboardHelper2.ping = 0;
					kit_ScoreboardHelper2.used = true;
					kit_ScoreboardHelper2.isAlive = this.main.currentBotManager.IsBotAlive(this.main.currentBotManager.bots[k]);
					this.rt_ScoreboardEntries.Add(kit_ScoreboardHelper2);
				}
			}
		}
		this.rt_ScoreboardEntries = (from x in this.rt_ScoreboardEntries
		orderby x.kills
		select x).Reverse<Kit_ScoreboardHelper>().ToList<Kit_ScoreboardHelper>();
		if (this.main.currentGameModeBehaviour.isTeamGameMode)
		{
			if (this.teamActive.Count == 0)
			{
				for (int l = 0; l < Mathf.Clamp(this.main.gameInformation.allPvpTeams.Length, 0, this.main.currentGameModeBehaviour.maximumAmountOfTeams); l++)
				{
					GameObject gameObject = UnityEngine.Object.Instantiate<GameObject>(this.teamPrefab, this.teamGo, false);
					Color teamColor = this.main.gameInformation.allPvpTeams[l].teamColor;
					gameObject.GetComponentInChildren<Image>().color = new Color(teamColor.r, teamColor.g, teamColor.b, 0.5f);
					this.teamActive.Add(gameObject.transform as RectTransform);
					this._teamViews.Add(gameObject.GetComponent<TeamScoreboardView>());
				}
			}
			ITeamGameMode teamGameMode = (ITeamGameMode)this.main.currentGameModeRuntimeData;
			for (int m = 0; m < this._teamViews.Count; m++)
			{
				this._teamViews[m].ScoreText.text = teamGameMode.TeamPoints[m].ToString();
			}
		}
		else if (this.teamActive.Count == 0)
		{
			GameObject gameObject2 = UnityEngine.Object.Instantiate<GameObject>(this.teamPrefab, this.teamGo, false);
			this.teamActive.Add(gameObject2.transform as RectTransform);
			this._teamViews.Add(gameObject2.GetComponent<TeamScoreboardView>());
			this._teamViews[0].ScoreText.enabled = false;
		}
		int num = 0;
		for (int n = 0; n < this.rt_ScoreboardEntries.Count; n++)
		{
			if (this.rt_ScoreboardEntries[n].used && this.rt_ScoreboardEntries[n].team >= 0)
			{
				while (this.activeEntries.Count < num + 1)
				{
					Kit_ScoreboardUIEntry component = UnityEngine.Object.Instantiate<GameObject>(this.entryPrefab, this._teamViews[0].EntriesGo, false).GetComponent<Kit_ScoreboardUIEntry>();
					this.activeEntries.Add(component);
				}
				if (this.main.currentGameModeBehaviour.isTeamGameMode)
				{
					this.activeEntries[num].transform.SetParent(this._teamViews[this.rt_ScoreboardEntries[n].team].EntriesGo);
				}
				else
				{
					this.activeEntries[num].transform.SetParent(this._teamViews[0].EntriesGo);
				}
				this.activeEntries[num].nameText.text = this.rt_ScoreboardEntries[n].name;
				this.activeEntries[num].kills.text = this.rt_ScoreboardEntries[n].kills.ToString();
				this.activeEntries[num].ping.text = this.rt_ScoreboardEntries[n].ping.ToString();
				this.activeEntries[num].score.text = (this.rt_ScoreboardEntries[n].kills * this.main.gameInformation.pointsPerKill).ToString();
				this.activeEntries[num].deaths.text = this.rt_ScoreboardEntries[n].deaths.ToString();
				this.activeEntries[num].assists.text = this.rt_ScoreboardEntries[n].assists.ToString();
				this.activeEntries[num].Background.color = (this.rt_ScoreboardEntries[n].isAlive ? this.activeEntries[num].LifeColor : this.activeEntries[num].DeadColor);
				this.activeEntries[num].BombImage.enabled = this.rt_ScoreboardEntries[n].isBomb;
				this.activeEntries[num].gameObject.SetActiveOptimized(true);
				num++;
			}
		}
		if (this.activeEntries.Count > num)
		{
			for (int num2 = num; num2 < this.activeEntries.Count; num2++)
			{
				this.activeEntries[num2].gameObject.SetActiveOptimized(false);
			}
		}
	}

	// Token: 0x060005FB RID: 1531 RVA: 0x0001C0C8 File Offset: 0x0001A2C8
	public void Redraw(List<Kit_ScoreboardHelper> entries)
	{
		Debug.Log("Redraw with entries");
		this.lastRedraw = Time.time + this.redrawFrequency;
		if (this.main.currentGameModeBehaviour.isTeamGameMode)
		{
			if (this.teamActive.Count == 0)
			{
				for (int i = 0; i < Mathf.Clamp(this.main.gameInformation.allPvpTeams.Length, 0, this.main.currentGameModeBehaviour.maximumAmountOfTeams); i++)
				{
					GameObject gameObject = UnityEngine.Object.Instantiate<GameObject>(this.teamPrefab, this.teamGo, false);
					Color teamColor = this.main.gameInformation.allPvpTeams[i].teamColor;
					gameObject.GetComponentInChildren<Image>().color = new Color(teamColor.r, teamColor.g, teamColor.b, 0.5f);
					this.teamActive.Add(gameObject.transform as RectTransform);
					this._teamViews.Add(gameObject.GetComponent<TeamScoreboardView>());
				}
			}
			ITeamGameMode teamGameMode = (ITeamGameMode)this.main.currentGameModeRuntimeData;
			for (int j = 0; j < this._teamViews.Count; j++)
			{
				this._teamViews[j].ScoreText.text = teamGameMode.TeamPoints[j].ToString();
			}
		}
		else if (this.teamActive.Count == 0)
		{
			GameObject gameObject2 = UnityEngine.Object.Instantiate<GameObject>(this.teamPrefab, this.teamGo, false);
			this.teamActive.Add(gameObject2.transform as RectTransform);
			this._teamViews.Add(gameObject2.GetComponent<TeamScoreboardView>());
			this._teamViews[0].ScoreText.enabled = false;
		}
		int num = 0;
		for (int k = 0; k < entries.Count; k++)
		{
			if (entries[k].used && entries[k].team >= 0)
			{
				while (this.activeEntries.Count < num + 1)
				{
					Kit_ScoreboardUIEntry component = UnityEngine.Object.Instantiate<GameObject>(this.entryPrefab, this._teamViews[0].EntriesGo, false).GetComponent<Kit_ScoreboardUIEntry>();
					this.activeEntries.Add(component);
				}
				if (this.main.currentGameModeBehaviour.isTeamGameMode)
				{
					this.activeEntries[num].transform.SetParent(this._teamViews[entries[k].team].EntriesGo);
				}
				else
				{
					this.activeEntries[num].transform.SetParent(this._teamViews[0].EntriesGo);
				}
				this.activeEntries[num].nameText.text = entries[k].name;
				this.activeEntries[num].kills.text = entries[k].kills.ToString();
				this.activeEntries[num].ping.text = entries[k].ping.ToString();
				this.activeEntries[num].score.text = (entries[k].kills * this.main.gameInformation.pointsPerKill).ToString();
				this.activeEntries[num].deaths.text = entries[k].deaths.ToString();
				this.activeEntries[num].assists.text = entries[k].assists.ToString();
				this.activeEntries[num].Background.color = (entries[k].isAlive ? this.activeEntries[num].LifeColor : this.activeEntries[num].DeadColor);
				this.activeEntries[num].BombImage.enabled = entries[k].isBomb;
				this.activeEntries[num].gameObject.SetActiveOptimized(true);
				num++;
			}
		}
		if (this.activeEntries.Count > num)
		{
			for (int l = num; l < this.activeEntries.Count; l++)
			{
				this.activeEntries[l].gameObject.SetActiveOptimized(false);
			}
		}
	}

	// Token: 0x060005FC RID: 1532 RVA: 0x0001C540 File Offset: 0x0001A740
	public bool IsPlayerByNameInActive(string name)
	{
		using (List<Kit_PlayerBehaviour>.Enumerator enumerator = this.main.allActivePlayers.GetEnumerator())
		{
			while (enumerator.MoveNext())
			{
				if (enumerator.Current.name == name)
				{
					return true;
				}
			}
		}
		return false;
	}

	// Token: 0x060005FD RID: 1533 RVA: 0x0001C5A4 File Offset: 0x0001A7A4
	public bool IsPlayerByIDActive(int id)
	{
		foreach (Kit_PlayerBehaviour kit_PlayerBehaviour in this.main.allActivePlayers)
		{
			if (kit_PlayerBehaviour.id == id && !kit_PlayerBehaviour.isBot)
			{
				VitalsRuntimeData vitalsRuntimeData = (VitalsRuntimeData)kit_PlayerBehaviour.customVitalsData;
				if (vitalsRuntimeData != null && vitalsRuntimeData.hitPoints > 0f)
				{
					return true;
				}
			}
		}
		return false;
	}

	// Token: 0x060005FE RID: 1534 RVA: 0x0001C62C File Offset: 0x0001A82C
	public bool IsPlayerByIDHasBomb(int id)
	{
		if (!this.IsOurPlayerCanSeeBomb())
		{
			return false;
		}
		foreach (Kit_PlayerBehaviour kit_PlayerBehaviour in this.main.allActivePlayers)
		{
			if (kit_PlayerBehaviour.id == id && !kit_PlayerBehaviour.isBot)
			{
				return kit_PlayerBehaviour.IsBomb;
			}
		}
		return false;
	}

	// Token: 0x060005FF RID: 1535 RVA: 0x0001C6A4 File Offset: 0x0001A8A4
	private bool IsOurPlayerCanSeeBomb()
	{
		if (this.main.currentGameModeBehaviour is PvPGMBPlantingBomb)
		{
			PvPGMBPlantingBomb pvPGMBPlantingBomb = (PvPGMBPlantingBomb)this.main.currentGameModeBehaviour;
			return this.main.myPlayer && this.main.myPlayer.myTeam == pvPGMBPlantingBomb.TerroristTeam;
		}
		return false;
	}

	// Token: 0x04000527 RID: 1319
	public CanvasGroup _parentyScreen;

	// Token: 0x04000528 RID: 1320
	public bool _update;

	// Token: 0x04000529 RID: 1321
	public Kit_IngameMain main;

	// Token: 0x0400052A RID: 1322
	public GameObject scoreboardRoot;

	// Token: 0x0400052B RID: 1323
	public GameObject teamPrefab;

	// Token: 0x0400052C RID: 1324
	public RectTransform teamGo;

	// Token: 0x0400052D RID: 1325
	public List<RectTransform> teamActive = new List<RectTransform>();

	// Token: 0x0400052E RID: 1326
	public GameObject entryPrefab;

	// Token: 0x0400052F RID: 1327
	public List<Kit_ScoreboardUIEntry> activeEntries = new List<Kit_ScoreboardUIEntry>();

	// Token: 0x04000530 RID: 1328
	[Header("Settings")]
	public float redrawFrequency = 1f;

	// Token: 0x04000531 RID: 1329
	private float lastRedraw;

	// Token: 0x04000532 RID: 1330
	public bool canUseScoreboard;

	// Token: 0x04000533 RID: 1331
	public List<Kit_ScoreboardHelper> rt_ScoreboardEntries = new List<Kit_ScoreboardHelper>();

	// Token: 0x04000534 RID: 1332
	private List<TeamScoreboardView> _teamViews = new List<TeamScoreboardView>();
}
﻿using System;
using System.IO;
using UnityEngine;

// Token: 0x020000B9 RID: 185
public class ScreenShoot : MonoBehaviour
{
	// Token: 0x0600048B RID: 1163 RVA: 0x000153F4 File Offset: 0x000135F4
	private void OnEnable()
	{
		this._camera = base.GetComponent<Camera>();
		Camera.SetupCurrent(this._camera);
	}

	// Token: 0x0600048C RID: 1164 RVA: 0x0001540D File Offset: 0x0001360D
	private void Update()
	{
		if (Input.GetKeyDown(KeyCode.K))
		{
			Debug.Log("Screen Shoot!");
			ScreenCapture.CaptureScreenshot(Path.Combine(Application.dataPath, "Sprites", "ScreenSHoot.png"));
		}
	}

	// Token: 0x0400037B RID: 891
	private Camera _camera;
}
﻿using System;
using System.Collections.Generic;
using System.Runtime.Serialization;

// Token: 0x02000006 RID: 6
public class SerializableDictionary<TKey, TValue> : SerializableDictionaryBase<TKey, TValue, TValue>
{
	// Token: 0x06000033 RID: 51 RVA: 0x00002C9D File Offset: 0x00000E9D
	public SerializableDictionary()
	{
	}

	// Token: 0x06000034 RID: 52 RVA: 0x00002CA5 File Offset: 0x00000EA5
	public SerializableDictionary(IDictionary<TKey, TValue> dict) : base(dict)
	{
	}

	// Token: 0x06000035 RID: 53 RVA: 0x00002CAE File Offset: 0x00000EAE
	protected SerializableDictionary(SerializationInfo info, StreamingContext context) : base(info, context)
	{
	}

	// Token: 0x06000036 RID: 54 RVA: 0x00002CB8 File Offset: 0x00000EB8
	protected override TValue GetValue(TValue[] storage, int i)
	{
		return storage[i];
	}

	// Token: 0x06000037 RID: 55 RVA: 0x00002CC1 File Offset: 0x00000EC1
	protected override void SetValue(TValue[] storage, int i, TValue value)
	{
		storage[i] = value;
	}
}
﻿using System;
using System.Collections.Generic;
using System.Runtime.Serialization;

// Token: 0x02000007 RID: 7
public class SerializableDictionary<TKey, TValue, TValueStorage> : SerializableDictionaryBase<TKey, TValue, TValueStorage> where TValueStorage : SerializableDictionary.Storage<TValue>, new()
{
	// Token: 0x06000038 RID: 56 RVA: 0x00002CCB File Offset: 0x00000ECB
	public SerializableDictionary()
	{
	}

	// Token: 0x06000039 RID: 57 RVA: 0x00002CD3 File Offset: 0x00000ED3
	public SerializableDictionary(IDictionary<TKey, TValue> dict) : base(dict)
	{
	}

	// Token: 0x0600003A RID: 58 RVA: 0x00002CDC File Offset: 0x00000EDC
	protected SerializableDictionary(SerializationInfo info, StreamingContext context) : base(info, context)
	{
	}

	// Token: 0x0600003B RID: 59 RVA: 0x00002CE6 File Offset: 0x00000EE6
	protected override TValue GetValue(TValueStorage[] storage, int i)
	{
		return storage[i].data;
	}

	// Token: 0x0600003C RID: 60 RVA: 0x00002CF9 File Offset: 0x00000EF9
	protected override void SetValue(TValueStorage[] storage, int i, TValue value)
	{
		storage[i] = Activator.CreateInstance<TValueStorage>();
		storage[i].data = value;
	}
}
﻿using System;

// Token: 0x02000005 RID: 5
public static class SerializableDictionary
{
	// Token: 0x02000469 RID: 1129
	public class Storage<T> : SerializableDictionaryBase.Storage
	{
		// Token: 0x04001B5B RID: 7003
		public T data;
	}
}
﻿using System;
using System.Collections;
using System.Collections.Generic;
using System.Runtime.Serialization;
using UnityEngine;

// Token: 0x02000004 RID: 4
[Serializable]
public abstract class SerializableDictionaryBase<TKey, TValue, TValueStorage> : SerializableDictionaryBase, IDictionary<TKey, TValue>, ICollection<KeyValuePair<TKey, TValue>>, IEnumerable<KeyValuePair<TKey, TValue>>, IEnumerable, IDictionary, ICollection, ISerializationCallbackReceiver, IDeserializationCallback, ISerializable
{
	// Token: 0x0600000C RID: 12 RVA: 0x00002932 File Offset: 0x00000B32
	public SerializableDictionaryBase()
	{
		this.m_dict = new SerializableDictionaryBase.Dictionary<TKey, TValue>();
	}

	// Token: 0x0600000D RID: 13 RVA: 0x00002945 File Offset: 0x00000B45
	public SerializableDictionaryBase(IDictionary<TKey, TValue> dict)
	{
		this.m_dict = new SerializableDictionaryBase.Dictionary<TKey, TValue>(dict);
	}

	// Token: 0x0600000E RID: 14
	protected abstract void SetValue(TValueStorage[] storage, int i, TValue value);

	// Token: 0x0600000F RID: 15
	protected abstract TValue GetValue(TValueStorage[] storage, int i);

	// Token: 0x06000010 RID: 16 RVA: 0x0000295C File Offset: 0x00000B5C
	public void CopyFrom(IDictionary<TKey, TValue> dict)
	{
		this.m_dict.Clear();
		foreach (KeyValuePair<TKey, TValue> keyValuePair in dict)
		{
			this.m_dict[keyValuePair.Key] = keyValuePair.Value;
		}
	}

	// Token: 0x06000011 RID: 17 RVA: 0x000029C4 File Offset: 0x00000BC4
	public void OnAfterDeserialize()
	{
		if (this.m_keys != null && this.m_values != null && this.m_keys.Length == this.m_values.Length)
		{
			this.m_dict.Clear();
			int num = this.m_keys.Length;
			for (int i = 0; i < num; i++)
			{
				this.m_dict[this.m_keys[i]] = this.GetValue(this.m_values, i);
			}
			this.m_keys = null;
			this.m_values = null;
		}
	}

	// Token: 0x06000012 RID: 18 RVA: 0x00002A48 File Offset: 0x00000C48
	public void OnBeforeSerialize()
	{
		int count = this.m_dict.Count;
		this.m_keys = new TKey[count];
		this.m_values = new TValueStorage[count];
		int num = 0;
		foreach (KeyValuePair<TKey, TValue> keyValuePair in this.m_dict)
		{
			this.m_keys[num] = keyValuePair.Key;
			this.SetValue(this.m_values, num, keyValuePair.Value);
			num++;
		}
	}

	// Token: 0x17000001 RID: 1
	// (get) Token: 0x06000013 RID: 19 RVA: 0x00002AE8 File Offset: 0x00000CE8
	public ICollection<TKey> Keys
	{
		get
		{
			return ((IDictionary<TKey, TValue>)this.m_dict).Keys;
		}
	}

	// Token: 0x17000002 RID: 2
	// (get) Token: 0x06000014 RID: 20 RVA: 0x00002AF5 File Offset: 0x00000CF5
	public ICollection<TValue> Values
	{
		get
		{
			return ((IDictionary<TKey, TValue>)this.m_dict).Values;
		}
	}

	// Token: 0x17000003 RID: 3
	// (get) Token: 0x06000015 RID: 21 RVA: 0x00002B02 File Offset: 0x00000D02
	public int Count
	{
		get
		{
			return ((ICollection<KeyValuePair<TKey, TValue>>)this.m_dict).Count;
		}
	}

	// Token: 0x17000004 RID: 4
	// (get) Token: 0x06000016 RID: 22 RVA: 0x00002B0F File Offset: 0x00000D0F
	public bool IsReadOnly
	{
		get
		{
			return ((ICollection<KeyValuePair<TKey, TValue>>)this.m_dict).IsReadOnly;
		}
	}

	// Token: 0x17000005 RID: 5
	public TValue this[TKey key]
	{
		get
		{
			return ((IDictionary<TKey, TValue>)this.m_dict)[key];
		}
		set
		{
			((IDictionary<TKey, TValue>)this.m_dict)[key] = value;
		}
	}

	// Token: 0x06000019 RID: 25 RVA: 0x00002B39 File Offset: 0x00000D39
	public void Add(TKey key, TValue value)
	{
		((IDictionary<TKey, TValue>)this.m_dict).Add(key, value);
	}

	// Token: 0x0600001A RID: 26 RVA: 0x00002B48 File Offset: 0x00000D48
	public bool ContainsKey(TKey key)
	{
		return ((IDictionary<TKey, TValue>)this.m_dict).ContainsKey(key);
	}

	// Token: 0x0600001B RID: 27 RVA: 0x00002B56 File Offset: 0x00000D56
	public bool Remove(TKey key)
	{
		return ((IDictionary<TKey, TValue>)this.m_dict).Remove(key);
	}

	// Token: 0x0600001C RID: 28 RVA: 0x00002B64 File Offset: 0x00000D64
	public bool TryGetValue(TKey key, out TValue value)
	{
		return ((IDictionary<TKey, TValue>)this.m_dict).TryGetValue(key, out value);
	}

	// Token: 0x0600001D RID: 29 RVA: 0x00002B73 File Offset: 0x00000D73
	public void Add(KeyValuePair<TKey, TValue> item)
	{
		((ICollection<KeyValuePair<TKey, TValue>>)this.m_dict).Add(item);
	}

	// Token: 0x0600001E RID: 30 RVA: 0x00002B81 File Offset: 0x00000D81
	public void Clear()
	{
		((ICollection<KeyValuePair<TKey, TValue>>)this.m_dict).Clear();
	}

	// Token: 0x0600001F RID: 31 RVA: 0x00002B8E File Offset: 0x00000D8E
	public bool Contains(KeyValuePair<TKey, TValue> item)
	{
		return ((ICollection<KeyValuePair<TKey, TValue>>)this.m_dict).Contains(item);
	}

	// Token: 0x06000020 RID: 32 RVA: 0x00002B9C File Offset: 0x00000D9C
	public void CopyTo(KeyValuePair<TKey, TValue>[] array, int arrayIndex)
	{
		((ICollection<KeyValuePair<TKey, TValue>>)this.m_dict).CopyTo(array, arrayIndex);
	}

	// Token: 0x06000021 RID: 33 RVA: 0x00002BAB File Offset: 0x00000DAB
	public bool Remove(KeyValuePair<TKey, TValue> item)
	{
		return ((ICollection<KeyValuePair<TKey, TValue>>)this.m_dict).Remove(item);
	}

	// Token: 0x06000022 RID: 34 RVA: 0x00002BB9 File Offset: 0x00000DB9
	public IEnumerator<KeyValuePair<TKey, TValue>> GetEnumerator()
	{
		return ((IEnumerable<KeyValuePair<TKey, TValue>>)this.m_dict).GetEnumerator();
	}

	// Token: 0x06000023 RID: 35 RVA: 0x00002BB9 File Offset: 0x00000DB9
	IEnumerator IEnumerable.GetEnumerator()
	{
		return ((IEnumerable<KeyValuePair<TKey, TValue>>)this.m_dict).GetEnumerator();
	}

	// Token: 0x17000006 RID: 6
	// (get) Token: 0x06000024 RID: 36 RVA: 0x00002BC6 File Offset: 0x00000DC6
	public bool IsFixedSize
	{
		get
		{
			return ((IDictionary)this.m_dict).IsFixedSize;
		}
	}

	// Token: 0x17000007 RID: 7
	// (get) Token: 0x06000025 RID: 37 RVA: 0x00002BD3 File Offset: 0x00000DD3
	ICollection IDictionary.Keys
	{
		get
		{
			return ((IDictionary)this.m_dict).Keys;
		}
	}

	// Token: 0x17000008 RID: 8
	// (get) Token: 0x06000026 RID: 38 RVA: 0x00002BE0 File Offset: 0x00000DE0
	ICollection IDictionary.Values
	{
		get
		{
			return ((IDictionary)this.m_dict).Values;
		}
	}

	// Token: 0x17000009 RID: 9
	// (get) Token: 0x06000027 RID: 39 RVA: 0x00002BED File Offset: 0x00000DED
	public bool IsSynchronized
	{
		get
		{
			return ((ICollection)this.m_dict).IsSynchronized;
		}
	}

	// Token: 0x1700000A RID: 10
	// (get) Token: 0x06000028 RID: 40 RVA: 0x00002BFA File Offset: 0x00000DFA
	public object SyncRoot
	{
		get
		{
			return ((ICollection)this.m_dict).SyncRoot;
		}
	}

	// Token: 0x1700000B RID: 11
	public object this[object key]
	{
		get
		{
			return ((IDictionary)this.m_dict)[key];
		}
		set
		{
			((IDictionary)this.m_dict)[key] = value;
		}
	}

	// Token: 0x0600002B RID: 43 RVA: 0x00002C24 File Offset: 0x00000E24
	public void Add(object key, object value)
	{
		((IDictionary)this.m_dict).Add(key, value);
	}

	// Token: 0x0600002C RID: 44 RVA: 0x00002C33 File Offset: 0x00000E33
	public bool Contains(object key)
	{
		return ((IDictionary)this.m_dict).Contains(key);
	}

	// Token: 0x0600002D RID: 45 RVA: 0x00002C41 File Offset: 0x00000E41
	IDictionaryEnumerator IDictionary.GetEnumerator()
	{
		return ((IDictionary)this.m_dict).GetEnumerator();
	}

	// Token: 0x0600002E RID: 46 RVA: 0x00002C4E File Offset: 0x00000E4E
	public void Remove(object key)
	{
		((IDictionary)this.m_dict).Remove(key);
	}

	// Token: 0x0600002F RID: 47 RVA: 0x00002C5C File Offset: 0x00000E5C
	public void CopyTo(Array array, int index)
	{
		((ICollection)this.m_dict).CopyTo(array, index);
	}

	// Token: 0x06000030 RID: 48 RVA: 0x00002C6B File Offset: 0x00000E6B
	public void OnDeserialization(object sender)
	{
		((IDeserializationCallback)this.m_dict).OnDeserialization(sender);
	}

	// Token: 0x06000031 RID: 49 RVA: 0x00002C79 File Offset: 0x00000E79
	protected SerializableDictionaryBase(SerializationInfo info, StreamingContext context)
	{
		this.m_dict = new SerializableDictionaryBase.Dictionary<TKey, TValue>(info, context);
	}

	// Token: 0x06000032 RID: 50 RVA: 0x00002C8E File Offset: 0x00000E8E
	public void GetObjectData(SerializationInfo info, StreamingContext context)
	{
		((ISerializable)this.m_dict).GetObjectData(info, context);
	}

	// Token: 0x0400000E RID: 14
	private SerializableDictionaryBase.Dictionary<TKey, TValue> m_dict;

	// Token: 0x0400000F RID: 15
	[SerializeField]
	private TKey[] m_keys;

	// Token: 0x04000010 RID: 16
	[SerializeField]
	private TValueStorage[] m_values;
}
﻿using System;
using System.Collections.Generic;
using System.Runtime.Serialization;

// Token: 0x02000003 RID: 3
public abstract class SerializableDictionaryBase
{
	// Token: 0x02000467 RID: 1127
	public abstract class Storage
	{
	}

	// Token: 0x02000468 RID: 1128
	protected class Dictionary<TKey, TValue> : System.Collections.Generic.Dictionary<TKey, TValue>
	{
		// Token: 0x06001BFF RID: 7167 RVA: 0x000784DC File Offset: 0x000766DC
		public Dictionary()
		{
		}

		// Token: 0x06001C00 RID: 7168 RVA: 0x000784E4 File Offset: 0x000766E4
		public Dictionary(IDictionary<TKey, TValue> dict) : base(dict)
		{
		}

		// Token: 0x06001C01 RID: 7169 RVA: 0x000784ED File Offset: 0x000766ED
		public Dictionary(SerializationInfo info, StreamingContext context) : base(info, context)
		{
		}
	}
}
﻿using System;
using System.Collections;
using System.Collections.Generic;
using System.Runtime.Serialization;
using UnityEngine;

// Token: 0x02000009 RID: 9
[Serializable]
public abstract class SerializableHashSet<T> : SerializableHashSetBase, ISet<T>, ICollection<T>, IEnumerable<T>, IEnumerable, ISerializationCallbackReceiver, IDeserializationCallback, ISerializable
{
	// Token: 0x0600003E RID: 62 RVA: 0x00002D19 File Offset: 0x00000F19
	public SerializableHashSet()
	{
		this.m_hashSet = new SerializableHashSetBase.HashSet<T>();
	}

	// Token: 0x0600003F RID: 63 RVA: 0x00002D2C File Offset: 0x00000F2C
	public SerializableHashSet(ISet<T> set)
	{
		this.m_hashSet = new SerializableHashSetBase.HashSet<T>(set);
	}

	// Token: 0x06000040 RID: 64 RVA: 0x00002D40 File Offset: 0x00000F40
	public void CopyFrom(ISet<T> set)
	{
		this.m_hashSet.Clear();
		foreach (T item in set)
		{
			this.m_hashSet.Add(item);
		}
	}

	// Token: 0x06000041 RID: 65 RVA: 0x00002D9C File Offset: 0x00000F9C
	public void OnAfterDeserialize()
	{
		if (this.m_keys != null)
		{
			this.m_hashSet.Clear();
			int num = this.m_keys.Length;
			for (int i = 0; i < num; i++)
			{
				this.m_hashSet.Add(this.m_keys[i]);
			}
			this.m_keys = null;
		}
	}

	// Token: 0x06000042 RID: 66 RVA: 0x00002DF0 File Offset: 0x00000FF0
	public void OnBeforeSerialize()
	{
		int count = this.m_hashSet.Count;
		this.m_keys = new T[count];
		int num = 0;
		foreach (T t in this.m_hashSet)
		{
			this.m_keys[num] = t;
			num++;
		}
	}

	// Token: 0x1700000C RID: 12
	// (get) Token: 0x06000043 RID: 67 RVA: 0x00002E68 File Offset: 0x00001068
	public int Count
	{
		get
		{
			return ((ICollection<T>)this.m_hashSet).Count;
		}
	}

	// Token: 0x1700000D RID: 13
	// (get) Token: 0x06000044 RID: 68 RVA: 0x00002E75 File Offset: 0x00001075
	public bool IsReadOnly
	{
		get
		{
			return ((ICollection<T>)this.m_hashSet).IsReadOnly;
		}
	}

	// Token: 0x06000045 RID: 69 RVA: 0x00002E82 File Offset: 0x00001082
	public bool Add(T item)
	{
		return ((ISet<T>)this.m_hashSet).Add(item);
	}

	// Token: 0x06000046 RID: 70 RVA: 0x00002E90 File Offset: 0x00001090
	public void ExceptWith(IEnumerable<T> other)
	{
		((ISet<T>)this.m_hashSet).ExceptWith(other);
	}

	// Token: 0x06000047 RID: 71 RVA: 0x00002E9E File Offset: 0x0000109E
	public void IntersectWith(IEnumerable<T> other)
	{
		((ISet<T>)this.m_hashSet).IntersectWith(other);
	}

	// Token: 0x06000048 RID: 72 RVA: 0x00002EAC File Offset: 0x000010AC
	public bool IsProperSubsetOf(IEnumerable<T> other)
	{
		return ((ISet<T>)this.m_hashSet).IsProperSubsetOf(other);
	}

	// Token: 0x06000049 RID: 73 RVA: 0x00002EBA File Offset: 0x000010BA
	public bool IsProperSupersetOf(IEnumerable<T> other)
	{
		return ((ISet<T>)this.m_hashSet).IsProperSupersetOf(other);
	}

	// Token: 0x0600004A RID: 74 RVA: 0x00002EC8 File Offset: 0x000010C8
	public bool IsSubsetOf(IEnumerable<T> other)
	{
		return ((ISet<T>)this.m_hashSet).IsSubsetOf(other);
	}

	// Token: 0x0600004B RID: 75 RVA: 0x00002ED6 File Offset: 0x000010D6
	public bool IsSupersetOf(IEnumerable<T> other)
	{
		return ((ISet<T>)this.m_hashSet).IsSupersetOf(other);
	}

	// Token: 0x0600004C RID: 76 RVA: 0x00002EE4 File Offset: 0x000010E4
	public bool Overlaps(IEnumerable<T> other)
	{
		return ((ISet<T>)this.m_hashSet).Overlaps(other);
	}

	// Token: 0x0600004D RID: 77 RVA: 0x00002EF2 File Offset: 0x000010F2
	public bool SetEquals(IEnumerable<T> other)
	{
		return ((ISet<T>)this.m_hashSet).SetEquals(other);
	}

	// Token: 0x0600004E RID: 78 RVA: 0x00002F00 File Offset: 0x00001100
	public void SymmetricExceptWith(IEnumerable<T> other)
	{
		((ISet<T>)this.m_hashSet).SymmetricExceptWith(other);
	}

	// Token: 0x0600004F RID: 79 RVA: 0x00002F0E File Offset: 0x0000110E
	public void UnionWith(IEnumerable<T> other)
	{
		((ISet<T>)this.m_hashSet).UnionWith(other);
	}

	// Token: 0x06000050 RID: 80 RVA: 0x00002F1C File Offset: 0x0000111C
	void ICollection<!0>.Add(T item)
	{
		((ISet<T>)this.m_hashSet).Add(item);
	}

	// Token: 0x06000051 RID: 81 RVA: 0x00002F2B File Offset: 0x0000112B
	public void Clear()
	{
		((ICollection<!0>)this.m_hashSet).Clear();
	}

	// Token: 0x06000052 RID: 82 RVA: 0x00002F38 File Offset: 0x00001138
	public bool Contains(T item)
	{
		return ((ICollection<!0>)this.m_hashSet).Contains(item);
	}

	// Token: 0x06000053 RID: 83 RVA: 0x00002F46 File Offset: 0x00001146
	public void CopyTo(T[] array, int arrayIndex)
	{
		((ICollection<!0>)this.m_hashSet).CopyTo(array, arrayIndex);
	}

	// Token: 0x06000054 RID: 84 RVA: 0x00002F55 File Offset: 0x00001155
	public bool Remove(T item)
	{
		return ((ICollection<!0>)this.m_hashSet).Remove(item);
	}

	// Token: 0x06000055 RID: 85 RVA: 0x00002F63 File Offset: 0x00001163
	public IEnumerator<T> GetEnumerator()
	{
		return ((IEnumerable<T>)this.m_hashSet).GetEnumerator();
	}

	// Token: 0x06000056 RID: 86 RVA: 0x00002F63 File Offset: 0x00001163
	IEnumerator IEnumerable.GetEnumerator()
	{
		return ((IEnumerable<T>)this.m_hashSet).GetEnumerator();
	}

	// Token: 0x06000057 RID: 87 RVA: 0x00002F70 File Offset: 0x00001170
	public void OnDeserialization(object sender)
	{
		((IDeserializationCallback)this.m_hashSet).OnDeserialization(sender);
	}

	// Token: 0x06000058 RID: 88 RVA: 0x00002F7E File Offset: 0x0000117E
	protected SerializableHashSet(SerializationInfo info, StreamingContext context)
	{
		this.m_hashSet = new SerializableHashSetBase.HashSet<T>(info, context);
	}

	// Token: 0x06000059 RID: 89 RVA: 0x00002F93 File Offset: 0x00001193
	public void GetObjectData(SerializationInfo info, StreamingContext context)
	{
		((ISerializable)this.m_hashSet).GetObjectData(info, context);
	}

	// Token: 0x04000011 RID: 17
	private SerializableHashSetBase.HashSet<T> m_hashSet;

	// Token: 0x04000012 RID: 18
	[SerializeField]
	private T[] m_keys;
}
﻿using System;
using System.Collections.Generic;
using System.Runtime.Serialization;

// Token: 0x02000008 RID: 8
public abstract class SerializableHashSetBase
{
	// Token: 0x0200046A RID: 1130
	public abstract class Storage
	{
	}

	// Token: 0x0200046B RID: 1131
	protected class HashSet<TValue> : System.Collections.Generic.HashSet<TValue>
	{
		// Token: 0x06001C04 RID: 7172 RVA: 0x000784FF File Offset: 0x000766FF
		public HashSet()
		{
		}

		// Token: 0x06001C05 RID: 7173 RVA: 0x00078507 File Offset: 0x00076707
		public HashSet(ISet<TValue> set) : base(set)
		{
		}

		// Token: 0x06001C06 RID: 7174 RVA: 0x00078510 File Offset: 0x00076710
		public HashSet(SerializationInfo info, StreamingContext context) : base(info, context)
		{
		}
	}
}
﻿using System;
using MarsFPSKit;
using Photon.Pun;
using Photon.Realtime;
using TMPro;
using UnityEngine;

// Token: 0x020000EE RID: 238
public class ServerBrowserEntry : MonoBehaviour
{
	// Token: 0x06000602 RID: 1538 RVA: 0x0001C744 File Offset: 0x0001A944
	public void Setup(ServerBrowserScreenView curMsb, RoomInfo curRoom, Kit_GameInformation game)
	{
		this.msb = curMsb;
		this.myRoom = curRoom;
		if (this.myRoom != null)
		{
			this.serverName.text = this.myRoom.Name;
			int num = (int)this.myRoom.CustomProperties["gameMode"];
			this.gameModeName.text = LocalizationController.Instance.GetText("GameModes", game.allPvpGameModes[num].gameModeName);
			this.mapName.text = game.allPvpGameModes[num].traditionalMaps[(int)this.myRoom.CustomProperties["map"]].mapName;
			if ((bool)this.myRoom.CustomProperties["bots"])
			{
				this.players.text = string.Format("{0}/{1} {2}", this.myRoom.PlayerCount, this.myRoom.MaxPlayers, LocalizationController.Instance.GetText("HostGameData", "Bots"));
			}
			else
			{
				this.players.text = this.myRoom.PlayerCount + "/" + this.myRoom.MaxPlayers;
			}
			this.ping.text = PhotonNetwork.GetPing().ToString();
			if (this.myRoom.CustomProperties["password"] != null && ((string)this.myRoom.CustomProperties["password"]).Length > 0)
			{
				this.password.text = LocalizationController.Instance.GetText("DefaultValues", "Yes");
			}
			else
			{
				this.password.text = LocalizationController.Instance.GetText("DefaultValues", "No");
			}
			this.backText.text = LocalizationController.Instance.GetText("DefaultValues", "Join");
		}
		base.transform.localScale = Vector3.one;
	}

	// Token: 0x06000603 RID: 1539 RVA: 0x0001C956 File Offset: 0x0001AB56
	public void OnClick()
	{
		if (this.msb && this.myRoom != null)
		{
			this.msb.JoinRoom(this.myRoom);
		}
	}

	// Token: 0x04000537 RID: 1335
	[SerializeField]
	private TextMeshProUGUI serverName;

	// Token: 0x04000538 RID: 1336
	[SerializeField]
	private TextMeshProUGUI mapName;

	// Token: 0x04000539 RID: 1337
	[SerializeField]
	private TextMeshProUGUI gameModeName;

	// Token: 0x0400053A RID: 1338
	[SerializeField]
	private TextMeshProUGUI players;

	// Token: 0x0400053B RID: 1339
	[SerializeField]
	private TextMeshProUGUI ping;

	// Token: 0x0400053C RID: 1340
	[SerializeField]
	private TextMeshProUGUI password;

	// Token: 0x0400053D RID: 1341
	[SerializeField]
	private TextMeshProUGUI backText;

	// Token: 0x0400053E RID: 1342
	private ServerBrowserScreenView msb;

	// Token: 0x0400053F RID: 1343
	private RoomInfo myRoom;
}
﻿using System;
using System.Collections.Generic;
using Photon.Pun;
using Photon.Realtime;

// Token: 0x020000EF RID: 239
public class ServerBrowserPhotonPartVIew : MonoBehaviourPunCallbacks
{
	// Token: 0x1400000F RID: 15
	// (add) Token: 0x06000605 RID: 1541 RVA: 0x0001C980 File Offset: 0x0001AB80
	// (remove) Token: 0x06000606 RID: 1542 RVA: 0x0001C9B8 File Offset: 0x0001ABB8
	public event Action<DisconnectCause> OnDisconnectedEvent = delegate(DisconnectCause <p0>)
	{
	};

	// Token: 0x14000010 RID: 16
	// (add) Token: 0x06000607 RID: 1543 RVA: 0x0001C9F0 File Offset: 0x0001ABF0
	// (remove) Token: 0x06000608 RID: 1544 RVA: 0x0001CA28 File Offset: 0x0001AC28
	public event Action<List<RoomInfo>> OnRoomListUpdateEvent = delegate(List<RoomInfo> <p0>)
	{
	};

	// Token: 0x06000609 RID: 1545 RVA: 0x0001CA5D File Offset: 0x0001AC5D
	public override void OnDisconnected(DisconnectCause cause)
	{
		this.OnDisconnectedEvent(cause);
	}

	// Token: 0x0600060A RID: 1546 RVA: 0x0001CA6B File Offset: 0x0001AC6B
	public override void OnRoomListUpdate(List<RoomInfo> roomList)
	{
		this.OnRoomListUpdateEvent(roomList);
	}
}
﻿using System;
using System.Collections.Generic;
using ExitGames.Client.Photon;
using MarsFPSKit;
using Photon.Pun;
using Photon.Realtime;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000F0 RID: 240
public class ServerBrowserScreenView : BaseMenuView
{
	// Token: 0x0600060C RID: 1548 RVA: 0x0001CADC File Offset: 0x0001ACDC
	private void OnEnable()
	{
		this._backButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(this._previousMenu);
		});
		this._photonCallbacks.OnDisconnectedEvent += this.OnDisconnected;
		this._photonCallbacks.OnRoomListUpdateEvent += this.OnRoomListUpdate;
		this._regionDropdown.onValueChanged.AddListener(delegate(int index)
		{
			RegionController.Instance.ChangeRegion(index);
		});
		RegionController.Instance.OnRegionChanged += this.SetRegionDropdown;
	}

	// Token: 0x0600060D RID: 1549 RVA: 0x0001CB78 File Offset: 0x0001AD78
	private void OnDisable()
	{
		this._backButton.onClick.RemoveAllListeners();
		this._photonCallbacks.OnDisconnectedEvent -= this.OnDisconnected;
		this._photonCallbacks.OnRoomListUpdateEvent -= this.OnRoomListUpdate;
		this._regionDropdown.onValueChanged.RemoveAllListeners();
		RegionController.Instance.OnRegionChanged -= this.SetRegionDropdown;
	}

	// Token: 0x0600060E RID: 1550 RVA: 0x0001CBE9 File Offset: 0x0001ADE9
	public override void MenuInit(ISwitchableMenu menuController)
	{
		base.MenuInit(menuController);
		this._game = Resources.Load<Kit_GameInformation>("Game");
		this.InitRegionDropdown();
	}

	// Token: 0x0600060F RID: 1551 RVA: 0x0001CC08 File Offset: 0x0001AE08
	public void JoinRoom(RoomInfo room)
	{
		if (PhotonNetwork.InRoom)
		{
			if (PhotonNetwork.IsMasterClient)
			{
				PhotonNetwork.RaiseEvent(Kit_EventIDs.changeRoomEvent, room.Name, new RaiseEventOptions
				{
					Receivers = ReceiverGroup.All
				}, SendOptions.SendReliable);
			}
			return;
		}
		if (((string)room.CustomProperties["password"]).Length <= 0)
		{
			if (PhotonNetwork.JoinRoom(room.Name, null))
			{
				SteamFriendsController.Instance.OnMasterClientJoinRoom(room.Name);
				return;
			}
		}
		else
		{
			this.passwordRoom = room;
			this.passwordInput.text = "";
			this.passwordUi.SetActive(true);
		}
	}

	// Token: 0x06000610 RID: 1552 RVA: 0x0001CCA8 File Offset: 0x0001AEA8
	public void PasswordJoin()
	{
		if ((string)this.passwordRoom.CustomProperties["password"] == this.passwordInput.text)
		{
			if (PhotonNetwork.JoinRoom(this.passwordRoom.Name, null))
			{
				SteamFriendsController.Instance.OnMasterClientJoinRoom(this.passwordRoom.Name);
				return;
			}
		}
		else
		{
			this.DisplayErrorMessage("Password is wrong.");
		}
	}

	// Token: 0x06000611 RID: 1553 RVA: 0x0001CD15 File Offset: 0x0001AF15
	public void PasswordAbort()
	{
		this.passwordUi.SetActive(false);
	}

	// Token: 0x06000612 RID: 1554 RVA: 0x0001CD23 File Offset: 0x0001AF23
	public void DisplayErrorMessage(string content)
	{
		this.em_text.text = content;
		this.em_root.SetActive(true);
		this.em_button.Select();
	}

	// Token: 0x06000613 RID: 1555 RVA: 0x0001CD48 File Offset: 0x0001AF48
	private void UpdateCachedRoomList(List<RoomInfo> roomList)
	{
		foreach (RoomInfo roomInfo in roomList)
		{
			if (!roomInfo.IsOpen || !roomInfo.IsVisible || roomInfo.RemovedFromList)
			{
				if (this.cachedRoomList.ContainsKey(roomInfo.Name))
				{
					this.cachedRoomList.Remove(roomInfo.Name);
				}
			}
			else if (this.cachedRoomList.ContainsKey(roomInfo.Name))
			{
				this.cachedRoomList[roomInfo.Name] = roomInfo;
			}
			else
			{
				this.cachedRoomList.Add(roomInfo.Name, roomInfo);
			}
		}
	}

	// Token: 0x06000614 RID: 1556 RVA: 0x0001CE10 File Offset: 0x0001B010
	private void InitRegionDropdown()
	{
		Kit_RegionInformation[] allRegions = this._game.allRegions;
		List<string> list = new List<string>(allRegions.Length);
		for (int i = 0; i < allRegions.Length; i++)
		{
			list.Add(string.Concat(new string[]
			{
				allRegions[i].regionName,
				" ",
				allRegions[i].serverLocation,
				" ",
				allRegions[i].token
			}));
		}
		this._regionDropdown.ClearOptions();
		this._regionDropdown.AddOptions(list);
		this._regionDropdown.SetValueWithoutNotify(RegionController.Instance.GetCurrentRegionIndex());
	}

	// Token: 0x06000615 RID: 1557 RVA: 0x0001CEAE File Offset: 0x0001B0AE
	private void SetRegionDropdown(int index)
	{
		this._regionDropdown.SetValueWithoutNotify(index);
	}

	// Token: 0x06000616 RID: 1558 RVA: 0x0001CEBC File Offset: 0x0001B0BC
	public void OnDisconnected(DisconnectCause cause)
	{
		this.cachedRoomList = new Dictionary<string, RoomInfo>();
		for (int i = 0; i < this.activeEntries.Count; i++)
		{
			UnityEngine.Object.Destroy(this.activeEntries[i]);
		}
		this.activeEntries = new List<GameObject>();
	}

	// Token: 0x06000617 RID: 1559 RVA: 0x0001CF08 File Offset: 0x0001B108
	public void OnRoomListUpdate(List<RoomInfo> roomList)
	{
		this.UpdateCachedRoomList(roomList);
		for (int i = 0; i < this.activeEntries.Count; i++)
		{
			UnityEngine.Object.Destroy(this.activeEntries[i]);
		}
		this.activeEntries = new List<GameObject>();
		foreach (RoomInfo roomInfo in this.cachedRoomList.Values)
		{
			if (!(bool)roomInfo.CustomProperties["lobby"])
			{
				GameObject gameObject = UnityEngine.Object.Instantiate<GameObject>(this.entriesPrefab, this.entriesGo);
				gameObject.GetComponent<ServerBrowserEntry>().Setup(this, roomInfo, this._game);
				this.activeEntries.Add(gameObject);
			}
		}
	}

	// Token: 0x04000542 RID: 1346
	[SerializeField]
	private RectTransform entriesGo;

	// Token: 0x04000543 RID: 1347
	[SerializeField]
	private GameObject entriesPrefab;

	// Token: 0x04000544 RID: 1348
	[SerializeField]
	public RoomInfo passwordRoom;

	// Token: 0x04000545 RID: 1349
	[SerializeField]
	private GameObject passwordUi;

	// Token: 0x04000546 RID: 1350
	[SerializeField]
	private TMP_InputField passwordInput;

	// Token: 0x04000547 RID: 1351
	[SerializeField]
	private GameObject em_root;

	// Token: 0x04000548 RID: 1352
	[SerializeField]
	private TextMeshProUGUI em_text;

	// Token: 0x04000549 RID: 1353
	[SerializeField]
	private Button em_button;

	// Token: 0x0400054A RID: 1354
	[SerializeField]
	private Button _backButton;

	// Token: 0x0400054B RID: 1355
	[SerializeField]
	private ServerBrowserPhotonPartVIew _photonCallbacks;

	// Token: 0x0400054C RID: 1356
	[SerializeField]
	private TMP_Dropdown _regionDropdown;

	// Token: 0x0400054D RID: 1357
	private List<GameObject> activeEntries = new List<GameObject>();

	// Token: 0x0400054E RID: 1358
	private bool isPasswordActive;

	// Token: 0x0400054F RID: 1359
	private Dictionary<string, RoomInfo> cachedRoomList = new Dictionary<string, RoomInfo>();

	// Token: 0x04000550 RID: 1360
	private Kit_GameInformation _game;
}
﻿using System;
using Steamworks;
using TMPro;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.UI;

// Token: 0x020000BB RID: 187
public class ShopItem : MonoBehaviour
{
	// Token: 0x06000491 RID: 1169 RVA: 0x000154A4 File Offset: 0x000136A4
	private void OnEnable()
	{
		this._costText.text = "";
		this._buyButton.onClick.AddListener(new UnityAction(this.BuyItem));
		StoreController.Instance.inventoryResultEvent.AddListener(new UnityAction(this.Redraw));
	}

	// Token: 0x06000492 RID: 1170 RVA: 0x000154F8 File Offset: 0x000136F8
	private void Start()
	{
		this.LoadPrice();
	}

	// Token: 0x06000493 RID: 1171 RVA: 0x00015500 File Offset: 0x00013700
	public void SetStoreController(StoreController storeScreenController)
	{
		this._storeController = storeScreenController;
	}

	// Token: 0x06000494 RID: 1172 RVA: 0x00015509 File Offset: 0x00013709
	private void LoadPrice()
	{
		this._loadPriceResult = CallResult<SteamInventoryRequestPricesResult_t>.Create(delegate(SteamInventoryRequestPricesResult_t cb, bool a)
		{
			this._costText.text = cb.m_rgchCurrency.ToString();
			this.GetItemPrice();
		});
		this._loadPriceResult.Set(SteamInventory.RequestPrices(), null);
	}

	// Token: 0x06000495 RID: 1173 RVA: 0x00015534 File Offset: 0x00013734
	private void GetItemPrice()
	{
		ulong num;
		ulong num2;
		if (SteamInventory.GetItemPrice(this._itemDef[0], out num, out num2))
		{
			this._costText.text = string.Format("{0} {1}", num / 100f, this._costText.text);
		}
	}

	// Token: 0x06000496 RID: 1174 RVA: 0x00015586 File Offset: 0x00013786
	public void BuyItem()
	{
		StoreController.Instance.AwakePurchase(this._itemDef, this._itemsQuant);
	}

	// Token: 0x06000497 RID: 1175 RVA: 0x000155A0 File Offset: 0x000137A0
	public void Redraw()
	{
		if (StoreController.Instance.openItems.Contains(this._itemDef[0].m_SteamItemDef))
		{
			this._unactiveImage.gameObject.SetActive(true);
			this._buyButton.gameObject.SetActive(false);
			this._costText.transform.parent.gameObject.SetActive(false);
			return;
		}
		this._unactiveImage.gameObject.SetActive(false);
		this._buyButton.gameObject.SetActive(true);
		this._costText.transform.parent.gameObject.SetActive(true);
	}

	// Token: 0x06000498 RID: 1176 RVA: 0x0001564A File Offset: 0x0001384A
	public int GetIdItem()
	{
		return this._itemDef[0].m_SteamItemDef;
	}

	// Token: 0x06000499 RID: 1177 RVA: 0x0001565D File Offset: 0x0001385D
	private void OnDisable()
	{
		this._buyButton.onClick.RemoveAllListeners();
		StoreController.Instance.inventoryResultEvent.RemoveListener(new UnityAction(this.Redraw));
	}

	// Token: 0x0400037D RID: 893
	[SerializeField]
	private SteamItemDef_t[] _itemDef = new SteamItemDef_t[1];

	// Token: 0x0400037E RID: 894
	[SerializeField]
	private uint[] _itemsQuant = new uint[1];

	// Token: 0x0400037F RID: 895
	[SerializeField]
	private Button _buyButton;

	// Token: 0x04000380 RID: 896
	[SerializeField]
	private TextMeshProUGUI _costText;

	// Token: 0x04000381 RID: 897
	[SerializeField]
	private Image _itemImage;

	// Token: 0x04000382 RID: 898
	[SerializeField]
	private Image _unactiveImage;

	// Token: 0x04000383 RID: 899
	private StoreController _storeController;

	// Token: 0x04000384 RID: 900
	private CallResult<SteamInventoryRequestPricesResult_t> _loadPriceResult;

	// Token: 0x04000385 RID: 901
	private CallResult<SteamInventoryStartPurchaseResult_t> _startPurchaseResult;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using UnityEngine;

// Token: 0x020000BA RID: 186
public class SightCameraAttachment : Kit_AttachmentBehaviour
{
	// Token: 0x0600048E RID: 1166 RVA: 0x0001543C File Offset: 0x0001363C
	public override void Selected(Kit_PlayerBehaviour pb, AttachmentUseCase auc)
	{
		for (int i = 0; i < this._sightsCameras.Length; i++)
		{
			this._sightsCameras[i].enabled = true;
		}
	}

	// Token: 0x0600048F RID: 1167 RVA: 0x0001546C File Offset: 0x0001366C
	public override void Unselected(Kit_PlayerBehaviour pb, AttachmentUseCase auc)
	{
		for (int i = 0; i < this._sightsCameras.Length; i++)
		{
			this._sightsCameras[i].enabled = false;
		}
	}

	// Token: 0x0400037C RID: 892
	[SerializeField]
	private Camera[] _sightsCameras;
}
﻿using System;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000F1 RID: 241
public class SliderTextView : MonoBehaviour
{
	// Token: 0x0600061A RID: 1562 RVA: 0x0001CFFC File Offset: 0x0001B1FC
	private void OnEnable()
	{
		this._slider.onValueChanged.AddListener(delegate(float a)
		{
			this._text.text = a.ToString(this._format);
		});
		this._text.text = this._slider.value.ToString(this._format);
	}

	// Token: 0x0600061B RID: 1563 RVA: 0x0001D049 File Offset: 0x0001B249
	private void OnDisable()
	{
		this._slider.onValueChanged.RemoveListener(delegate(float a)
		{
			this._text.text = a.ToString(this._format);
		});
	}

	// Token: 0x04000551 RID: 1361
	[SerializeField]
	private TextMeshProUGUI _text;

	// Token: 0x04000552 RID: 1362
	[SerializeField]
	private Slider _slider;

	// Token: 0x04000553 RID: 1363
	[SerializeField]
	private string _format;
}
﻿using System;
using UnityEngine;
using UnityEngine.EventSystems;

// Token: 0x02000109 RID: 265
public class SoundEffectView : MonoBehaviour, IPointerEnterHandler, IEventSystemHandler, IPointerClickHandler
{
	// Token: 0x060006B7 RID: 1719 RVA: 0x0001FA10 File Offset: 0x0001DC10
	public void OnPointerEnter(PointerEventData eventData)
	{
		this._menuAudio.PlayOneShot(this._buttonEnterAudioClip);
	}

	// Token: 0x060006B8 RID: 1720 RVA: 0x0001FA23 File Offset: 0x0001DC23
	public void OnPointerClick(PointerEventData eventData)
	{
		this._menuAudio.PlayOneShot(this._buttonClickAudioClip);
	}

	// Token: 0x040005FB RID: 1531
	[SerializeField]
	private AudioSource _menuAudio;

	// Token: 0x040005FC RID: 1532
	[SerializeField]
	private AudioClip _buttonEnterAudioClip;

	// Token: 0x040005FD RID: 1533
	[SerializeField]
	private AudioClip _buttonClickAudioClip;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.UI;
using Newtonsoft.Json;
using UnityEngine;

// Token: 0x02000064 RID: 100
[CreateAssetMenu(menuName = "MarsFPSKit/Statistics/ServerData")]
public class StaticticsServerData : Kit_StatisticsBase, IAccount
{
	// Token: 0x0600029D RID: 669 RVA: 0x0000DD33 File Offset: 0x0000BF33
	public override void OnAssist(Kit_IngameMain main)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.Assists++;
		}
	}

	// Token: 0x0600029E RID: 670 RVA: 0x0000DD55 File Offset: 0x0000BF55
	public override void OnDeath(Kit_IngameMain main, int weapon)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.Deaths++;
		}
	}

	// Token: 0x0600029F RID: 671 RVA: 0x0000DD55 File Offset: 0x0000BF55
	public override void OnDeath(Kit_IngameMain main, string weapon)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.Deaths++;
		}
	}

	// Token: 0x060002A0 RID: 672 RVA: 0x0000DD77 File Offset: 0x0000BF77
	public override void OnKill(Kit_IngameMain main, int weapon)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.Kills++;
		}
	}

	// Token: 0x060002A1 RID: 673 RVA: 0x0000DD77 File Offset: 0x0000BF77
	public override void OnKill(Kit_IngameMain main, string reason)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.Kills++;
		}
	}

	// Token: 0x060002A2 RID: 674 RVA: 0x00005F78 File Offset: 0x00004178
	public override void OnStart(Kit_MenuManager menu)
	{
	}

	// Token: 0x060002A3 RID: 675 RVA: 0x00005F78 File Offset: 0x00004178
	public override void OnStart()
	{
	}

	// Token: 0x060002A4 RID: 676 RVA: 0x0000DD9C File Offset: 0x0000BF9C
	public override void Save(Kit_IngameMain main)
	{
		string accountJson = JsonConvert.SerializeObject(this._accountModel);
		DataSender.Instance.SetPlayerStatistics(accountJson, delegate(string str)
		{
		});
	}

	// Token: 0x060002A5 RID: 677 RVA: 0x00005F78 File Offset: 0x00004178
	public override void Save(Kit_MenuManager menu)
	{
	}

	// Token: 0x060002A6 RID: 678 RVA: 0x0000DDDF File Offset: 0x0000BFDF
	public override void OnHeadShot(Kit_IngameMain main)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.HeadShot++;
		}
	}

	// Token: 0x060002A7 RID: 679 RVA: 0x0000DE01 File Offset: 0x0000C001
	public override void OnWin(Kit_IngameMain main)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.WinMatch++;
		}
	}

	// Token: 0x060002A8 RID: 680 RVA: 0x0000DE23 File Offset: 0x0000C023
	public override void OnEndGame(Kit_IngameMain main)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.MatchCount++;
		}
	}

	// Token: 0x060002A9 RID: 681 RVA: 0x0000DE45 File Offset: 0x0000C045
	public override void OnKillByExplosion(Kit_IngameMain main)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.KilledByExplosion++;
		}
	}

	// Token: 0x060002AA RID: 682 RVA: 0x0000DE67 File Offset: 0x0000C067
	public override void OnInstalledBomb(Kit_IngameMain main)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.InstalledBombs++;
		}
	}

	// Token: 0x060002AB RID: 683 RVA: 0x0000DE89 File Offset: 0x0000C089
	public override void OnMeleeKill(Kit_IngameMain main)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.KillByMelee++;
		}
	}

	// Token: 0x060002AC RID: 684 RVA: 0x0000DEAB File Offset: 0x0000C0AB
	public override void OnShotGunKill(Kit_IngameMain main)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.KillByShotGun++;
		}
	}

	// Token: 0x060002AD RID: 685 RVA: 0x0000DECD File Offset: 0x0000C0CD
	public override void OnAssaultKill(Kit_IngameMain main)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.KillByAssault++;
		}
	}

	// Token: 0x060002AE RID: 686 RVA: 0x0000DEEF File Offset: 0x0000C0EF
	public override void OnPistolKill(Kit_IngameMain main)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.KillByPistol++;
		}
	}

	// Token: 0x060002AF RID: 687 RVA: 0x0000DF11 File Offset: 0x0000C111
	public override void OnSniperKill(Kit_IngameMain main)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.KillBySniper++;
		}
	}

	// Token: 0x060002B0 RID: 688 RVA: 0x0000DF33 File Offset: 0x0000C133
	public override void OnLevelUpdate(int level)
	{
		if (this._accountModel != null)
		{
			this._accountModel.Statistics.CurrentLevel = level;
		}
	}

	// Token: 0x060002B1 RID: 689 RVA: 0x0000DF4E File Offset: 0x0000C14E
	public override int GetCurrentLevel()
	{
		if (this._accountModel != null)
		{
			return this._accountModel.Statistics.CurrentLevel;
		}
		return -1;
	}

	// Token: 0x17000026 RID: 38
	// (get) Token: 0x060002B2 RID: 690 RVA: 0x0000DF6A File Offset: 0x0000C16A
	// (set) Token: 0x060002B3 RID: 691 RVA: 0x0000DF72 File Offset: 0x0000C172
	public AccountModel Account
	{
		get
		{
			return this._accountModel;
		}
		set
		{
			this._accountModel = value;
		}
	}

	// Token: 0x040001CB RID: 459
	private AccountModel _accountModel;
}
﻿using System;
using TigerForge;

// Token: 0x02000033 RID: 51
public static class StatisticDataSave
{
	// Token: 0x0600013B RID: 315 RVA: 0x00006DC3 File Offset: 0x00004FC3
	public static void SaveData(string key, int value)
	{
		EasyFileSave easyFileSave = new EasyFileSave("DailyData");
		easyFileSave.Add(key, value, false);
		easyFileSave.Append(true, "");
	}

	// Token: 0x0600013C RID: 316 RVA: 0x00006DE9 File Offset: 0x00004FE9
	public static void SaveData(string key, bool value)
	{
		EasyFileSave easyFileSave = new EasyFileSave("DailyData");
		easyFileSave.Add(key, value, false);
		easyFileSave.Append(true, "");
	}

	// Token: 0x0600013D RID: 317 RVA: 0x00006E0F File Offset: 0x0000500F
	public static void SaveData(string key, DateTime value)
	{
		EasyFileSave easyFileSave = new EasyFileSave("DailyData");
		easyFileSave.Add(key, value, false);
		easyFileSave.Append(true, "");
	}

	// Token: 0x0600013E RID: 318 RVA: 0x00006E38 File Offset: 0x00005038
	public static int LoadDataInt(string key)
	{
		EasyFileSave easyFileSave = new EasyFileSave("DailyData");
		if (easyFileSave.Load(""))
		{
			int @int = easyFileSave.GetInt(key, -999);
			easyFileSave.Dispose();
			return @int;
		}
		return -999;
	}

	// Token: 0x0600013F RID: 319 RVA: 0x00006E78 File Offset: 0x00005078
	public static bool LoadDataBool(string key)
	{
		EasyFileSave easyFileSave = new EasyFileSave("DailyData");
		if (easyFileSave.Load(""))
		{
			bool @bool = easyFileSave.GetBool(key, false);
			easyFileSave.Dispose();
			return @bool;
		}
		return false;
	}

	// Token: 0x06000140 RID: 320 RVA: 0x00006EB0 File Offset: 0x000050B0
	public static DateTime LoadDateTime(string key)
	{
		EasyFileSave easyFileSave = new EasyFileSave("DailyData");
		if (easyFileSave.Load(""))
		{
			DateTime dateTime = easyFileSave.GetDateTime(key, DateTime.Today.AddHours(3.0));
			easyFileSave.Dispose();
			return dateTime;
		}
		return DateTime.Today.AddHours(3.0);
	}
}
﻿using System;

// Token: 0x0200008B RID: 139
public class StatisticsModel
{
	// Token: 0x04000289 RID: 649
	public int Kills;

	// Token: 0x0400028A RID: 650
	public int Deaths;

	// Token: 0x0400028B RID: 651
	public int Assists;

	// Token: 0x0400028C RID: 652
	public int HeadShot;

	// Token: 0x0400028D RID: 653
	public int WinMatch;

	// Token: 0x0400028E RID: 654
	public int MatchCount;

	// Token: 0x0400028F RID: 655
	public int InstalledBombs;

	// Token: 0x04000290 RID: 656
	public int KilledByExplosion;

	// Token: 0x04000291 RID: 657
	public int KillByMelee;

	// Token: 0x04000292 RID: 658
	public int KillByShotGun;

	// Token: 0x04000293 RID: 659
	public int KillByAssault;

	// Token: 0x04000294 RID: 660
	public int KillByPistol;

	// Token: 0x04000295 RID: 661
	public int KillBySniper;

	// Token: 0x04000296 RID: 662
	public int CurrentLevel;

	// Token: 0x04000297 RID: 663
	public int CurrentXp;
}
﻿using System;
using System.Collections.Generic;
using Steamworks;
using UnityEngine;

// Token: 0x02000065 RID: 101
public class SteamAvatarsService
{
	// Token: 0x17000027 RID: 39
	// (get) Token: 0x060002B5 RID: 693 RVA: 0x0000DF83 File Offset: 0x0000C183
	public static SteamAvatarsService Instance
	{
		get
		{
			if (SteamAvatarsService._instance == null)
			{
				SteamAvatarsService._instance = new SteamAvatarsService();
			}
			return SteamAvatarsService._instance;
		}
	}

	// Token: 0x060002B6 RID: 694 RVA: 0x0000DF9B File Offset: 0x0000C19B
	private SteamAvatarsService()
	{
		this._avatarBase = new Dictionary<CSteamID, Sprite>();
	}

	// Token: 0x060002B7 RID: 695 RVA: 0x0000DFAE File Offset: 0x0000C1AE
	public Sprite GetSteamUserAvatar(CSteamID userID)
	{
		if (this._avatarBase.ContainsKey(userID))
		{
			return this._avatarBase[userID];
		}
		this._avatarBase.Add(userID, this.GetFriendSprite(userID));
		return this._avatarBase[userID];
	}

	// Token: 0x060002B8 RID: 696 RVA: 0x0000DFEC File Offset: 0x0000C1EC
	private Sprite GetFriendSprite(CSteamID steamID)
	{
		Texture2D texture = this.GetTexture(steamID);
		return Sprite.Create(texture, new Rect(0f, 0f, (float)texture.width, (float)texture.height), Vector2.zero, 100f, 0U, SpriteMeshType.FullRect);
	}

	// Token: 0x060002B9 RID: 697 RVA: 0x0000E030 File Offset: 0x0000C230
	private Texture2D GetTexture(CSteamID steamID)
	{
		int mediumFriendAvatar = SteamFriends.GetMediumFriendAvatar(steamID);
		uint num;
		uint num2;
		if (SteamUtils.GetImageSize(mediumFriendAvatar, out num, out num2))
		{
			int num3 = (int)(num * num2 * 4U);
			byte[] array = new byte[num3];
			Texture2D texture2D = new Texture2D((int)num, (int)num2, TextureFormat.RGBA32, false, true);
			if (SteamUtils.GetImageRGBA(mediumFriendAvatar, array, num3))
			{
				texture2D.LoadRawTextureData(array);
				texture2D.Apply();
			}
			return this.MirrorTexture(texture2D, (int)num, (int)num2);
		}
		Debug.LogError("Cannot download Image");
		return new Texture2D(0, 0);
	}

	// Token: 0x060002BA RID: 698 RVA: 0x0000E0A4 File Offset: 0x0000C2A4
	private Texture2D MirrorTexture(Texture2D texture, int width, int height)
	{
		Texture2D texture2D = new Texture2D(width, height, TextureFormat.RGBA32, false, true);
		for (int i = 0; i < width; i++)
		{
			for (int j = 0; j < height; j++)
			{
				Color pixel = texture.GetPixel(i, j);
				texture2D.SetPixel(i, height - 1 - j, pixel);
			}
		}
		texture2D.Apply();
		return texture2D;
	}

	// Token: 0x040001CC RID: 460
	private static SteamAvatarsService _instance;

	// Token: 0x040001CD RID: 461
	private Dictionary<CSteamID, Sprite> _avatarBase;
}
﻿using System;
using Steamworks;
using UnityEngine;

// Token: 0x0200008C RID: 140
[Serializable]
public class SteamFriendModel
{
	// Token: 0x1700003B RID: 59
	// (get) Token: 0x06000356 RID: 854 RVA: 0x0000FF19 File Offset: 0x0000E119
	public CSteamID SteamID
	{
		get
		{
			return this._steamID;
		}
	}

	// Token: 0x1700003C RID: 60
	// (get) Token: 0x06000357 RID: 855 RVA: 0x0000FF21 File Offset: 0x0000E121
	public string Name
	{
		get
		{
			return this._name;
		}
	}

	// Token: 0x1700003D RID: 61
	// (get) Token: 0x06000358 RID: 856 RVA: 0x0000FF29 File Offset: 0x0000E129
	public Sprite Image
	{
		get
		{
			return this._image;
		}
	}

	// Token: 0x06000359 RID: 857 RVA: 0x0000FF31 File Offset: 0x0000E131
	public SteamFriendModel(CSteamID steamID, string name, Sprite sprite, EPersonaState state)
	{
		this._steamID = steamID;
		this._name = name;
		this._image = sprite;
		this.PlayerState = state;
	}

	// Token: 0x04000298 RID: 664
	public EPersonaState PlayerState;

	// Token: 0x04000299 RID: 665
	private CSteamID _steamID;

	// Token: 0x0400029A RID: 666
	private string _name;

	// Token: 0x0400029B RID: 667
	private Sprite _image;
}
﻿using System;
using System.Collections.Generic;
using System.Text;
using Photon.Pun;
using Steamworks;
using UnityEngine;

// Token: 0x02000066 RID: 102
public class SteamFriendsController
{
	// Token: 0x17000028 RID: 40
	// (get) Token: 0x060002BB RID: 699 RVA: 0x0000E0F1 File Offset: 0x0000C2F1
	public static SteamFriendsController Instance
	{
		get
		{
			if (SteamFriendsController._instance == null)
			{
				SteamFriendsController._instance = new SteamFriendsController();
			}
			return SteamFriendsController._instance;
		}
	}

	// Token: 0x060002BC RID: 700 RVA: 0x0000E109 File Offset: 0x0000C309
	private SteamFriendsController()
	{
		this.SteamCallbacksInit();
	}

	// Token: 0x060002BD RID: 701 RVA: 0x0000E120 File Offset: 0x0000C320
	public void OnMasterClientJoinRoom(string roomName)
	{
		if (this._isMasterClient)
		{
			byte[] bytes = Encoding.Default.GetBytes(roomName);
			SteamMatchmaking.SendLobbyChatMsg(this._lobbyID, bytes, bytes.Length + 1);
		}
	}

	// Token: 0x060002BE RID: 702 RVA: 0x0000E153 File Offset: 0x0000C353
	public void CreateGroup()
	{
		this._lobbyCreatedCallResult.Set(SteamMatchmaking.CreateLobby(ELobbyType.k_ELobbyTypeInvisible, this._maxMembers), null);
	}

	// Token: 0x060002BF RID: 703 RVA: 0x0000E16D File Offset: 0x0000C36D
	public bool IsPlayerInGroup(SteamFriendModel friendModel)
	{
		return this._friendsListInGroup.Contains(friendModel);
	}

	// Token: 0x060002C0 RID: 704 RVA: 0x0000E17B File Offset: 0x0000C37B
	public void InviteFriendToGroup(SteamFriendModel friendModel)
	{
		SteamMatchmaking.InviteUserToLobby(friendModel.SteamID, this._lobbyID);
	}

	// Token: 0x060002C1 RID: 705 RVA: 0x0000E190 File Offset: 0x0000C390
	public void Init()
	{
		SteamViewHub steamViewHub = UnityEngine.Object.FindObjectOfType<SteamViewHub>();
		this._steamFriendsPanel = steamViewHub.PanelView;
		this._steamFriendsInGroup = steamViewHub.GroupPanelView;
		this._playerInfoView = steamViewHub.PlayerInfoView;
		this._steamFriendsPanel.ViewInit(this);
		this._steamFriendsInGroup.ViewInit(this);
		this.LoadFriends();
		this.GetPlayer();
		CSteamID lobbyID = this._lobbyID;
		this.LoadFriendsInLobby(this._lobbyID);
	}

	// Token: 0x060002C2 RID: 706 RVA: 0x0000E200 File Offset: 0x0000C400
	public string[] GetExpectedUsers()
	{
		int count = this._friendsListInGroup.Count;
		if (count == 0)
		{
			return null;
		}
		string[] array = new string[count];
		for (int i = 0; i < this._friendsListInGroup.Count; i++)
		{
			array[i] = this._friendsListInGroup[i].Name;
		}
		return array;
	}

	// Token: 0x060002C3 RID: 707 RVA: 0x0000E250 File Offset: 0x0000C450
	private void SteamCallbacksInit()
	{
		this._lobbyCreatedCallResult = CallResult<LobbyCreated_t>.Create(new CallResult<LobbyCreated_t>.APIDispatchDelegate(this.OnLobbyCreated));
		this._lobbyChatMsgResult = Callback<LobbyChatMsg_t>.Create(new Callback<LobbyChatMsg_t>.DispatchDelegate(this.OnChatMsgResult));
		this._lobbyEnterCallback = Callback<LobbyEnter_t>.Create(new Callback<LobbyEnter_t>.DispatchDelegate(this.OnLobbyEnter));
		this._goInGameCallback = Callback<GameRichPresenceJoinRequested_t>.Create(new Callback<GameRichPresenceJoinRequested_t>.DispatchDelegate(this.OnGameEter));
		this._lobbyMembersChanged = Callback<LobbyChatUpdate_t>.Create(new Callback<LobbyChatUpdate_t>.DispatchDelegate(this.OnLobbyMembersChanged));
		this._goInLobbyCallback = Callback<GameLobbyJoinRequested_t>.Create(new Callback<GameLobbyJoinRequested_t>.DispatchDelegate(this.OnGameLobbyJoinRequest));
	}

	// Token: 0x060002C4 RID: 708 RVA: 0x0000E2E8 File Offset: 0x0000C4E8
	private void GetPlayer()
	{
		CSteamID steamID = SteamUser.GetSteamID();
		string personaName = SteamFriends.GetPersonaName();
		Sprite steamUserAvatar = SteamAvatarsService.Instance.GetSteamUserAvatar(steamID);
		this._player = new SteamFriendModel(steamID, personaName, steamUserAvatar, EPersonaState.k_EPersonaStateOnline);
		this._playerInfoView.SetView(this._player);
	}

	// Token: 0x060002C5 RID: 709 RVA: 0x0000E330 File Offset: 0x0000C530
	private void LoadFriends()
	{
		int num = SteamFriends.GetFriendCount(EFriendFlags.k_EFriendFlagImmediate);
		if (num == -1)
		{
			num = 0;
		}
		this._friendsList = new List<SteamFriendModel>(num);
		for (int i = 0; i < num; i++)
		{
			CSteamID friendByIndex = SteamFriends.GetFriendByIndex(i, EFriendFlags.k_EFriendFlagImmediate);
			string friendPersonaName = SteamFriends.GetFriendPersonaName(friendByIndex);
			Sprite steamUserAvatar = SteamAvatarsService.Instance.GetSteamUserAvatar(friendByIndex);
			EPersonaState friendPersonaState = SteamFriends.GetFriendPersonaState(friendByIndex);
			SteamFriendModel item = new SteamFriendModel(friendByIndex, friendPersonaName, steamUserAvatar, friendPersonaState);
			this._friendsList.Add(item);
		}
		this._steamFriendsPanel.SetView(this._friendsList);
	}

	// Token: 0x060002C6 RID: 710 RVA: 0x0000E3B4 File Offset: 0x0000C5B4
	private void LoadFriendsInLobby(CSteamID steamID)
	{
		int num = SteamMatchmaking.GetNumLobbyMembers(steamID);
		if (num == -1)
		{
			num = 0;
		}
		this._friendsListInGroup = new List<SteamFriendModel>(num);
		for (int i = 0; i < num; i++)
		{
			CSteamID lobbyMemberByIndex = SteamMatchmaking.GetLobbyMemberByIndex(steamID, i);
			string friendPersonaName = SteamFriends.GetFriendPersonaName(lobbyMemberByIndex);
			Sprite steamUserAvatar = SteamAvatarsService.Instance.GetSteamUserAvatar(lobbyMemberByIndex);
			EPersonaState friendPersonaState = SteamFriends.GetFriendPersonaState(lobbyMemberByIndex);
			SteamFriendModel item = new SteamFriendModel(lobbyMemberByIndex, friendPersonaName, steamUserAvatar, friendPersonaState);
			this._friendsListInGroup.Add(item);
		}
		this._steamFriendsInGroup.SetView(this._friendsListInGroup);
	}

	// Token: 0x060002C7 RID: 711 RVA: 0x0000E438 File Offset: 0x0000C638
	private SteamFriendModel GetFriendByID(CSteamID id)
	{
		for (int i = 0; i < this._friendsList.Count; i++)
		{
			if (this._friendsList[i].SteamID == id)
			{
				return this._friendsList[i];
			}
		}
		return null;
	}

	// Token: 0x060002C8 RID: 712 RVA: 0x00005F78 File Offset: 0x00004178
	private void OnLobbyCreated(LobbyCreated_t param, bool bIOFailure)
	{
	}

	// Token: 0x060002C9 RID: 713 RVA: 0x0000E484 File Offset: 0x0000C684
	private void OnLobbyEnter(LobbyEnter_t param)
	{
		this._lobbyID = new CSteamID(param.m_ulSteamIDLobby);
		if (SteamMatchmaking.GetLobbyOwner(this._lobbyID) == this._player.SteamID)
		{
			this._isMasterClient = true;
		}
		else
		{
			this._isMasterClient = false;
		}
		this.LoadFriendsInLobby(this._lobbyID);
		this._steamFriendsPanel.UpdateInviteAbilities();
	}

	// Token: 0x060002CA RID: 714 RVA: 0x0000E4E6 File Offset: 0x0000C6E6
	private void OnGameEter(GameRichPresenceJoinRequested_t param)
	{
		Debug.Log("OnGameEnter");
	}

	// Token: 0x060002CB RID: 715 RVA: 0x0000E4F2 File Offset: 0x0000C6F2
	private void OnGameLobbyJoinRequest(GameLobbyJoinRequested_t param)
	{
		Debug.Log("OnGameLobbyJoinRequest!");
		SteamMatchmaking.JoinLobby(param.m_steamIDLobby);
	}

	// Token: 0x060002CC RID: 716 RVA: 0x0000E50A File Offset: 0x0000C70A
	private void OnLobbyMembersChanged(LobbyChatUpdate_t param)
	{
		Debug.Log("OnLobbyMembersCHanged");
		this.LoadFriendsInLobby(this._lobbyID);
		this._steamFriendsPanel.UpdateInviteAbilities();
	}

	// Token: 0x060002CD RID: 717 RVA: 0x0000E530 File Offset: 0x0000C730
	private void OnChatMsgResult(LobbyChatMsg_t pCallback)
	{
		byte[] array = new byte[4096];
		CSteamID csteamID;
		EChatEntryType echatEntryType;
		SteamMatchmaking.GetLobbyChatEntry((CSteamID)pCallback.m_ulSteamIDLobby, (int)pCallback.m_iChatID, out csteamID, array, array.Length, out echatEntryType);
		string @string = Encoding.Default.GetString(array);
		if (!this._isMasterClient)
		{
			PhotonNetwork.JoinRoom(@string, null);
		}
	}

	// Token: 0x040001CE RID: 462
	private SteamFriendsPanel _steamFriendsPanel;

	// Token: 0x040001CF RID: 463
	private SteamFriendsInGroupPanel _steamFriendsInGroup;

	// Token: 0x040001D0 RID: 464
	private SteamFriendModel _player;

	// Token: 0x040001D1 RID: 465
	private List<SteamFriendModel> _friendsList;

	// Token: 0x040001D2 RID: 466
	private List<SteamFriendModel> _friendsListInGroup;

	// Token: 0x040001D3 RID: 467
	private SteamPlayerInfoView _playerInfoView;

	// Token: 0x040001D4 RID: 468
	private CallResult<LobbyCreated_t> _lobbyCreatedCallResult;

	// Token: 0x040001D5 RID: 469
	private Callback<LobbyChatMsg_t> _lobbyChatMsgResult;

	// Token: 0x040001D6 RID: 470
	private Callback<LobbyEnter_t> _lobbyEnterCallback;

	// Token: 0x040001D7 RID: 471
	private Callback<GameRichPresenceJoinRequested_t> _goInGameCallback;

	// Token: 0x040001D8 RID: 472
	private Callback<LobbyChatUpdate_t> _lobbyMembersChanged;

	// Token: 0x040001D9 RID: 473
	private Callback<GameLobbyJoinRequested_t> _goInLobbyCallback;

	// Token: 0x040001DA RID: 474
	private CSteamID _lobbyID;

	// Token: 0x040001DB RID: 475
	private int _maxMembers = 5;

	// Token: 0x040001DC RID: 476
	private static SteamFriendsController _instance;

	// Token: 0x040001DD RID: 477
	private bool _isMasterClient;

	// Token: 0x040001DE RID: 478
	private ObserveProcess _observeProcess;
}
﻿using System;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000F3 RID: 243
public class SteamFriendsInGroupPanel : SteamFriendsPanel
{
	// Token: 0x06000625 RID: 1573 RVA: 0x0001D154 File Offset: 0x0001B354
	private void OnEnable()
	{
		this._createGroupButton.onClick.AddListener(delegate()
		{
			this._createGroupButton.gameObject.SetActive(false);
			this._steamFriendsController.CreateGroup();
		});
	}

	// Token: 0x06000626 RID: 1574 RVA: 0x0001D172 File Offset: 0x0001B372
	private void OnDisable()
	{
		this._createGroupButton.onClick.RemoveAllListeners();
	}

	// Token: 0x04000559 RID: 1369
	[SerializeField]
	private Button _createGroupButton;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x020000F4 RID: 244
public class SteamFriendsPanel : MonoBehaviour
{
	// Token: 0x06000629 RID: 1577 RVA: 0x0001D1AA File Offset: 0x0001B3AA
	public void ViewInit(SteamFriendsController controller)
	{
		this._steamFriendsController = controller;
	}

	// Token: 0x0600062A RID: 1578 RVA: 0x0001D1B3 File Offset: 0x0001B3B3
	public void SetView(List<SteamFriendModel> steamFriends)
	{
		this.ClearViews();
		this.SetFriendsViews(steamFriends);
	}

	// Token: 0x0600062B RID: 1579 RVA: 0x0001D1C4 File Offset: 0x0001B3C4
	public void UpdateInviteAbilities()
	{
		for (int i = 0; i < this._friendsViews.Count; i++)
		{
			this._friendsViews[i].TurnOnInviteAbility();
		}
	}

	// Token: 0x0600062C RID: 1580 RVA: 0x0001D1F8 File Offset: 0x0001B3F8
	private void ClearViews()
	{
		if (this._friendsViews != null)
		{
			for (int i = 0; i < this._friendsViews.Count; i++)
			{
				UnityEngine.Object.Destroy(this._friendsViews[i]);
			}
		}
		this._friendsViews = new List<SteamFriendView>();
	}

	// Token: 0x0600062D RID: 1581 RVA: 0x0001D240 File Offset: 0x0001B440
	private void SetFriendsViews(List<SteamFriendModel> steamFriends)
	{
		float y = this._friendPrefab.GetComponent<RectTransform>().sizeDelta.y;
		for (int i = 0; i < steamFriends.Count; i++)
		{
			SteamFriendView steamFriendView = UnityEngine.Object.Instantiate<SteamFriendView>(this._friendPrefab, this._content);
			steamFriendView.ViewInit(this._steamFriendsController, steamFriends[i]);
			RectTransform component = steamFriendView.GetComponent<RectTransform>();
			component.anchoredPosition = new Vector2(component.anchoredPosition.x, (float)(-(float)i) * y);
			this._friendsViews.Add(steamFriendView);
		}
		this._content.sizeDelta = new Vector2(this._content.sizeDelta.x, y * (float)this._friendsViews.Count);
	}

	// Token: 0x0400055A RID: 1370
	[SerializeField]
	private SteamFriendView _friendPrefab;

	// Token: 0x0400055B RID: 1371
	[SerializeField]
	private RectTransform _content;

	// Token: 0x0400055C RID: 1372
	protected List<SteamFriendView> _friendsViews;

	// Token: 0x0400055D RID: 1373
	protected SteamFriendsController _steamFriendsController;
}
﻿using System;
using Steamworks;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000F2 RID: 242
public class SteamFriendView : MonoBehaviour
{
	// Token: 0x0600061F RID: 1567 RVA: 0x0001D081 File Offset: 0x0001B281
	private void OnEnable()
	{
		this._inviteButton.onClick.AddListener(delegate()
		{
			this._steamFriendsController.InviteFriendToGroup(this._friendModel);
		});
	}

	// Token: 0x06000620 RID: 1568 RVA: 0x0001D09F File Offset: 0x0001B29F
	private void OnDisable()
	{
		this._inviteButton.onClick.RemoveAllListeners();
	}

	// Token: 0x06000621 RID: 1569 RVA: 0x0001D0B1 File Offset: 0x0001B2B1
	public void ViewInit(SteamFriendsController controller, SteamFriendModel friendModel)
	{
		this._steamFriendsController = controller;
		this._friendModel = friendModel;
		this._friendImage.sprite = this._friendModel.Image;
		this._friendsNameText.text = this._friendModel.Name;
	}

	// Token: 0x06000622 RID: 1570 RVA: 0x0001D0F0 File Offset: 0x0001B2F0
	public void TurnOnInviteAbility()
	{
		if (this._friendModel.PlayerState == EPersonaState.k_EPersonaStateOnline && !this._steamFriendsController.IsPlayerInGroup(this._friendModel))
		{
			this._inviteButton.gameObject.SetActive(true);
			return;
		}
		this._inviteButton.gameObject.SetActive(false);
	}

	// Token: 0x04000554 RID: 1364
	[SerializeField]
	private Image _friendImage;

	// Token: 0x04000555 RID: 1365
	[SerializeField]
	private TextMeshProUGUI _friendsNameText;

	// Token: 0x04000556 RID: 1366
	[SerializeField]
	private Button _inviteButton;

	// Token: 0x04000557 RID: 1367
	private SteamFriendsController _steamFriendsController;

	// Token: 0x04000558 RID: 1368
	private SteamFriendModel _friendModel;
}
﻿using System;
using UnityEngine;

// Token: 0x0200008D RID: 141
[CreateAssetMenu(menuName = "SteamInventoryAddLevelsData")]
public class SteamInventoryAddLevelsData : SteamInventoryItemData
{
	// Token: 0x0600035A RID: 858 RVA: 0x0000FF56 File Offset: 0x0000E156
	public override void ActivateItem()
	{
		BattlePassController.Instance.LevelUp(this._levels, true);
	}

	// Token: 0x0400029C RID: 668
	[SerializeField]
	private int _levels;
}
﻿using System;
using Steamworks;
using UnityEngine;
using UnityEngine.Events;

// Token: 0x020000BC RID: 188
[SerializeField]
public class SteamInventoryDetailLoadEvent : UnityEvent<SteamItemDetails_t[]>
{
}
﻿using System;
using UnityEngine;

// Token: 0x0200008E RID: 142
[CreateAssetMenu(menuName = "SteamInventoryItemData")]
public class SteamInventoryItemData : ScriptableObject
{
	// Token: 0x1700003E RID: 62
	// (get) Token: 0x0600035C RID: 860 RVA: 0x0000FF71 File Offset: 0x0000E171
	public string Name
	{
		get
		{
			return this._name;
		}
	}

	// Token: 0x1700003F RID: 63
	// (get) Token: 0x0600035D RID: 861 RVA: 0x0000FF79 File Offset: 0x0000E179
	public int SteamDefId
	{
		get
		{
			return this._steamDefId;
		}
	}

	// Token: 0x17000040 RID: 64
	// (get) Token: 0x0600035E RID: 862 RVA: 0x0000FF81 File Offset: 0x0000E181
	public Sprite Image
	{
		get
		{
			return this._image;
		}
	}

	// Token: 0x17000041 RID: 65
	// (get) Token: 0x0600035F RID: 863 RVA: 0x0000FF89 File Offset: 0x0000E189
	public GameObject Object3D
	{
		get
		{
			return this._3dObject;
		}
	}

	// Token: 0x06000360 RID: 864 RVA: 0x00005F78 File Offset: 0x00004178
	public virtual void ActivateItem()
	{
	}

	// Token: 0x0400029D RID: 669
	[SerializeField]
	private string _name;

	// Token: 0x0400029E RID: 670
	[SerializeField]
	private int _steamDefId;

	// Token: 0x0400029F RID: 671
	[SerializeField]
	private Sprite _image;

	// Token: 0x040002A0 RID: 672
	[SerializeField]
	private GameObject _3dObject;
}
﻿using System;
using Steamworks;
using UnityEngine;

// Token: 0x02000067 RID: 103
public class SteamNamesController
{
	// Token: 0x060002CF RID: 719 RVA: 0x0000E584 File Offset: 0x0000C784
	public void GetPlayerName(ulong steamID, Action<string> callback)
	{
		CSteamID csteamID = new CSteamID(steamID);
		string friendPersonaName = SteamFriends.GetFriendPersonaName(csteamID);
		if (friendPersonaName == "" || friendPersonaName == "[unknown]")
		{
			this.LoadName(csteamID, callback);
			return;
		}
		callback(friendPersonaName);
	}

	// Token: 0x060002D0 RID: 720 RVA: 0x0000E5CC File Offset: 0x0000C7CC
	private void LoadName(CSteamID steamID, Action<string> callback)
	{
		this.personaState = Callback<PersonaStateChange_t>.Create(delegate(PersonaStateChange_t cb)
		{
			Debug.Log(string.Format("steamId: {0}; cb.m_ulSteamID: {1}", steamID.m_SteamID, cb.m_ulSteamID));
			if (steamID == (CSteamID)cb.m_ulSteamID)
			{
				string friendPersonaName = SteamFriends.GetFriendPersonaName(steamID);
				callback(friendPersonaName);
			}
		});
		if (steamID.m_SteamID != 0UL)
		{
			SteamFriends.RequestUserInformation(steamID, true);
			return;
		}
		callback("");
	}

	// Token: 0x040001DF RID: 479
	private Callback<PersonaStateChange_t> personaState;
}
﻿using System;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000F5 RID: 245
public class SteamPlayerInfoView : MonoBehaviour
{
	// Token: 0x0600062F RID: 1583 RVA: 0x0001D2F3 File Offset: 0x0001B4F3
	public void SetView(SteamFriendModel playerModel)
	{
		this._playerAvatar.sprite = playerModel.Image;
		this._playerName.text = playerModel.Name;
	}

	// Token: 0x0400055E RID: 1374
	[SerializeField]
	private Image _playerAvatar;

	// Token: 0x0400055F RID: 1375
	[SerializeField]
	private TextMeshProUGUI _playerName;
}
﻿using System;
using MarsFPSKit;
using Steamworks;
using UnityEngine;

// Token: 0x02000068 RID: 104
public class SteamStatsController
{
	// Token: 0x17000029 RID: 41
	// (get) Token: 0x060002D1 RID: 721 RVA: 0x0000E62F File Offset: 0x0000C82F
	public static SteamStatsController Instance
	{
		get
		{
			if (SteamStatsController._instance == null)
			{
				SteamStatsController._instance = new SteamStatsController();
			}
			return SteamStatsController._instance;
		}
	}

	// Token: 0x060002D2 RID: 722 RVA: 0x0000E647 File Offset: 0x0000C847
	private SteamStatsController()
	{
	}

	// Token: 0x060002D3 RID: 723 RVA: 0x0000E686 File Offset: 0x0000C886
	public void Initialize(Kit_GameInformation game)
	{
		this._game = game;
		if (this._userStatsReceivedCallback == null)
		{
			this.RequestCurrentStats();
			BattlePassController.Instance.OnLevelUp += this.CheckBattlePassLevelUp;
			return;
		}
		this.StoreStats();
	}

	// Token: 0x060002D4 RID: 724 RVA: 0x0000E6BA File Offset: 0x0000C8BA
	private void RequestCurrentStats()
	{
		this._userStatsReceivedCallback = Callback<UserStatsReceived_t>.Create(delegate(UserStatsReceived_t a)
		{
			Debug.Log(string.Format("RequestStatsResult: {0}", a.m_eResult));
			this.GetGlobalStats();
		});
		if (!SteamUserStats.RequestCurrentStats())
		{
			Debug.Log("There is no logged user...");
			return;
		}
		Debug.Log("RequestCurrentStats");
	}

	// Token: 0x060002D5 RID: 725 RVA: 0x0000E6EF File Offset: 0x0000C8EF
	private void SetStat(string statName, int statValue = 1)
	{
		if (SteamUserStats.SetStat(statName, statValue))
		{
			Debug.Log("SetStat success");
			return;
		}
		Debug.Log("SetStat fail...\n Stat no exist \n RequestCurrentStats has not called \n wrong type");
	}

	// Token: 0x060002D6 RID: 726 RVA: 0x0000E710 File Offset: 0x0000C910
	private void StoreStats()
	{
		if (this._userStatsStoredCallback == null)
		{
			this._userStatsStoredCallback = Callback<UserStatsStored_t>.Create(delegate(UserStatsStored_t a)
			{
				if (a.m_eResult == EResult.k_EResultOK)
				{
					Debug.Log("Stats successfully stored");
				}
			});
		}
		if (!SteamUserStats.StoreStats())
		{
			Debug.Log("StoreStats fail... Check RequestCurrentStats or publishing settings");
		}
	}

	// Token: 0x060002D7 RID: 727 RVA: 0x0000E760 File Offset: 0x0000C960
	private void CheckBattlePassLevelUp(int level)
	{
		if (level <= 10)
		{
			if (level != 5)
			{
				return;
			}
			this.GetSetGlobalStats(this._battlePassLv5Stat);
		}
		else if (level != 25)
		{
			return;
		}
	}

	// Token: 0x060002D8 RID: 728 RVA: 0x0000E788 File Offset: 0x0000C988
	private void GetGlobalStats()
	{
		this._globalStatsReceivedCallback = CallResult<GlobalStatsReceived_t>.Create(delegate(GlobalStatsReceived_t a, bool b)
		{
			long num;
			if (SteamUserStats.GetGlobalStat(this._battlePassLv5Stat, out num))
			{
				Debug.Log(string.Format("Global Stat {0}: {1}", this._battlePassLv5Stat, num));
				return;
			}
			Debug.Log("Global Stat Somthing wrong");
		});
		this._globalStatsReceivedCallback.Set(SteamUserStats.RequestGlobalStats(0), null);
	}

	// Token: 0x060002D9 RID: 729 RVA: 0x0000E7B4 File Offset: 0x0000C9B4
	private void GetSetGlobalStats(string stat)
	{
		this._globalStatsReceivedCallback = CallResult<GlobalStatsReceived_t>.Create(delegate(GlobalStatsReceived_t a, bool b)
		{
			long num;
			if (SteamUserStats.GetGlobalStat(stat, out num))
			{
				this.SetStat(this._battlePassLv5Stat, (int)(num + 1L));
				return;
			}
			Debug.Log("Get Set Global Stat Somthing wrong");
		});
		this._globalStatsReceivedCallback.Set(SteamUserStats.RequestGlobalStats(0), null);
	}

	// Token: 0x040001E0 RID: 480
	private static SteamStatsController _instance;

	// Token: 0x040001E1 RID: 481
	private Kit_GameInformation _game;

	// Token: 0x040001E2 RID: 482
	private Callback<UserStatsReceived_t> _userStatsReceivedCallback;

	// Token: 0x040001E3 RID: 483
	private Callback<UserStatsStored_t> _userStatsStoredCallback;

	// Token: 0x040001E4 RID: 484
	private CallResult<GlobalStatsReceived_t> _globalStatsReceivedCallback;

	// Token: 0x040001E5 RID: 485
	private string _battlePassLv5Stat = "battle_pass_5";

	// Token: 0x040001E6 RID: 486
	private string _battlePassLv10Stat = "battle_pass_10";

	// Token: 0x040001E7 RID: 487
	private string _battlePassLv15Stat = "battle_pass_15";

	// Token: 0x040001E8 RID: 488
	private string _battlePassLv25Stat = "battle_pass_25";

	// Token: 0x040001E9 RID: 489
	private string _battlePassLv50Stat = "battle_pass_50";
}
﻿using System;
using UnityEngine;

// Token: 0x020000F6 RID: 246
public class SteamViewHub : MonoBehaviour
{
	// Token: 0x04000560 RID: 1376
	public SteamFriendsPanel PanelView;

	// Token: 0x04000561 RID: 1377
	public SteamFriendsInGroupPanel GroupPanelView;

	// Token: 0x04000562 RID: 1378
	public SteamPlayerInfoView PlayerInfoView;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using Steamworks;
using UnityEngine;
using UnityEngine.Events;

// Token: 0x020000BD RID: 189
public class StoreController
{
	// Token: 0x1700005A RID: 90
	// (get) Token: 0x0600049D RID: 1181 RVA: 0x000156D1 File Offset: 0x000138D1
	public static StoreController Instance
	{
		get
		{
			if (StoreController._instance == null)
			{
				StoreController._instance = new StoreController();
			}
			return StoreController._instance;
		}
	}

	// Token: 0x0600049E RID: 1182 RVA: 0x000156E9 File Offset: 0x000138E9
	public void Initialize(Kit_GameInformation game)
	{
		this._game = game;
		this.LoadAllItems();
	}

	// Token: 0x0600049F RID: 1183 RVA: 0x000156F8 File Offset: 0x000138F8
	public void LoadPurchasedItems()
	{
		Callback<SteamInventoryResultReady_t> readyPurchaseResult = this._readyPurchaseResult;
		if (readyPurchaseResult != null)
		{
			readyPurchaseResult.Dispose();
		}
		Callback<SteamInventoryResultReady_t> rewardInventoryResult = this._rewardInventoryResult;
		if (rewardInventoryResult != null)
		{
			rewardInventoryResult.Dispose();
		}
		SteamItemDetails_t[] itemsInInventory = new SteamItemDetails_t[this._allItemsId.Count];
		uint itemsCount = (uint)itemsInInventory.Length;
		if (this.ItemsDict != null)
		{
			this.ItemsDict.Clear();
		}
		else
		{
			this.ItemsDict = new Dictionary<int, List<ulong>>();
		}
		SteamInventoryResult_t allInventory;
		if (!SteamInventory.GetAllItems(out allInventory))
		{
			Debug.Log("inventory unaviable");
		}
		this._inventoryResult = Callback<SteamInventoryResultReady_t>.Create(delegate(SteamInventoryResultReady_t call)
		{
			if (SteamInventory.GetResultItems(allInventory, null, ref itemsCount))
			{
				SteamItemDetails_t[] itemsInInventory = new SteamItemDetails_t[itemsCount];
				if (SteamInventory.GetResultItems(allInventory, itemsInInventory, ref itemsCount))
				{
					foreach (SteamItemDetails_t steamItemDetails_t in itemsInInventory)
					{
						int steamItemDef = steamItemDetails_t.m_iDefinition.m_SteamItemDef;
						if (!this.openItems.Contains(steamItemDetails_t.m_iDefinition.m_SteamItemDef))
						{
							this.openItems.Add(steamItemDetails_t.m_iDefinition.m_SteamItemDef);
						}
						if (this.ItemsDict.ContainsKey(steamItemDef))
						{
							this.ItemsDict[steamItemDef].Add(steamItemDetails_t.m_itemId.m_SteamItemInstanceID);
						}
						else
						{
							this.ItemsDict.Add(steamItemDef, new List<ulong>
							{
								steamItemDetails_t.m_itemId.m_SteamItemInstanceID
							});
						}
					}
					SteamInventory.DestroyResult(allInventory);
					this.inventoryResultEvent.Invoke();
					this._inventoryResult.Dispose();
					this.CheckConsumableItems();
					return;
				}
			}
			else
			{
				Debug.Log(SteamInventory.GetResultStatus(allInventory));
				SteamItemDetails_t[] itemsInInventory;
				Debug.Log(string.Concat(new object[]
				{
					itemsInInventory.Length,
					"\nitem count : ",
					itemsCount,
					"\nresultHandle оказался недействительным, либо дескриптор результата действий с инвентарём оказался не готов.\nМассив не уместился в pOutItemsArray.\nУ пользователя нет предметов."
				}));
			}
		});
	}

	// Token: 0x060004A0 RID: 1184 RVA: 0x000157A8 File Offset: 0x000139A8
	private void LoadAllItems()
	{
		foreach (Kit_WeaponBase kit_WeaponBase in this._game.allWeapons)
		{
			if (!this._allItemsId.Contains(kit_WeaponBase.id))
			{
				this._allItemsId.Add(kit_WeaponBase.id);
			}
		}
		foreach (Kit_PlayerModelInformation kit_PlayerModelInformation in this._game.allPvpTeams[0].playerModels)
		{
			if (!this._allItemsId.Contains(kit_PlayerModelInformation.id))
			{
				this._allItemsId.Add(kit_PlayerModelInformation.id);
			}
		}
		foreach (AttachmentMonoBehaviour attachmentMonoBehaviour in this._game.allAttachments)
		{
			if (!this._allItemsId.Contains(attachmentMonoBehaviour.ID))
			{
				this._allItemsId.Add(attachmentMonoBehaviour.ID);
			}
		}
		this.LoadPurchasedItems();
	}

	// Token: 0x060004A1 RID: 1185 RVA: 0x00015891 File Offset: 0x00013A91
	public void AwakePurchase(SteamItemDef_t[] itemDef, uint[] itemQuant)
	{
		this.StartPurchase(itemDef, itemQuant);
	}

	// Token: 0x060004A2 RID: 1186 RVA: 0x0001589C File Offset: 0x00013A9C
	public void AwakePurchase(int itemDef, int count)
	{
		SteamItemDef_t[] array = new SteamItemDef_t[1];
		array[0].m_SteamItemDef = itemDef;
		uint[] itemQuant = new uint[]
		{
			(uint)count
		};
		this.StartPurchase(array, itemQuant);
	}

	// Token: 0x060004A3 RID: 1187 RVA: 0x000158D0 File Offset: 0x00013AD0
	private void StartPurchase(SteamItemDef_t[] itemDef, uint[] itemQuant)
	{
		Callback<SteamInventoryResultReady_t> inventoryResult = this._inventoryResult;
		if (inventoryResult != null)
		{
			inventoryResult.Dispose();
		}
		Callback<SteamInventoryResultReady_t> rewardInventoryResult = this._rewardInventoryResult;
		if (rewardInventoryResult != null)
		{
			rewardInventoryResult.Dispose();
		}
		CallResult<SteamInventoryStartPurchaseResult_t>.Create(delegate(SteamInventoryStartPurchaseResult_t cb, bool a)
		{
			Debug.Log("start purchase result: " + cb.m_result);
			Debug.Log("m_ulOrderID: " + cb.m_ulOrderID);
			Debug.Log("m_ulTransID: " + cb.m_ulTransID);
			this._readyPurchaseResult = Callback<SteamInventoryResultReady_t>.Create(delegate(SteamInventoryResultReady_t cal)
			{
				Debug.Log("ready purchase result: " + cal.m_result);
				Debug.Log("ready m_handle: " + cal.m_handle);
				if (cal.m_result == EResult.k_EResultOK)
				{
					this.LoadPurchasedItems();
					Debug.Log("Purchase complette!!!");
				}
			});
		}).Set(SteamInventory.StartPurchase(itemDef, itemQuant, 1U), null);
	}

	// Token: 0x060004A4 RID: 1188 RVA: 0x00015920 File Offset: 0x00013B20
	public void GetRewardItem(int id)
	{
		int[] itemDefid = new int[]
		{
			id
		};
		DataSender.Instance.AddSteamItem(SteamUser.GetSteamID().m_SteamID, itemDefid, delegate(string cb)
		{
			Debug.Log(cb);
			this.LoadPurchasedItems();
		});
	}

	// Token: 0x060004A5 RID: 1189 RVA: 0x00015959 File Offset: 0x00013B59
	public void GetRewardItem(int[] idArray)
	{
		DataSender.Instance.AddSteamItem(SteamUser.GetSteamID().m_SteamID, idArray, delegate(string cb)
		{
			Debug.Log(cb);
			this.LoadPurchasedItems();
		});
	}

	// Token: 0x060004A6 RID: 1190 RVA: 0x0001597C File Offset: 0x00013B7C
	public void GetItemPrice(int id, Action<string> callback)
	{
		string cost = "";
		SteamItemDef_t _itemDef;
		_itemDef.m_SteamItemDef = id;
		string moneyCode;
		CallResult<SteamInventoryRequestPricesResult_t>.Create(delegate(SteamInventoryRequestPricesResult_t cb, bool a)
		{
			moneyCode = cb.m_rgchCurrency.ToString();
			ulong num;
			ulong num2;
			if (SteamInventory.GetItemPrice(_itemDef, out num, out num2))
			{
				cost = string.Format("{0} {1}", num / 100f, moneyCode);
				callback(cost);
				return;
			}
			Debug.Log("ERROR: no price stored for the item definition");
		}).Set(SteamInventory.RequestPrices(), null);
	}

	// Token: 0x060004A7 RID: 1191 RVA: 0x000159BC File Offset: 0x00013BBC
	public void ConsumeItems(List<int> id, List<Action> callback)
	{
		int counter = 0;
		int count = 0;
		List<Action> callbackQueue = new List<Action>();
		for (int i = 0; i < id.Count; i++)
		{
			if (this.ItemsDict.ContainsKey(id[i]))
			{
				count += this.ItemsDict[id[i]].Count;
				for (int j = 0; j < this.ItemsDict[id[i]].Count; j++)
				{
					callbackQueue.Add(callback[i]);
				}
			}
		}
		SteamInventoryResult_t result = default(SteamInventoryResult_t);
		this._consumeResult = Callback<SteamInventoryResultReady_t>.Create(delegate(SteamInventoryResultReady_t a)
		{
			Debug.Log(string.Format("consume result: {0}", a.m_result));
			if (a.m_result == EResult.k_EResultOK)
			{
				int counter;
				Action action = callbackQueue[counter];
				if (action != null)
				{
					action();
				}
				counter = counter;
				counter++;
				if (counter == count)
				{
					this._consumeResult.Dispose();
					SteamInventory.DestroyResult(result);
					return;
				}
			}
			else
			{
				this._consumeResult.Dispose();
				SteamInventory.DestroyResult(result);
			}
		});
		for (int k = 0; k < id.Count; k++)
		{
			for (int l = 0; l < this.ItemsDict[id[k]].Count; l++)
			{
				SteamItemInstanceID_t itemConsume = new SteamItemInstanceID_t(this.ItemsDict[id[k]][l]);
				SteamInventory.ConsumeItem(out result, itemConsume, 1U);
			}
		}
	}

	// Token: 0x060004A8 RID: 1192 RVA: 0x00015AF8 File Offset: 0x00013CF8
	private void CheckConsumableItems()
	{
		List<int> list = new List<int>();
		List<Action> list2 = new List<Action>();
		foreach (int num in this.ItemsDict.Keys)
		{
			for (int i = 0; i < this._game.allSteamConsumableItems.Length; i++)
			{
				if (this._game.allSteamConsumableItems[i].SteamDefId == num)
				{
					list.Add(num);
					list2.Add(new Action(this._game.allSteamConsumableItems[i].ActivateItem));
					Debug.Log(string.Format("Consumable: {0}", num));
				}
			}
		}
		if (list.Count > 0)
		{
			this.ConsumeItems(list, list2);
		}
	}

	// Token: 0x04000386 RID: 902
	private static StoreController _instance;

	// Token: 0x04000387 RID: 903
	public List<int> openItems = new List<int>();

	// Token: 0x04000388 RID: 904
	public Dictionary<int, List<ulong>> ItemsDict;

	// Token: 0x04000389 RID: 905
	private Callback<SteamInventoryResultReady_t> _inventoryResult;

	// Token: 0x0400038A RID: 906
	private Callback<SteamInventoryResultReady_t> _rewardInventoryResult;

	// Token: 0x0400038B RID: 907
	private Callback<SteamInventoryResultReady_t> _readyPurchaseResult;

	// Token: 0x0400038C RID: 908
	private Callback<SteamInventoryResultReady_t> _consumeResult;

	// Token: 0x0400038D RID: 909
	private Kit_GameInformation _game;

	// Token: 0x0400038E RID: 910
	private List<int> _allItemsId = new List<int>();

	// Token: 0x0400038F RID: 911
	public UnityEvent inventoryResultEvent = new UnityEvent();

	// Token: 0x04000390 RID: 912
	public Action OpenItemsLoaded = delegate()
	{
	};
}
﻿using System;
using System.Collections.Generic;
using TMPro;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.UI;

// Token: 0x020000BE RID: 190
public class StoreScreenView : BaseMenuView
{
	// Token: 0x060004AE RID: 1198 RVA: 0x00015D0C File Offset: 0x00013F0C
	private void OnEnable()
	{
		this._backButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(this._previousMenu);
		});
		StoreController.Instance.inventoryResultEvent.AddListener(new UnityAction(this.SetView));
	}

	// Token: 0x060004AF RID: 1199 RVA: 0x00015D48 File Offset: 0x00013F48
	public void SetView()
	{
		foreach (int num in StoreController.Instance.openItems)
		{
			for (int i = 0; i < this.bestPriceItems.Count; i++)
			{
				if (num == this.bestPriceItems[i].GetIdItem())
				{
					this.bestPriceItems[i].Redraw();
				}
			}
		}
	}

	// Token: 0x060004B0 RID: 1200 RVA: 0x00015DD4 File Offset: 0x00013FD4
	private void OnDisable()
	{
		this._backButton.onClick.RemoveAllListeners();
		StoreController.Instance.inventoryResultEvent.RemoveListener(new UnityAction(this.SetView));
	}

	// Token: 0x04000391 RID: 913
	[SerializeField]
	private Button _backButton;

	// Token: 0x04000392 RID: 914
	[SerializeField]
	private Image _descriptionImage;

	// Token: 0x04000393 RID: 915
	[SerializeField]
	private TextMeshProUGUI _descriptionText;

	// Token: 0x04000394 RID: 916
	public List<ShopItem> bestPriceItems;
}
﻿using System;

// Token: 0x0200001B RID: 27
public static class StringExtensions
{
	// Token: 0x060000D9 RID: 217 RVA: 0x000058B8 File Offset: 0x00003AB8
	public static bool IsNullOrWhiteSpace(this string value)
	{
		if (value != null)
		{
			for (int i = 0; i < value.Length; i++)
			{
				if (!char.IsWhiteSpace(value[i]))
				{
					return false;
				}
			}
		}
		return true;
	}
}
﻿using System;

// Token: 0x020000C1 RID: 193
[Serializable]
public class TaskData
{
	// Token: 0x0400039B RID: 923
	public int itemId;

	// Token: 0x0400039C RID: 924
	public DailyTask task;
}
﻿using System;

// Token: 0x020000BF RID: 191
[Serializable]
public class TaskInfo
{
	// Token: 0x04000395 RID: 917
	public int curValue;

	// Token: 0x04000396 RID: 918
	public int targetValue;

	// Token: 0x04000397 RID: 919
	public string description;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using TMPro;
using UnityEngine;

// Token: 0x020000C3 RID: 195
public class TeamDeathmatchRoundHUD : Kit_GameModeHUDBase
{
	// Token: 0x060004B9 RID: 1209 RVA: 0x00015F74 File Offset: 0x00014174
	private void Start()
	{
		this._waitProcess = new WaitProcess();
	}

	// Token: 0x060004BA RID: 1210 RVA: 0x00015F84 File Offset: 0x00014184
	private bool IsArraysEquals(int[] arr1, int[] arr2)
	{
		if (arr1.Length == arr2.Length)
		{
			for (int i = 0; i < arr1.Length; i++)
			{
				if (arr1[i] != arr2[i])
				{
					return false;
				}
			}
			return true;
		}
		return false;
	}

	// Token: 0x060004BB RID: 1211 RVA: 0x00015FB8 File Offset: 0x000141B8
	public override void HUDUpdate(Kit_IngameMain main)
	{
		if (main.currentGameModeBehaviour.AreEnoughPlayersThere(main) || main.hasGameModeStarted)
		{
			this.roundedRestSeconds = Mathf.CeilToInt(main.timer);
			this.displaySeconds = this.roundedRestSeconds % 60;
			this.displayMinutes = this.roundedRestSeconds / 60;
			this.timer.text = string.Format("{0:00} : {1:00}", this.displayMinutes, this.displaySeconds);
			this.timer.enabled = true;
			this.SetElements(main);
			return;
		}
		this.timer.enabled = false;
	}

	// Token: 0x060004BC RID: 1212 RVA: 0x00016058 File Offset: 0x00014258
	private void SetElements(Kit_IngameMain main)
	{
		if (main.currentGameModeRuntimeData != null && main.currentGameModeRuntimeData.GetType() == typeof(TeamDeathmatchRoundsRuntimeData))
		{
			TeamDeathmatchRoundsRuntimeData drd = main.currentGameModeRuntimeData as TeamDeathmatchRoundsRuntimeData;
			this.SetRoundComplete(main, drd);
		}
	}

	// Token: 0x060004BD RID: 1213 RVA: 0x000160A0 File Offset: 0x000142A0
	private void SetPoints(Kit_IngameMain main, TeamDeathmatchRoundsRuntimeData drd)
	{
		if (this.pointsActive.Count == 0)
		{
			for (int i = 0; i < Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, main.currentGameModeBehaviour.maximumAmountOfTeams); i++)
			{
				GameObject gameObject = UnityEngine.Object.Instantiate<GameObject>(this.pointsPrefab, this.pointsGo, false);
				this.pointsActive.Add(gameObject.GetComponentInChildren<TextMeshProUGUI>());
				this.pointsActive[i].color = main.gameInformation.allPvpTeams[i].teamColor;
			}
		}
		if (this.SuperPointsActive.Count == 0)
		{
			for (int j = 0; j < Mathf.Clamp(main.gameInformation.allPvpTeams.Length, 0, main.currentGameModeBehaviour.maximumAmountOfTeams); j++)
			{
				GameObject gameObject2 = UnityEngine.Object.Instantiate<GameObject>(this.pointsPrefab, this.pointsSuperGo, false);
				this.SuperPointsActive.Add(gameObject2.GetComponentInChildren<TextMeshProUGUI>());
				this.SuperPointsActive[j].color = main.gameInformation.allPvpTeams[j].teamColor;
			}
		}
		for (int k = 0; k < drd.teamPoints.Length; k++)
		{
			this.pointsActive[k].text = drd.teamPoints[k].ToString();
		}
	}

	// Token: 0x060004BE RID: 1214 RVA: 0x000161E4 File Offset: 0x000143E4
	private void SetRoundComplete(Kit_IngameMain main, TeamDeathmatchRoundsRuntimeData drd)
	{
		if (!this.IsArraysEquals(this.oldData, drd.teamPoints))
		{
			this._roundCompleteAnimation.ShowRoundComplete();
			for (int i = 0; i < this.oldData.Length; i++)
			{
				this.oldData[i] = drd.teamPoints[i];
			}
		}
	}

	// Token: 0x0400039E RID: 926
	[SerializeField]
	private TextMeshProUGUI _roundCompleteText;

	// Token: 0x0400039F RID: 927
	[SerializeField]
	private RoundCompleteAnimation _roundCompleteAnimation;

	// Token: 0x040003A0 RID: 928
	public TextMeshProUGUI timer;

	// Token: 0x040003A1 RID: 929
	public RectTransform pointsGo;

	// Token: 0x040003A2 RID: 930
	public RectTransform pointsSuperGo;

	// Token: 0x040003A3 RID: 931
	public GameObject pointsPrefab;

	// Token: 0x040003A4 RID: 932
	public List<TextMeshProUGUI> pointsActive = new List<TextMeshProUGUI>();

	// Token: 0x040003A5 RID: 933
	public List<TextMeshProUGUI> SuperPointsActive = new List<TextMeshProUGUI>();

	// Token: 0x040003A6 RID: 934
	private int roundedRestSeconds;

	// Token: 0x040003A7 RID: 935
	private int displaySeconds;

	// Token: 0x040003A8 RID: 936
	private int displayMinutes;

	// Token: 0x040003A9 RID: 937
	private int[] oldData = new int[2];

	// Token: 0x040003AA RID: 938
	private WaitProcess _waitProcess;

	// Token: 0x040003AB RID: 939
	private float _waitTime = 5f;
}
﻿using System;

// Token: 0x020000B4 RID: 180
public class TeamDeathmatchRoundsRuntimeData : ITeamGameMode
{
	// Token: 0x1700004F RID: 79
	// (get) Token: 0x06000427 RID: 1063 RVA: 0x00012100 File Offset: 0x00010300
	public int[] TeamPoints
	{
		get
		{
			return this.teamPoints;
		}
	}

	// Token: 0x04000327 RID: 807
	public int[] teamPoints;
}
﻿using System;
using TMPro;
using UnityEngine;

// Token: 0x020000ED RID: 237
public class TeamScoreboardView : MonoBehaviour
{
	// Token: 0x04000535 RID: 1333
	public Transform EntriesGo;

	// Token: 0x04000536 RID: 1334
	public TextMeshProUGUI ScoreText;
}
﻿using System;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x020000F8 RID: 248
public class TEstInput : MonoBehaviour
{
	// Token: 0x06000635 RID: 1589 RVA: 0x0001D414 File Offset: 0x0001B614
	private void Update()
	{
		if (Input.GetKeyDown(KeyCode.Q))
		{
			BattlePassController.Instance.TestExp();
		}
		if (Input.GetKeyDown(KeyCode.E))
		{
			BattlePassController.Instance.GetPremium();
		}
		if (Input.GetKeyDown(KeyCode.Z))
		{
			StoreController instance = StoreController.Instance;
			List<int> list = new List<int>();
			list.Add(10000);
			List<Action> list2 = new List<Action>();
			list2.Add(delegate
			{
				Debug.Log("Consume Item has been made");
			});
			instance.ConsumeItems(list, list2);
		}
	}
}
﻿using System;

// Token: 0x02000097 RID: 151
public class TestServer
{
	// Token: 0x06000389 RID: 905 RVA: 0x00005F78 File Offset: 0x00004178
	public void UserRegistration()
	{
	}

	// Token: 0x0600038A RID: 906 RVA: 0x00005F78 File Offset: 0x00004178
	public void GetUser()
	{
	}

	// Token: 0x0600038B RID: 907 RVA: 0x00005F78 File Offset: 0x00004178
	public void SetCarHistory()
	{
	}

	// Token: 0x0600038C RID: 908 RVA: 0x00005F78 File Offset: 0x00004178
	public void SetTestUser()
	{
	}

	// Token: 0x0600038D RID: 909 RVA: 0x00005F78 File Offset: 0x00004178
	public void PushNotification()
	{
	}
}
﻿using System;
using TMPro;
using UnityEngine;

// Token: 0x0200010A RID: 266
public class TextAnimatedMessageView : MonoBehaviour
{
	// Token: 0x060006BA RID: 1722 RVA: 0x0001FA36 File Offset: 0x0001DC36
	private void OnEnable()
	{
		this._lerpProcess = new LerpProcess();
		this._waitProcess = new WaitProcess();
	}

	// Token: 0x060006BB RID: 1723 RVA: 0x0001FA50 File Offset: 0x0001DC50
	public void ShowMessage(string message)
	{
		this.StopProcess();
		this._text.text = message;
		this._lerpProcess.StartProcess(delegate(float t)
		{
			this._rectTransform.localScale = Vector2.Lerp(this._startScale, this._targetScale, t);
			this._canvasGroup.alpha = Mathf.Lerp(0f, 1f, t);
		}, this.duration);
		this._waitProcess.StartProcess(delegate
		{
			this._canvasGroup.alpha = 0f;
		}, 2f * this.duration);
	}

	// Token: 0x060006BC RID: 1724 RVA: 0x0001FAAF File Offset: 0x0001DCAF
	private void StopProcess()
	{
		this._lerpProcess.EndProcess();
		this._waitProcess.EndProcess();
	}

	// Token: 0x040005FE RID: 1534
	[SerializeField]
	private TextMeshProUGUI _text;

	// Token: 0x040005FF RID: 1535
	[SerializeField]
	private RectTransform _rectTransform;

	// Token: 0x04000600 RID: 1536
	[SerializeField]
	private CanvasGroup _canvasGroup;

	// Token: 0x04000601 RID: 1537
	[SerializeField]
	private Vector2 _startScale;

	// Token: 0x04000602 RID: 1538
	[SerializeField]
	private Vector2 _targetScale;

	// Token: 0x04000603 RID: 1539
	[SerializeField]
	private float duration;

	// Token: 0x04000604 RID: 1540
	private LerpProcess _lerpProcess;

	// Token: 0x04000605 RID: 1541
	private WaitProcess _waitProcess;
}
﻿using System;
using System.Collections.Generic;
using PolyAndCode.UI;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000FB RID: 251
public class TournamensListScreenView : BaseMenuView, IRecyclableScrollRectDataSource
{
	// Token: 0x0600063B RID: 1595 RVA: 0x0001D579 File Offset: 0x0001B779
	private void OnEnable()
	{
		this._backButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(this._previousMenu);
		});
		this._createTournamentButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(MenuType.TournamentCreate);
		});
	}

	// Token: 0x0600063C RID: 1596 RVA: 0x0001D5B3 File Offset: 0x0001B7B3
	private void OnDisable()
	{
		this._backButton.onClick.RemoveAllListeners();
		this._createTournamentButton.onClick.RemoveAllListeners();
	}

	// Token: 0x0600063D RID: 1597 RVA: 0x0001D5D5 File Offset: 0x0001B7D5
	public override void ShowMenu(bool isAnimated)
	{
		base.ShowMenu(isAnimated);
		this._updateList();
	}

	// Token: 0x0600063E RID: 1598 RVA: 0x0001D5E9 File Offset: 0x0001B7E9
	public void ViewInit(Action<int> onTournamentChoose, Action updateList, Action<int, bool> filterChanged)
	{
		this._onTournamentChoose = onTournamentChoose;
		this._updateList = updateList;
		this._tournamentFilterView.ViewInit(filterChanged);
		this._recyclableScrollRect.Initialize(this);
	}

	// Token: 0x0600063F RID: 1599 RVA: 0x0001D611 File Offset: 0x0001B811
	public void SetView(List<TournamentModel> tournaments)
	{
		this.SetTournaments(tournaments);
	}

	// Token: 0x06000640 RID: 1600 RVA: 0x0001D61C File Offset: 0x0001B81C
	private void ClearContent()
	{
		if (this._tournaments != null)
		{
			for (int i = 0; i < this._tournaments.Count; i++)
			{
				UnityEngine.Object.Destroy(this._tournaments[i].gameObject);
			}
		}
		this._tournaments = new List<TournamentVIew>();
	}

	// Token: 0x06000641 RID: 1601 RVA: 0x0001D668 File Offset: 0x0001B868
	private void SetTournaments(List<TournamentModel> tournaments)
	{
		if (this._tournamentsModels.Count != tournaments.Count)
		{
			this._tournamentsModels = tournaments;
			this._recyclableScrollRect.ReloadData();
		}
	}

	// Token: 0x06000642 RID: 1602 RVA: 0x0001D68F File Offset: 0x0001B88F
	public int GetItemCount()
	{
		return this._tournamentsModels.Count;
	}

	// Token: 0x06000643 RID: 1603 RVA: 0x0001D69C File Offset: 0x0001B89C
	public void SetCell(ICell cell, int index)
	{
		(cell as TournamentVIew).ViewInit(delegate(int tournament)
		{
			this._onTournamentChoose(tournament);
			this._menuController.SwitchMenu(MenuType.TournamentOverview);
		}, this._tournamentsModels[index]);
	}

	// Token: 0x0400056A RID: 1386
	[SerializeField]
	private Button _createTournamentButton;

	// Token: 0x0400056B RID: 1387
	[SerializeField]
	private Button _backButton;

	// Token: 0x0400056C RID: 1388
	[SerializeField]
	private RectTransform _tournamentViewPrefub;

	// Token: 0x0400056D RID: 1389
	[SerializeField]
	private RectTransform _tournamentsContent;

	// Token: 0x0400056E RID: 1390
	[SerializeField]
	private TournamentListFilterView _tournamentFilterView;

	// Token: 0x0400056F RID: 1391
	[SerializeField]
	private RecyclableScrollRect _recyclableScrollRect;

	// Token: 0x04000570 RID: 1392
	private List<TournamentVIew> _tournaments;

	// Token: 0x04000571 RID: 1393
	private List<TournamentModel> _tournamentsModels = new List<TournamentModel>();

	// Token: 0x04000572 RID: 1394
	private Action<int> _onTournamentChoose = delegate(int <p0>)
	{
	};

	// Token: 0x04000573 RID: 1395
	private Action _updateList = delegate()
	{
	};
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000FC RID: 252
public class TournamentCreateScreenView : BaseMenuView
{
	// Token: 0x06000648 RID: 1608 RVA: 0x0001D756 File Offset: 0x0001B956
	private void OnEnable()
	{
		this._backButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(this._previousMenu);
		});
		this._createTournamentButton.onClick.AddListener(delegate()
		{
			this.CreateNewTournament();
			this._menuController.SwitchMenu(this._previousMenu);
		});
	}

	// Token: 0x06000649 RID: 1609 RVA: 0x0001D790 File Offset: 0x0001B990
	private void OnDisable()
	{
		this._backButton.onClick.RemoveAllListeners();
		this._createTournamentButton.onClick.RemoveAllListeners();
	}

	// Token: 0x0600064A RID: 1610 RVA: 0x0001D7B4 File Offset: 0x0001B9B4
	public void ViewInit(Kit_GameInformation game, Action<TournamentModel> createNewTournament)
	{
		this._game = game;
		this._createNewTournament = createNewTournament;
		if (this._hostGameScreenController == null)
		{
			this._hostGameScreenController = new HostGameScreenController(this._game, this.GetRoundGameModes(), false);
		}
		this._gameSettingsView.ViewInit(this._hostGameScreenController, this._game);
		this._tournamentCreatePanelView.ViewInit(game);
	}

	// Token: 0x0600064B RID: 1611 RVA: 0x0001D814 File Offset: 0x0001BA14
	private void CreateNewTournament()
	{
		TournamentModel tournament = this._tournamentCreatePanelView.GetTournament(this._hostGameScreenController.HostGameData);
		this._createNewTournament(tournament);
	}

	// Token: 0x0600064C RID: 1612 RVA: 0x0001D844 File Offset: 0x0001BA44
	private List<int> GetRoundGameModes()
	{
		List<int> list = new List<int>();
		Kit_PvP_GameModeBase[] allPvpGameModes = this._game.allPvpGameModes;
		for (int i = 0; i < allPvpGameModes.Length; i++)
		{
			if (allPvpGameModes[i] is IRounds)
			{
				list.Add(i);
			}
		}
		return list;
	}

	// Token: 0x04000574 RID: 1396
	[SerializeField]
	private TournamentsCreatePanelView _tournamentCreatePanelView;

	// Token: 0x04000575 RID: 1397
	[SerializeField]
	private GameSettingsView _gameSettingsView;

	// Token: 0x04000576 RID: 1398
	[SerializeField]
	private Button _backButton;

	// Token: 0x04000577 RID: 1399
	[SerializeField]
	private Button _createTournamentButton;

	// Token: 0x04000578 RID: 1400
	private HostGameScreenController _hostGameScreenController;

	// Token: 0x04000579 RID: 1401
	private Kit_GameInformation _game;

	// Token: 0x0400057A RID: 1402
	private Action<TournamentModel> _createNewTournament;
}
﻿using System;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000FD RID: 253
public class TournamentListFilterView : MonoBehaviour
{
	// Token: 0x06000650 RID: 1616 RVA: 0x0001D89C File Offset: 0x0001BA9C
	private void OnEnable()
	{
		this._nowToggle.onValueChanged.AddListener(delegate(bool v)
		{
			this._onToggleChanged(0, v);
		});
		this._endedToggle.onValueChanged.AddListener(delegate(bool v)
		{
			this._onToggleChanged(1, v);
		});
		this._enabledToggle.onValueChanged.AddListener(delegate(bool v)
		{
			this._onToggleChanged(2, v);
		});
		this._participationToggle.onValueChanged.AddListener(delegate(bool v)
		{
			this._onToggleChanged(3, v);
		});
	}

	// Token: 0x06000651 RID: 1617 RVA: 0x0001D91C File Offset: 0x0001BB1C
	private void OnDisable()
	{
		this._nowToggle.onValueChanged.RemoveAllListeners();
		this._endedToggle.onValueChanged.RemoveAllListeners();
		this._enabledToggle.onValueChanged.RemoveAllListeners();
		this._participationToggle.onValueChanged.RemoveAllListeners();
	}

	// Token: 0x06000652 RID: 1618 RVA: 0x0001D969 File Offset: 0x0001BB69
	public void ViewInit(Action<int, bool> onToggleChanged)
	{
		this._onToggleChanged = onToggleChanged;
	}

	// Token: 0x0400057B RID: 1403
	[SerializeField]
	private Toggle _nowToggle;

	// Token: 0x0400057C RID: 1404
	[SerializeField]
	private Toggle _endedToggle;

	// Token: 0x0400057D RID: 1405
	[SerializeField]
	private Toggle _enabledToggle;

	// Token: 0x0400057E RID: 1406
	[SerializeField]
	private Toggle _participationToggle;

	// Token: 0x0400057F RID: 1407
	private Action<int, bool> _onToggleChanged = delegate(int <p0>, bool <p1>)
	{
	};
}
﻿using System;

// Token: 0x0200008F RID: 143
public class TournamentMatch
{
	// Token: 0x040002A1 RID: 673
	public string RoomName;

	// Token: 0x040002A2 RID: 674
	public TournamentTeam[] Teams;

	// Token: 0x040002A3 RID: 675
	public bool IsEnded;

	// Token: 0x040002A4 RID: 676
	public bool IsStarted;
}
﻿using System;

// Token: 0x02000090 RID: 144
[Serializable]
public class TournamentMember
{
	// Token: 0x040002A5 RID: 677
	public string Name;

	// Token: 0x040002A6 RID: 678
	public ulong SteamID;
}
﻿using System;
using TMPro;
using UnityEngine;

// Token: 0x020000FE RID: 254
public class TournamentMemberView : MonoBehaviour
{
	// Token: 0x06000658 RID: 1624 RVA: 0x0001D9DB File Offset: 0x0001BBDB
	public void SetView(string name)
	{
		this._memberNameText.text = (string.IsNullOrEmpty(name) ? "Open" : name);
	}

	// Token: 0x04000580 RID: 1408
	[SerializeField]
	private TextMeshProUGUI _memberNameText;
}
﻿using System;
using System.Collections.Generic;

// Token: 0x02000091 RID: 145
public class TournamentModel
{
	// Token: 0x040002A7 RID: 679
	public int ID;

	// Token: 0x040002A8 RID: 680
	public string Name;

	// Token: 0x040002A9 RID: 681
	public int Date;

	// Token: 0x040002AA RID: 682
	public HostGameData HostGameData;

	// Token: 0x040002AB RID: 683
	public ulong[][] Members;

	// Token: 0x040002AC RID: 684
	public ulong[] MembersInRound;

	// Token: 0x040002AD RID: 685
	public int MatchPeriod;

	// Token: 0x040002AE RID: 686
	public int TimeInLobby;

	// Token: 0x040002AF RID: 687
	public List<TournamentRound> Rounds;

	// Token: 0x040002B0 RID: 688
	public int TimeBeforeLobby;

	// Token: 0x040002B1 RID: 689
	public int TimeBeforeMatch;

	// Token: 0x040002B2 RID: 690
	public int TicketPrice;

	// Token: 0x040002B3 RID: 691
	public int PrizeFund;

	// Token: 0x040002B4 RID: 692
	public bool IsEnded;

	// Token: 0x040002B5 RID: 693
	public TournamentTeam WinTeam;

	// Token: 0x040002B6 RID: 694
	public bool MatchEnded;

	// Token: 0x040002B7 RID: 695
	public int CurrentRound;

	// Token: 0x040002B8 RID: 696
	public bool Participation;

	// Token: 0x040002B9 RID: 697
	public int RoundCount;

	// Token: 0x040002BA RID: 698
	public int CurrentRoundTime;

	// Token: 0x040002BB RID: 699
	public int Region;

	// Token: 0x040002BC RID: 700
	public int CurrentRoundDate;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using Steamworks;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x020000FF RID: 255
public class TournamentOverviewScreenView : BaseMenuView
{
	// Token: 0x0600065A RID: 1626 RVA: 0x0001D9F8 File Offset: 0x0001BBF8
	private void OnEnable()
	{
		this._backButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(this._previousMenu);
		});
		this._joinButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(MenuType.TournamentParticipation);
			this._onTournamentJoin();
		});
		this._registrationButton.onClick.AddListener(delegate()
		{
			this._tournamentRegistrationView.Show();
		});
	}

	// Token: 0x0600065B RID: 1627 RVA: 0x0001DA59 File Offset: 0x0001BC59
	private void OnDisable()
	{
		this._backButton.onClick.RemoveAllListeners();
		this._joinButton.onClick.RemoveAllListeners();
		this._registrationButton.onClick.RemoveAllListeners();
	}

	// Token: 0x0600065C RID: 1628 RVA: 0x0001DA8C File Offset: 0x0001BC8C
	private void Update()
	{
		if (base.IsActive)
		{
			this._remainingTimeToLobbyAccess = this._remainingTimeToLobbyAccess.Subtract(new TimeSpan(0, 0, 0, 0, (int)(Time.deltaTime * 1000f)));
			this._remainingTimeToMatch = this._remainingTimeToMatch.Subtract(new TimeSpan(0, 0, 0, 0, (int)(Time.deltaTime * 1000f)));
			if (this._remainingTimeToLobbyAccess.TotalSeconds > 0.0)
			{
				this._joinButtonText.text = string.Format("{0:hh':'mm':'ss}", this._remainingTimeToLobbyAccess);
				this._joinButton.enabled = false;
				return;
			}
			this._joinButton.enabled = this._isMember;
			this._joinButtonText.text = this._joinButtonMessage;
		}
	}

	// Token: 0x0600065D RID: 1629 RVA: 0x0001DB55 File Offset: 0x0001BD55
	public void ViewInit(Kit_GameInformation game, Action<string> onCodeEnterd, Action onTournamentJoin)
	{
		this._game = game;
		this._tournamentRegistrationView.ViewInit(onCodeEnterd);
		this._onTournamentJoin = onTournamentJoin;
		this._unixEpoch = new DateTime(1970, 1, 1, 0, 0, 0);
	}

	// Token: 0x0600065E RID: 1630 RVA: 0x0001DB88 File Offset: 0x0001BD88
	public void SetView(TournamentModel tournament)
	{
		this._tournament = tournament;
		this._titleText.text = tournament.Name;
		HostGameData hostGameData = tournament.HostGameData;
		Kit_PvP_GameModeBase kit_PvP_GameModeBase = this._game.allPvpGameModes[hostGameData.GameMode];
		this._gameModeText.text = kit_PvP_GameModeBase.gameModeName;
		this._mapText.text = kit_PvP_GameModeBase.traditionalMaps[hostGameData.Map].mapName;
		this._durationText.text = string.Format("{0} minutes", kit_PvP_GameModeBase.traditionalDurations[hostGameData.Duration] / 60);
		this._playerLimitText.text = string.Format("{0}", kit_PvP_GameModeBase.traditionalPlayerLimits[hostGameData.PlayerLimit]);
		this._playerNeededText.text = string.Format("{0}", kit_PvP_GameModeBase.traditionalPlayerNeeded[hostGameData.PlayerNeeded]);
		this._pingLimitText.text = string.Format("{0}", kit_PvP_GameModeBase.traditionalPingLimits[hostGameData.PingLimit]);
		this._afkLimitText.text = string.Format("{0}", kit_PvP_GameModeBase.traditionalAfkLimits[hostGameData.AFKLimit]);
		this._ticketPriceText.text = string.Format("{0}", tournament.TicketPrice);
		this._prizeFundText.text = string.Format("{0}", tournament.PrizeFund);
		if (kit_PvP_GameModeBase is IRounds)
		{
			IRounds rounds = (IRounds)kit_PvP_GameModeBase;
			this._roundsText.text = string.Format("{0}", rounds.Rounds[hostGameData.RoundCount]);
		}
		else
		{
			this._roundsText.text = string.Format("{0}", 1);
		}
		if (!tournament.MatchEnded)
		{
			this._startTimeText.text = DateTimeOffset.FromUnixTimeSeconds((long)tournament.CurrentRoundDate).UtcDateTime.ToString();
			this._joinButton.gameObject.SetActive(true);
		}
		else
		{
			this._joinButton.gameObject.SetActive(false);
			this._startTimeText.text = "-";
		}
		this._mapImage.sprite = kit_PvP_GameModeBase.traditionalMaps[hostGameData.Map].mapPicture;
		this.SetTournamentMembersViews(tournament);
		this._remainingTimeToLobbyAccess = DateTimeOffset.FromUnixTimeSeconds((long)this._tournament.TimeBeforeLobby).UtcDateTime.Subtract(this._unixEpoch);
		this._remainingTimeToMatch = DateTimeOffset.FromUnixTimeSeconds((long)this._tournament.TimeBeforeMatch).UtcDateTime.Subtract(this._unixEpoch);
		Debug.Log(string.Format("_remainingTimeToMatch: {0}", this._remainingTimeToMatch));
		this.SetJoinButtonByMembers(tournament);
		this.SetRegistrationButton(tournament);
	}

	// Token: 0x0600065F RID: 1631 RVA: 0x0001DE5A File Offset: 0x0001C05A
	public void SwitchToParticipationScreen()
	{
		if (this._joinButton.enabled)
		{
			this._menuController.SwitchMenu(MenuType.TournamentParticipation);
			return;
		}
		this._tournamentRegistrationView.Hide();
	}

	// Token: 0x06000660 RID: 1632 RVA: 0x0001DE82 File Offset: 0x0001C082
	public void ShowMessage(string message)
	{
		this._tournamentRegistrationView.ShowMessage(message);
	}

	// Token: 0x06000661 RID: 1633 RVA: 0x0001DE90 File Offset: 0x0001C090
	public override void HideMenu(bool isAnimated)
	{
		base.HideMenu(isAnimated);
		this._tournamentRegistrationView.Hide();
	}

	// Token: 0x06000662 RID: 1634 RVA: 0x0001DEA4 File Offset: 0x0001C0A4
	private void SetTournamentMembersViews(TournamentModel tournament)
	{
		this.ClearContent();
		if (!tournament.IsEnded)
		{
			this.SetMemberViews(tournament.MembersInRound);
			return;
		}
		this.SetMemberViews(tournament.WinTeam.Players);
	}

	// Token: 0x06000663 RID: 1635 RVA: 0x0001DED4 File Offset: 0x0001C0D4
	private void ClearContent()
	{
		if (this._members != null)
		{
			for (int i = 0; i < this._members.Count; i++)
			{
				UnityEngine.Object.Destroy(this._members[i].gameObject);
			}
			this._members.Clear();
		}
		this._members = new List<TournamentMemberView>();
	}

	// Token: 0x06000664 RID: 1636 RVA: 0x0001DF2C File Offset: 0x0001C12C
	private void SetMemberViews(ulong[] members)
	{
		if (members == null)
		{
			Debug.Log("Members is null");
			return;
		}
		for (int i = 0; i < members.Length; i++)
		{
			RectTransform rectTransform = UnityEngine.Object.Instantiate<RectTransform>(this._memberPrefab, this._content);
			rectTransform.anchoredPosition = new Vector2(rectTransform.anchoredPosition.x, (float)(-(float)i) * this._memberPrefab.sizeDelta.y);
			TournamentMemberView component = rectTransform.GetComponent<TournamentMemberView>();
			this._steamNameController.GetPlayerName(members[i], new Action<string>(component.SetView));
			this._members.Add(component);
		}
		this._content.sizeDelta = new Vector2(this._content.sizeDelta.x, (float)this._members.Count * this._memberPrefab.sizeDelta.y);
	}

	// Token: 0x06000665 RID: 1637 RVA: 0x0001DFFC File Offset: 0x0001C1FC
	private void SetJoinButtonByMembers(TournamentModel tournament)
	{
		ulong[] membersInRound = tournament.MembersInRound;
		for (int i = 0; i < membersInRound.Length; i++)
		{
			if (membersInRound[i] == SteamUser.GetSteamID().m_SteamID)
			{
				this._isMember = true;
				return;
			}
		}
		this._isMember = false;
	}

	// Token: 0x06000666 RID: 1638 RVA: 0x0001E03C File Offset: 0x0001C23C
	private void SetRegistrationButton(TournamentModel tournament)
	{
		bool flag = DateTime.Now > DateTimeOffset.FromUnixTimeSeconds((long)tournament.Date).UtcDateTime;
		bool flag2 = false;
		ulong steamID = SteamUser.GetSteamID().m_SteamID;
		for (int i = 0; i < tournament.MembersInRound.Length; i++)
		{
			if (tournament.MembersInRound[i] == steamID)
			{
				flag2 = true;
				break;
			}
		}
		bool active = !flag && !flag2;
		this._registrationButton.gameObject.SetActive(active);
	}

	// Token: 0x04000581 RID: 1409
	[SerializeField]
	private TextMeshProUGUI _titleText;

	// Token: 0x04000582 RID: 1410
	[SerializeField]
	private TextMeshProUGUI _gameModeText;

	// Token: 0x04000583 RID: 1411
	[SerializeField]
	private TextMeshProUGUI _mapText;

	// Token: 0x04000584 RID: 1412
	[SerializeField]
	private TextMeshProUGUI _durationText;

	// Token: 0x04000585 RID: 1413
	[SerializeField]
	private TextMeshProUGUI _playerLimitText;

	// Token: 0x04000586 RID: 1414
	[SerializeField]
	private TextMeshProUGUI _playerNeededText;

	// Token: 0x04000587 RID: 1415
	[SerializeField]
	private TextMeshProUGUI _pingLimitText;

	// Token: 0x04000588 RID: 1416
	[SerializeField]
	private TextMeshProUGUI _afkLimitText;

	// Token: 0x04000589 RID: 1417
	[SerializeField]
	private TextMeshProUGUI _roundsText;

	// Token: 0x0400058A RID: 1418
	[SerializeField]
	private TextMeshProUGUI _startTimeText;

	// Token: 0x0400058B RID: 1419
	[SerializeField]
	private TextMeshProUGUI _ticketPriceText;

	// Token: 0x0400058C RID: 1420
	[SerializeField]
	private TextMeshProUGUI _prizeFundText;

	// Token: 0x0400058D RID: 1421
	[SerializeField]
	private TextMeshProUGUI _joinButtonText;

	// Token: 0x0400058E RID: 1422
	[SerializeField]
	private Image _mapImage;

	// Token: 0x0400058F RID: 1423
	[SerializeField]
	private RectTransform _content;

	// Token: 0x04000590 RID: 1424
	[SerializeField]
	private RectTransform _memberPrefab;

	// Token: 0x04000591 RID: 1425
	[SerializeField]
	private Button _backButton;

	// Token: 0x04000592 RID: 1426
	[SerializeField]
	private Button _joinButton;

	// Token: 0x04000593 RID: 1427
	[SerializeField]
	private Button _registrationButton;

	// Token: 0x04000594 RID: 1428
	[SerializeField]
	private TournamentRegistrationView _tournamentRegistrationView;

	// Token: 0x04000595 RID: 1429
	private List<TournamentMemberView> _members;

	// Token: 0x04000596 RID: 1430
	private Kit_GameInformation _game;

	// Token: 0x04000597 RID: 1431
	private TournamentModel _tournament;

	// Token: 0x04000598 RID: 1432
	private Action _onTournamentJoin = delegate()
	{
	};

	// Token: 0x04000599 RID: 1433
	private TimeSpan _remainingTimeToLobbyAccess;

	// Token: 0x0400059A RID: 1434
	private TimeSpan _remainingTimeToMatch;

	// Token: 0x0400059B RID: 1435
	private DateTime _unixEpoch;

	// Token: 0x0400059C RID: 1436
	private string _joinButtonMessage = "Участвовать";

	// Token: 0x0400059D RID: 1437
	private bool _isMember;

	// Token: 0x0400059E RID: 1438
	private bool _isMatchInRoundEnded = true;

	// Token: 0x0400059F RID: 1439
	private SteamNamesController _steamNameController = new SteamNamesController();
}
﻿using System;
using System.Collections.Generic;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000100 RID: 256
public class TournamentParticipationScreenView : BaseMenuView
{
	// Token: 0x0600066B RID: 1643 RVA: 0x0001E138 File Offset: 0x0001C338
	private void OnEnable()
	{
		this._backButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(this._previousMenu);
			this._onBackButtonClick();
		});
	}

	// Token: 0x0600066C RID: 1644 RVA: 0x0001E156 File Offset: 0x0001C356
	private void OnDisable()
	{
		this._backButton.onClick.RemoveAllListeners();
	}

	// Token: 0x0600066D RID: 1645 RVA: 0x0001E168 File Offset: 0x0001C368
	private void Update()
	{
		if (base.IsActive)
		{
			if (this._remainingTime.TotalSeconds < 0.0)
			{
				this._timerText.text = "";
				this._isRemainingTimeIsZero = true;
				return;
			}
			this._remainingTime = this._remainingTime.Subtract(new TimeSpan(0, 0, 0, 0, (int)(Time.deltaTime * 1000f)));
			this._timerText.text = string.Format("{0:hh':'mm':'ss}", this._remainingTime);
		}
	}

	// Token: 0x0600066E RID: 1646 RVA: 0x0001E1F1 File Offset: 0x0001C3F1
	public void ViewInit(Action<int, int> onPlayerCellChoose, Action onBackButtonClick, Action<ulong> onCellEnter, Action onTimeOut)
	{
		this._onPlayerCellChoose = onPlayerCellChoose;
		this._onBackButtonClick = onBackButtonClick;
		this._onCellEnter = onCellEnter;
		this._onTimeOut = onTimeOut;
		this._unixEpoch = new DateTime(1970, 1, 1, 0, 0, 0);
		this._isRemainingTimeIsZero = false;
	}

	// Token: 0x0600066F RID: 1647 RVA: 0x0001E22C File Offset: 0x0001C42C
	public void SetView(TournamentModel choosenTournament, string playerName)
	{
		this._choosenTournament = choosenTournament;
		this._playerName = playerName;
		this.ClearContent();
		this.SetTournamentTeamsViewsRounds();
		this.SetTimer();
		this._title.text = this._choosenTournament.Name;
		this._tournamentStage.text = string.Format("{0} {1}", LocalizationController.Instance.GetText("TournamentsParticipationScreen", "Stage"), this._choosenTournament.CurrentRound + 1);
	}

	// Token: 0x06000670 RID: 1648 RVA: 0x0001E2AC File Offset: 0x0001C4AC
	public void SetTimer()
	{
		int timeBeforeMatch = this._choosenTournament.TimeBeforeMatch;
		this._remainingTime = DateTimeOffset.FromUnixTimeSeconds((long)timeBeforeMatch).UtcDateTime.Subtract(this._unixEpoch);
	}

	// Token: 0x06000671 RID: 1649 RVA: 0x0001E2E8 File Offset: 0x0001C4E8
	public void SetPlayerStatisticsView(AccountModel account)
	{
		this._playerStatisticsView.SetView(account);
	}

	// Token: 0x06000672 RID: 1650 RVA: 0x0001E2F8 File Offset: 0x0001C4F8
	private void ClearContent()
	{
		if (this._tournamentsTeamsViews != null)
		{
			for (int i = 0; i < this._tournamentsTeamsViews.Count; i++)
			{
				UnityEngine.Object.Destroy(this._tournamentsTeamsViews[i].gameObject);
			}
		}
		this._tournamentsTeamsViews = new List<TournamentTeamView>();
	}

	// Token: 0x06000673 RID: 1651 RVA: 0x0001E344 File Offset: 0x0001C544
	private void SetTournamentTeamsViewsRounds()
	{
		if (!this._choosenTournament.IsEnded)
		{
			List<TournamentMatch> matches = this._choosenTournament.Rounds[0].Matches;
			for (int i = 0; i < matches.Count; i++)
			{
				for (int j = 0; j < matches[i].Teams.Length; j++)
				{
					TournamentTeamView tournamentTeamView = UnityEngine.Object.Instantiate<TournamentTeamView>(this._tournamentTeamViewPrefab, this._content);
					tournamentTeamView.ViewInit(this._onPlayerCellChoose, this._onCellEnter, new Action(this.HidePlayerStatisticsView), matches[i].Teams[j], i * 2 + j);
					this._tournamentsTeamsViews.Add(tournamentTeamView);
				}
			}
			this._content.sizeDelta = new Vector2(this._content.sizeDelta.x, this._gridLayoutGroup.preferredHeight);
			return;
		}
		TournamentTeamView tournamentTeamView2 = UnityEngine.Object.Instantiate<TournamentTeamView>(this._tournamentTeamViewPrefab, this._content);
		tournamentTeamView2.ViewInit(delegate(int a, int b)
		{
		}, delegate(ulong a)
		{
		}, delegate
		{
		}, this._choosenTournament.WinTeam, 0);
		this._tournamentsTeamsViews.Add(tournamentTeamView2);
	}

	// Token: 0x06000674 RID: 1652 RVA: 0x0001E4AD File Offset: 0x0001C6AD
	private void HidePlayerStatisticsView()
	{
		this._playerStatisticsView.gameObject.SetActive(false);
	}

	// Token: 0x040005A0 RID: 1440
	[SerializeField]
	private Button _backButton;

	// Token: 0x040005A1 RID: 1441
	[SerializeField]
	private RectTransform _content;

	// Token: 0x040005A2 RID: 1442
	[SerializeField]
	private TournamentTeamView _tournamentTeamViewPrefab;

	// Token: 0x040005A3 RID: 1443
	[SerializeField]
	private TextMeshProUGUI _title;

	// Token: 0x040005A4 RID: 1444
	[SerializeField]
	private TextMeshProUGUI _tournamentStage;

	// Token: 0x040005A5 RID: 1445
	[SerializeField]
	private TextMeshProUGUI _timerText;

	// Token: 0x040005A6 RID: 1446
	[SerializeField]
	private GridLayoutGroup _gridLayoutGroup;

	// Token: 0x040005A7 RID: 1447
	[SerializeField]
	private PlayerStatisticsView _playerStatisticsView;

	// Token: 0x040005A8 RID: 1448
	private TournamentModel _choosenTournament;

	// Token: 0x040005A9 RID: 1449
	private string _playerName;

	// Token: 0x040005AA RID: 1450
	private List<TournamentTeamView> _tournamentsTeamsViews;

	// Token: 0x040005AB RID: 1451
	private Action<int, int> _onPlayerCellChoose = delegate(int <p0>, int <p1>)
	{
	};

	// Token: 0x040005AC RID: 1452
	private Action _onBackButtonClick = delegate()
	{
	};

	// Token: 0x040005AD RID: 1453
	private Action<ulong> _onCellEnter = delegate(ulong <p0>)
	{
	};

	// Token: 0x040005AE RID: 1454
	private Action _onTimeOut = delegate()
	{
	};

	// Token: 0x040005AF RID: 1455
	private DateTime _unixEpoch;

	// Token: 0x040005B0 RID: 1456
	private TimeSpan _remainingTime;

	// Token: 0x040005B1 RID: 1457
	private bool _isRemainingTimeIsZero;
}
﻿using System;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000101 RID: 257
public class TournamentRegistrationView : MonoBehaviour
{
	// Token: 0x06000677 RID: 1655 RVA: 0x0001E585 File Offset: 0x0001C785
	private void OnEnable()
	{
		this._cancelButton.onClick.AddListener(delegate()
		{
			this.Hide();
			this._inputField.text = "";
		});
		this._okButton.onClick.AddListener(delegate()
		{
			this._onOkButton(this._inputField.text);
		});
	}

	// Token: 0x06000678 RID: 1656 RVA: 0x0001E5BF File Offset: 0x0001C7BF
	private void OnDisable()
	{
		this._cancelButton.onClick.RemoveAllListeners();
		this._okButton.onClick.RemoveAllListeners();
	}

	// Token: 0x06000679 RID: 1657 RVA: 0x0001E5E1 File Offset: 0x0001C7E1
	public void ViewInit(Action<string> onOkButton)
	{
		this._onOkButton = onOkButton;
		this._lerpProcess = new LerpProcess();
		this.Hide();
	}

	// Token: 0x0600067A RID: 1658 RVA: 0x0001E5FC File Offset: 0x0001C7FC
	public void Show()
	{
		if (!this._canvasGroup.interactable)
		{
			this._canvasGroup.blocksRaycasts = true;
			this._canvasGroup.interactable = true;
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._canvasGroup.alpha = Mathf.Lerp(0f, 1f, t);
			}, this._appearTime);
		}
	}

	// Token: 0x0600067B RID: 1659 RVA: 0x0001E64C File Offset: 0x0001C84C
	public void Hide()
	{
		if (this._canvasGroup.interactable)
		{
			this._messageText.text = "";
			this._canvasGroup.blocksRaycasts = false;
			this._canvasGroup.interactable = false;
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._canvasGroup.alpha = Mathf.Lerp(1f, 0f, t);
			}, this._appearTime);
		}
	}

	// Token: 0x0600067C RID: 1660 RVA: 0x0001E6AB File Offset: 0x0001C8AB
	public void ShowMessage(string message)
	{
		this._messageText.text = message;
	}

	// Token: 0x040005B2 RID: 1458
	[SerializeField]
	private CanvasGroup _canvasGroup;

	// Token: 0x040005B3 RID: 1459
	[SerializeField]
	private TMP_InputField _inputField;

	// Token: 0x040005B4 RID: 1460
	[SerializeField]
	private Button _okButton;

	// Token: 0x040005B5 RID: 1461
	[SerializeField]
	private Button _cancelButton;

	// Token: 0x040005B6 RID: 1462
	[SerializeField]
	private TextMeshProUGUI _messageText;

	// Token: 0x040005B7 RID: 1463
	private float _appearTime = 0.25f;

	// Token: 0x040005B8 RID: 1464
	private LerpProcess _lerpProcess;

	// Token: 0x040005B9 RID: 1465
	private Action<string> _onOkButton;

	// Token: 0x040005BA RID: 1466
	private bool _isActive = true;
}
﻿using System;
using System.Collections.Generic;

// Token: 0x02000092 RID: 146
public class TournamentRound
{
	// Token: 0x040002BD RID: 701
	public List<TournamentMatch> Matches;

	// Token: 0x040002BE RID: 702
	public int Data;

	// Token: 0x040002BF RID: 703
	public bool IsEnded;
}
﻿using System;
using Newtonsoft.Json;
using Steamworks;
using UnityEngine;

// Token: 0x02000069 RID: 105
public class TournamentsController
{
	// Token: 0x1700002A RID: 42
	// (get) Token: 0x060002DC RID: 732 RVA: 0x0000E862 File Offset: 0x0000CA62
	public static TournamentsController Instance
	{
		get
		{
			if (TournamentsController._instance == null)
			{
				TournamentsController._instance = new TournamentsController();
			}
			return TournamentsController._instance;
		}
	}

	// Token: 0x1700002B RID: 43
	// (get) Token: 0x060002DD RID: 733 RVA: 0x0000E87A File Offset: 0x0000CA7A
	// (set) Token: 0x060002DE RID: 734 RVA: 0x0000E882 File Offset: 0x0000CA82
	public bool IsItTournament { get; private set; }

	// Token: 0x1700002C RID: 44
	// (get) Token: 0x060002DF RID: 735 RVA: 0x0000E88B File Offset: 0x0000CA8B
	public TournamentMatch Match
	{
		get
		{
			return this._currentMatch;
		}
	}

	// Token: 0x060002E0 RID: 736 RVA: 0x0000292A File Offset: 0x00000B2A
	private TournamentsController()
	{
	}

	// Token: 0x060002E1 RID: 737 RVA: 0x0000E893 File Offset: 0x0000CA93
	public void Initialize()
	{
		this.IsItTournament = false;
		this._memberID = SteamUser.GetSteamID().m_SteamID;
		this._memberJson = this._memberID.ToString();
	}

	// Token: 0x060002E2 RID: 738 RVA: 0x0000E8C0 File Offset: 0x0000CAC0
	public void SetNewTournament(TournamentModel tournament, Action<string> callback)
	{
		string text = JsonConvert.SerializeObject(tournament);
		Debug.Log("To server: " + text);
		DataSender.Instance.SetNewTournament(text, callback);
	}

	// Token: 0x060002E3 RID: 739 RVA: 0x0000E8F0 File Offset: 0x0000CAF0
	public void GetTournamentsList(Action<string> callback)
	{
		DataSender.Instance.GetTournamentsList(this._memberID, callback);
	}

	// Token: 0x060002E4 RID: 740 RVA: 0x0000E903 File Offset: 0x0000CB03
	public void GetTournamentById(int id, Action<string> callback)
	{
		DataSender.Instance.GetTournamentById(id, this._memberJson, callback);
	}

	// Token: 0x060002E5 RID: 741 RVA: 0x0000E917 File Offset: 0x0000CB17
	public void SetPlayerInCell(int teamIndex, int cellIndex, Action<string> callback)
	{
		DataSender.Instance.SetPlayerInCell(teamIndex, cellIndex, this._memberJson, this._choosenTournament.ID, callback);
	}

	// Token: 0x060002E6 RID: 742 RVA: 0x0000E937 File Offset: 0x0000CB37
	public void CheckTournamentCode(string code, Action<string> callback)
	{
		DataSender.Instance.CheckTournamentCode(this._choosenTournament.ID, this._memberJson, code, callback);
	}

	// Token: 0x060002E7 RID: 743 RVA: 0x0000E956 File Offset: 0x0000CB56
	public void GetParticipationData(Action<string> callback)
	{
		DataSender.Instance.GetTournamentParticipationData(this._choosenTournament.ID, callback);
	}

	// Token: 0x060002E8 RID: 744 RVA: 0x0000E96E File Offset: 0x0000CB6E
	public void GetMatch(int tournamentId, Action<string> callback)
	{
		DataSender.Instance.GetMatch(tournamentId, this._memberJson, callback);
	}

	// Token: 0x060002E9 RID: 745 RVA: 0x0000E982 File Offset: 0x0000CB82
	public void SetChoosenTournament(TournamentModel tournament)
	{
		this._choosenTournament = tournament;
	}

	// Token: 0x060002EA RID: 746 RVA: 0x0000E98B File Offset: 0x0000CB8B
	public void SetCurrentMatch(TournamentMatch match)
	{
		this.IsItTournament = true;
		this._currentMatch = match;
	}

	// Token: 0x060002EB RID: 747 RVA: 0x0000E99C File Offset: 0x0000CB9C
	public void SendEndMatchToServer(int winTeam)
	{
		Debug.Log(string.Format("WinTeam: {0}", winTeam));
		string text = JsonConvert.SerializeObject(this._currentMatch);
		Debug.Log("Match To Server: " + text);
		DataSender.Instance.SendMatchEndToServer(this._choosenTournament.ID, text, winTeam, new Action<string>(this.SendEndMatchToServerCallback));
	}

	// Token: 0x060002EC RID: 748 RVA: 0x0000E9FD File Offset: 0x0000CBFD
	public void GetPlayerStatistics(ulong steamID, Action<string> callback)
	{
		DataSender.Instance.GetStatistics(steamID, callback);
	}

	// Token: 0x060002ED RID: 749 RVA: 0x0000EA0B File Offset: 0x0000CC0B
	public void SendEndMatchToServerCallback(string json)
	{
		Debug.Log("SendEndMatchToServerCallback: " + json);
		this._choosenTournament = JsonConvert.DeserializeObject<TournamentModel>(json);
	}

	// Token: 0x060002EE RID: 750 RVA: 0x0000EA29 File Offset: 0x0000CC29
	public void SetMatchStarted()
	{
		DataSender.Instance.SetMatchStarted(this._currentMatch.RoomName, this._choosenTournament.ID);
	}

	// Token: 0x040001EA RID: 490
	private static TournamentsController _instance;

	// Token: 0x040001EB RID: 491
	private TournamentModel _choosenTournament;

	// Token: 0x040001EC RID: 492
	private TournamentMatch _currentMatch;

	// Token: 0x040001ED RID: 493
	private ulong _memberID;

	// Token: 0x040001EE RID: 494
	private string _memberJson;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using TMPro;
using UnityEngine;

// Token: 0x02000105 RID: 261
public class TournamentsCreatePanelView : MonoBehaviour
{
	// Token: 0x0600068E RID: 1678 RVA: 0x0001EBFC File Offset: 0x0001CDFC
	private void OnEnable()
	{
		this._tournamentTimeInLobbyInputField.onEndEdit.AddListener(delegate(string str)
		{
			this.CheckTimeInLobby(str);
		});
		this._tournamentMatchPeriodInputField.onEndEdit.AddListener(delegate(string str)
		{
			this.CheckTimeInLobby(str);
		});
		this._tournamentTicketPriceInputField.onEndEdit.AddListener(delegate(string str)
		{
			this.CheckTicketPrice();
		});
		this._tournamentDateMinutesInputField.onEndEdit.AddListener(delegate(string str)
		{
			this.CheckData();
		});
		this._tournamentDateHoursInputField.onEndEdit.AddListener(delegate(string str)
		{
			this.CheckData();
		});
		this._tournamentDateDayesInputField.onEndEdit.AddListener(delegate(string str)
		{
			this.CheckData();
		});
		this._tournamentDateMonthsInputField.onEndEdit.AddListener(delegate(string str)
		{
			this.CheckData();
		});
		this._tournamentDateYearsInputField.onEndEdit.AddListener(delegate(string str)
		{
			this.CheckData();
		});
		LocalizationController.Instance.OnLanguageChanged += this.SetView;
	}

	// Token: 0x0600068F RID: 1679 RVA: 0x0001ED00 File Offset: 0x0001CF00
	private void OnDisable()
	{
		this._tournamentTimeInLobbyInputField.onEndEdit.RemoveAllListeners();
		this._tournamentMatchPeriodInputField.onEndEdit.RemoveAllListeners();
		this._tournamentTicketPriceInputField.onEndEdit.RemoveAllListeners();
		this._tournamentDateMinutesInputField.onEndEdit.RemoveAllListeners();
		this._tournamentDateHoursInputField.onEndEdit.RemoveAllListeners();
		this._tournamentDateDayesInputField.onEndEdit.RemoveAllListeners();
		this._tournamentDateMonthsInputField.onEndEdit.RemoveAllListeners();
		this._tournamentDateYearsInputField.onEndEdit.RemoveAllListeners();
		LocalizationController.Instance.OnLanguageChanged -= this.SetView;
	}

	// Token: 0x06000690 RID: 1680 RVA: 0x0001EDA3 File Offset: 0x0001CFA3
	public void ViewInit(Kit_GameInformation game)
	{
		this._game = game;
		this.SetView();
	}

	// Token: 0x06000691 RID: 1681 RVA: 0x0001EDB4 File Offset: 0x0001CFB4
	public TournamentModel GetTournament(HostGameData hostGameData)
	{
		return new TournamentModel
		{
			Name = this._tournamentNameInputField.text,
			Date = this.GetUnixTimeFromInput(),
			HostGameData = hostGameData,
			Members = this.GetMembers(),
			MatchPeriod = int.Parse(this._tournamentMatchPeriodInputField.text) * 60,
			TimeInLobby = int.Parse(this._tournamentTimeInLobbyInputField.text) * 60,
			Rounds = this.GetRounds(),
			TicketPrice = int.Parse(this._tournamentTicketPriceInputField.text),
			PrizeFund = 0,
			IsEnded = false,
			WinTeam = new TournamentTeam(),
			RoundCount = this.GetRoundsCount(),
			Region = this._tournamentRegionDropdown.value
		};
	}

	// Token: 0x06000692 RID: 1682 RVA: 0x0001EE80 File Offset: 0x0001D080
	private void SetView()
	{
		this._tournamentNameInputField.text = LocalizationController.Instance.GetText(this._group, this._tournamentKey) + " 1";
		this._tournamentMatchPeriodInputField.text = this._defaultTournamentMatchPeriod.ToString();
		this._tournamentTimeInLobbyInputField.text = this._defaultTimeInLobby.ToString();
		this._tournamentTicketPriceInputField.text = this._defaultTicketPrice.ToString();
		this._tournamentTeamCountSwitchable.ViewInit(delegate
		{
			this.ChangeTeamCount(-1);
		}, delegate
		{
			this.ChangeTeamCount(1);
		});
		this._tournamentTeamCountSwitchable.UpdateValue(this._teamCounts[this._currentTeamCount].ToString());
		this.InitRegionDropdown();
		this.InitPlayersCountDropdown();
		TimeZoneInfo destinationTimeZone = TimeZoneInfo.FindSystemTimeZoneById("Russian Standard Time");
		DateTime dateTime = TimeZoneInfo.ConvertTime(DateTime.Now, destinationTimeZone);
		this._tournamentDateMinutesInputField.text = dateTime.Minute.ToString();
		this._tournamentDateHoursInputField.text = dateTime.Hour.ToString();
		this._tournamentDateDayesInputField.text = dateTime.Day.ToString();
		this._tournamentDateMonthsInputField.text = dateTime.Month.ToString();
		this._tournamentDateYearsInputField.text = dateTime.Year.ToString();
	}

	// Token: 0x06000693 RID: 1683 RVA: 0x0001EFE4 File Offset: 0x0001D1E4
	private void InitRegionDropdown()
	{
		Kit_RegionInformation[] allRegions = this._game.allRegions;
		List<string> list = new List<string>(allRegions.Length);
		for (int i = 0; i < allRegions.Length; i++)
		{
			list.Add(string.Concat(new string[]
			{
				allRegions[i].regionName,
				" ",
				allRegions[i].serverLocation,
				" ",
				allRegions[i].token
			}));
		}
		this._tournamentRegionDropdown.ClearOptions();
		this._tournamentRegionDropdown.AddOptions(list);
		this._tournamentRegionDropdown.value = this._defaultRegionIndex;
	}

	// Token: 0x06000694 RID: 1684 RVA: 0x0001F080 File Offset: 0x0001D280
	private void InitPlayersCountDropdown()
	{
		List<string> list = new List<string>(this._defaultTeamSizeVariantCount);
		list.Add("1 " + LocalizationController.Instance.GetText(this._group, this._playerKey));
		for (int i = 1; i < this._defaultTeamSizeVariantCount - 1; i++)
		{
			list.Add(string.Format("{0} {1}", i + 1, LocalizationController.Instance.GetText(this._group, this._playersKey1)));
		}
		list.Add(string.Format("{0} {1}", this._defaultTeamSizeVariantCount, LocalizationController.Instance.GetText(this._group, this._playersKey2)));
		this._tournamentTeamSizeDropdown.ClearOptions();
		this._tournamentTeamSizeDropdown.AddOptions(list);
	}

	// Token: 0x06000695 RID: 1685 RVA: 0x0001F148 File Offset: 0x0001D348
	private int GetUnixTimeFromInput()
	{
		int minute = int.Parse(this._tournamentDateMinutesInputField.text);
		int hour = int.Parse(this._tournamentDateHoursInputField.text);
		int day = int.Parse(this._tournamentDateDayesInputField.text);
		int month = int.Parse(this._tournamentDateMonthsInputField.text);
		int year = int.Parse(this._tournamentDateYearsInputField.text);
		if (new DateTime(year, month, day, hour, minute, 0) <= DateTime.Now)
		{
			year = DateTime.Now.Year;
			month = DateTime.Now.Month;
			day = DateTime.Now.Day;
			hour = DateTime.Now.Hour;
			minute = DateTime.Now.Minute + this._defaultMinDelay;
		}
		DateTimeOffset dateTimeOffset = new DateTimeOffset(year, month, day, hour, minute, 0, default(TimeSpan));
		return (int)dateTimeOffset.ToUnixTimeSeconds();
	}

	// Token: 0x06000696 RID: 1686 RVA: 0x0001F238 File Offset: 0x0001D438
	private ulong[][] GetMembers()
	{
		int num = this._teamCounts[this._currentTeamCount];
		int num2 = this._tournamentTeamSizeDropdown.value + 1;
		ulong[][] array = new ulong[this._currentTeamCount + 1][];
		for (int i = 0; i < array.Length; i++)
		{
			int num3 = (int)Mathf.Pow(2f, (float)(this._currentTeamCount - i)) * 2 * num2;
			array[i] = new ulong[num3];
		}
		return array;
	}

	// Token: 0x06000697 RID: 1687 RVA: 0x0001F2A0 File Offset: 0x0001D4A0
	private void ChangeTeamCount(int value)
	{
		this._currentTeamCount += value;
		if (this._currentTeamCount >= this._teamCounts.Length)
		{
			this._currentTeamCount = 0;
		}
		else if (this._currentTeamCount < 0)
		{
			this._currentTeamCount = this._teamCounts.Length - 1;
		}
		this._tournamentTeamCountSwitchable.UpdateValue(this._teamCounts[this._currentTeamCount].ToString());
	}

	// Token: 0x06000698 RID: 1688 RVA: 0x0001F310 File Offset: 0x0001D510
	private List<TournamentRound> GetRounds()
	{
		int num = this._currentTeamCount + 1;
		List<TournamentRound> list = new List<TournamentRound>(num);
		for (int i = 0; i < num; i++)
		{
			TournamentRound item = new TournamentRound
			{
				Matches = this.GetMatches(i),
				IsEnded = false
			};
			list.Add(item);
		}
		list[0].Data = this.GetUnixTimeFromInput();
		return list;
	}

	// Token: 0x06000699 RID: 1689 RVA: 0x0001F370 File Offset: 0x0001D570
	private List<TournamentMatch> GetMatches(int round)
	{
		int length = this._tournamentTeamSizeDropdown.value + 1;
		int num = (int)Mathf.Pow(2f, (float)(this._currentTeamCount - round));
		List<TournamentMatch> list = new List<TournamentMatch>(num);
		for (int i = 0; i < num; i++)
		{
			TournamentMatch item = new TournamentMatch
			{
				RoomName = this.GetRandomKey(),
				Teams = new TournamentTeam[]
				{
					new TournamentTeam
					{
						Name = string.Format("Команда {0}", 2 * i + 1),
						Players = this.GetPlayers(length)
					},
					new TournamentTeam
					{
						Name = string.Format("Команда {0}", 2 * i + 2),
						Players = this.GetPlayers(length)
					}
				},
				IsEnded = false,
				IsStarted = false
			};
			list.Add(item);
		}
		return list;
	}

	// Token: 0x0600069A RID: 1690 RVA: 0x0001F458 File Offset: 0x0001D658
	private string GetRandomKey()
	{
		int length = this._baseString.Length;
		List<int> list = new List<int>(10);
		for (int i = 0; i < 10; i++)
		{
			list.Add(UnityEngine.Random.Range(0, length));
		}
		return string.Format("{0}{1}{2}{3}{4}{5}{6}{7}{8}{9}", new object[]
		{
			this._baseString[list[0]],
			this._baseString[list[1]],
			this._baseString[list[2]],
			this._baseString[list[3]],
			this._baseString[list[4]],
			this._baseString[list[5]],
			this._baseString[list[6]],
			this._baseString[list[7]],
			this._baseString[list[8]],
			this._baseString[list[9]]
		});
	}

	// Token: 0x0600069B RID: 1691 RVA: 0x0001F5AA File Offset: 0x0001D7AA
	private ulong[] GetPlayers(int length)
	{
		return new ulong[length];
	}

	// Token: 0x0600069C RID: 1692 RVA: 0x0001F5B2 File Offset: 0x0001D7B2
	private int GetRoundsCount()
	{
		return this._currentTeamCount + 1;
	}

	// Token: 0x0600069D RID: 1693 RVA: 0x0001F5BC File Offset: 0x0001D7BC
	private void CheckTimeInLobby(string value)
	{
		int num = int.Parse(this._tournamentMatchPeriodInputField.text);
		if (int.Parse(this._tournamentTimeInLobbyInputField.text) > num)
		{
			this._tournamentTimeInLobbyInputField.text = num.ToString();
		}
	}

	// Token: 0x0600069E RID: 1694 RVA: 0x0001F5FF File Offset: 0x0001D7FF
	private void CheckTicketPrice()
	{
		if (int.Parse(this._tournamentTicketPriceInputField.text) < 0)
		{
			this._tournamentTicketPriceInputField.text = "0";
		}
	}

	// Token: 0x0600069F RID: 1695 RVA: 0x0001F624 File Offset: 0x0001D824
	private void CheckData()
	{
		int value = int.Parse(this._tournamentDateMinutesInputField.text);
		int value2 = int.Parse(this._tournamentDateHoursInputField.text);
		int value3 = int.Parse(this._tournamentDateDayesInputField.text);
		int num = int.Parse(this._tournamentDateMonthsInputField.text);
		int year = int.Parse(this._tournamentDateYearsInputField.text);
		num = Mathf.Clamp(num, 1, 12);
		value3 = Mathf.Clamp(value3, 1, DateTime.DaysInMonth(year, num));
		value2 = Mathf.Clamp(value2, 0, 23);
		value = Mathf.Clamp(value, 0, 59);
		this._tournamentDateYearsInputField.text = year.ToString();
		this._tournamentDateMonthsInputField.text = num.ToString();
		this._tournamentDateDayesInputField.text = value3.ToString();
		this._tournamentDateHoursInputField.text = value2.ToString();
		this._tournamentDateMinutesInputField.text = value.ToString();
	}

	// Token: 0x040005D1 RID: 1489
	[SerializeField]
	private TMP_InputField _tournamentNameInputField;

	// Token: 0x040005D2 RID: 1490
	[SerializeField]
	private TMP_InputField _tournamentMatchPeriodInputField;

	// Token: 0x040005D3 RID: 1491
	[SerializeField]
	private TMP_InputField _tournamentTimeInLobbyInputField;

	// Token: 0x040005D4 RID: 1492
	[SerializeField]
	private TMP_InputField _tournamentTicketPriceInputField;

	// Token: 0x040005D5 RID: 1493
	[SerializeField]
	private HostSwitchableSettingView _tournamentTeamCountSwitchable;

	// Token: 0x040005D6 RID: 1494
	[SerializeField]
	private TMP_Dropdown _tournamentTeamSizeDropdown;

	// Token: 0x040005D7 RID: 1495
	[SerializeField]
	private TMP_Dropdown _tournamentRegionDropdown;

	// Token: 0x040005D8 RID: 1496
	[SerializeField]
	private TMP_InputField _tournamentDateMinutesInputField;

	// Token: 0x040005D9 RID: 1497
	[SerializeField]
	private TMP_InputField _tournamentDateHoursInputField;

	// Token: 0x040005DA RID: 1498
	[SerializeField]
	private TMP_InputField _tournamentDateDayesInputField;

	// Token: 0x040005DB RID: 1499
	[SerializeField]
	private TMP_InputField _tournamentDateMonthsInputField;

	// Token: 0x040005DC RID: 1500
	[SerializeField]
	private TMP_InputField _tournamentDateYearsInputField;

	// Token: 0x040005DD RID: 1501
	private int _defaultTournamentMatchPeriod = 15;

	// Token: 0x040005DE RID: 1502
	private int[] _teamCounts = new int[]
	{
		2,
		4,
		8,
		16,
		32
	};

	// Token: 0x040005DF RID: 1503
	private int _currentTeamCount = 1;

	// Token: 0x040005E0 RID: 1504
	private int _defaultTimeInLobby = 10;

	// Token: 0x040005E1 RID: 1505
	private int _defaultTicketPrice = 1000;

	// Token: 0x040005E2 RID: 1506
	private int _defaultRegionIndex = 6;

	// Token: 0x040005E3 RID: 1507
	private int _defaultTeamSizeVariantCount = 5;

	// Token: 0x040005E4 RID: 1508
	private Kit_GameInformation _game;

	// Token: 0x040005E5 RID: 1509
	private string _baseString = "1234567890ABCDEF";

	// Token: 0x040005E6 RID: 1510
	private string _group = "DefaultValues";

	// Token: 0x040005E7 RID: 1511
	private string _playerKey = "Player";

	// Token: 0x040005E8 RID: 1512
	private string _playersKey1 = "Players1";

	// Token: 0x040005E9 RID: 1513
	private string _playersKey2 = "Players2";

	// Token: 0x040005EA RID: 1514
	private string _tournamentKey = "Tournament";

	// Token: 0x040005EB RID: 1515
	private int _defaultMinDelay = 2;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using Newtonsoft.Json;
using UnityEngine;

// Token: 0x0200006A RID: 106
public class TournamentsScreenController
{
	// Token: 0x060002F0 RID: 752 RVA: 0x0000EAA4 File Offset: 0x0000CCA4
	public void Initialize()
	{
		Kit_GameInformation game = Resources.Load<Kit_GameInformation>("Game");
		this._unixEpoch = new DateTime(1970, 1, 1, 0, 0, 0);
		this._tournamentListScreenView = UnityEngine.Object.FindObjectOfType<TournamensListScreenView>();
		this._tournamentParticipationScreenView = UnityEngine.Object.FindObjectOfType<TournamentParticipationScreenView>();
		this._tournamentCreateScreenView = UnityEngine.Object.FindObjectOfType<TournamentCreateScreenView>();
		this._tournamentOverviewScreenView = UnityEngine.Object.FindObjectOfType<TournamentOverviewScreenView>();
		this._tournamentListScreenView.ViewInit(new Action<int>(this.ChooseTournament), new Action(this.GetTournamentList), new Action<int, bool>(this.ChangeFilter));
		this._tournamentParticipationScreenView.ViewInit(new Action<int, int>(this.OnPlayerCellChoose), new Action(this.GetTournamentById), new Action<ulong>(this.GetPlayerStatistics), new Action(this.GetParticipationData));
		this._tournamentCreateScreenView.ViewInit(game, new Action<TournamentModel>(this.SetTestTournament));
		this._tournamentOverviewScreenView.ViewInit(game, new Action<string>(this.CheckCode), new Action(this.JoinToTournamentLobby));
		this._observeProcess = new ObserveProcess();
		this._waitProcess = new WaitProcess();
		this._observeProcess.StartProcess(delegate
		{
			if (this._tournamentListScreenView.IsActive)
			{
				this._waitProcess.StartProcess(delegate
				{
					this.GetTournamentList();
				}, this._updateRateTournamentsList);
				return;
			}
			if (this._tournamentOverviewScreenView.IsActive)
			{
				this._waitProcess.StartProcess(delegate
				{
					this.GetTournamentById();
				}, this._updateRateTournametnOverview);
				return;
			}
			if (this._tournamentParticipationScreenView.IsActive)
			{
				this._waitProcess.StartProcess(delegate
				{
					this.GetParticipationData();
				}, this._updateRateTournamentParticipation);
			}
		});
		TournamentsController.Instance.GetTournamentsList(new Action<string>(this.OnTournamentsListLoaded));
	}

	// Token: 0x060002F1 RID: 753 RVA: 0x0000EBE6 File Offset: 0x0000CDE6
	private void ChooseTournament(int choosenTournamentId)
	{
		TournamentsController.Instance.GetTournamentById(choosenTournamentId, new Action<string>(this.OnTournamentOverviewInfoLoad));
	}

	// Token: 0x060002F2 RID: 754 RVA: 0x0000EBFF File Offset: 0x0000CDFF
	private void OnPlayerCellChoose(int teamIndex, int cellIndex)
	{
		TournamentsController.Instance.SetPlayerInCell(teamIndex, cellIndex, new Action<string>(this.PlayerCellChooseCallback));
	}

	// Token: 0x060002F3 RID: 755 RVA: 0x0000EC19 File Offset: 0x0000CE19
	private void SetTestTournament(TournamentModel tournament)
	{
		TournamentsController.Instance.SetNewTournament(tournament, new Action<string>(this.OnTournamentsListLoaded));
	}

	// Token: 0x060002F4 RID: 756 RVA: 0x0000EC32 File Offset: 0x0000CE32
	private void CheckCode(string code)
	{
		TournamentsController.Instance.CheckTournamentCode(code, new Action<string>(this.CheckCodeCallback));
	}

	// Token: 0x060002F5 RID: 757 RVA: 0x0000EC4B File Offset: 0x0000CE4B
	private void JoinToTournamentLobby()
	{
		this.GetParticipationData();
	}

	// Token: 0x060002F6 RID: 758 RVA: 0x0000EC53 File Offset: 0x0000CE53
	private void GetTournamentList()
	{
		TournamentsController.Instance.GetTournamentsList(new Action<string>(this.OnTournamentsListLoaded));
		this._waitProcess.RestartTimer();
	}

	// Token: 0x060002F7 RID: 759 RVA: 0x0000EC76 File Offset: 0x0000CE76
	private void GetTournamentById()
	{
		TournamentsController.Instance.GetTournamentById(this._choosenTournament.ID, new Action<string>(this.OnTournamentOverviewInfoLoad));
		this._waitProcess.RestartTimer();
	}

	// Token: 0x060002F8 RID: 760 RVA: 0x0000ECA4 File Offset: 0x0000CEA4
	private void GetParticipationData()
	{
		TournamentsController.Instance.GetParticipationData(new Action<string>(this.ParticipationScreenUpdateCallback));
		this._waitProcess.RestartTimer();
	}

	// Token: 0x060002F9 RID: 761 RVA: 0x0000ECC7 File Offset: 0x0000CEC7
	private void GetPlayerStatistics(ulong steamID)
	{
		Debug.Log(string.Format("GetPlayerStatistics player Name: {0}", steamID));
		if (steamID != 0UL)
		{
			TournamentsController.Instance.GetPlayerStatistics(steamID, new Action<string>(this.GetPlayerStatisticsCallback));
		}
	}

	// Token: 0x060002FA RID: 762 RVA: 0x0000ECF8 File Offset: 0x0000CEF8
	private void ChangeFilter(int index, bool value)
	{
		this._tournamentsFilter[index] = value;
		this._tournamentListScreenView.SetView(this.FiltTournamentList(this._tournamentList));
	}

	// Token: 0x060002FB RID: 763 RVA: 0x0000ED1A File Offset: 0x0000CF1A
	private void OnTournamentsListLoaded(string list)
	{
		if (list == "null")
		{
			return;
		}
		this._tournamentList = JsonConvert.DeserializeObject<List<TournamentModel>>(list);
		this._tournamentListScreenView.SetView(this.FiltTournamentList(this._tournamentList));
	}

	// Token: 0x060002FC RID: 764 RVA: 0x0000ED4D File Offset: 0x0000CF4D
	private void OnTournamentOverviewInfoLoad(string json)
	{
		Debug.Log("From Server Overview: " + json);
		this._choosenTournament = JsonConvert.DeserializeObject<TournamentModel>(json);
		TournamentsController.Instance.SetChoosenTournament(this._choosenTournament);
		this._tournamentOverviewScreenView.SetView(this._choosenTournament);
	}

	// Token: 0x060002FD RID: 765 RVA: 0x0000ED8C File Offset: 0x0000CF8C
	private void OnServerTimeLoaded(string time)
	{
		DateTime utcDateTime = DateTimeOffset.FromUnixTimeSeconds((long)int.Parse(time)).UtcDateTime;
	}

	// Token: 0x060002FE RID: 766 RVA: 0x0000EDB0 File Offset: 0x0000CFB0
	private void PlayerCellChooseCallback(string json)
	{
		Debug.Log("PlayerCellChooseCallback " + json);
		TournamentModel choosenTournament = JsonConvert.DeserializeObject<TournamentModel>(json);
		this._tournamentParticipationScreenView.SetView(choosenTournament, Kit_GameSettings.userName);
	}

	// Token: 0x060002FF RID: 767 RVA: 0x0000EDE8 File Offset: 0x0000CFE8
	private void ParticipationScreenUpdateCallback(string tournamentJson)
	{
		Debug.Log("From Server Participation: " + tournamentJson);
		TournamentModel tournamentModel = JsonConvert.DeserializeObject<TournamentModel>(tournamentJson);
		RegionController.Instance.ChangeRegion(tournamentModel.Region);
		if (!tournamentModel.IsEnded && DateTimeOffset.FromUnixTimeSeconds((long)tournamentModel.TimeBeforeMatch).UtcDateTime.Subtract(this._unixEpoch).TotalSeconds <= 0.0)
		{
			Debug.Log("Start Match");
			TournamentsController.Instance.GetMatch(tournamentModel.ID, new Action<string>(this.GetMatchCallback));
			this._waitProcess.RestartTimer();
			return;
		}
		this._tournamentParticipationScreenView.SetView(tournamentModel, Kit_GameSettings.userName);
		if ((float)tournamentModel.TimeBeforeMatch < this._updateRateTournamentParticipation)
		{
			this._waitProcess.OverrideEndTime((float)tournamentModel.TimeBeforeMatch + 0.25f);
		}
	}

	// Token: 0x06000300 RID: 768 RVA: 0x0000EEC4 File Offset: 0x0000D0C4
	private void CheckCodeCallback(string answer)
	{
		if (bool.Parse(answer))
		{
			this.JoinToTournamentLobby();
			this.GetTournamentById();
			this._tournamentOverviewScreenView.SwitchToParticipationScreen();
			return;
		}
		this._tournamentOverviewScreenView.ShowMessage(LocalizationController.Instance.GetText("TournamentRegistrationView", this._defaultFailureMessage));
	}

	// Token: 0x06000301 RID: 769 RVA: 0x0000EF14 File Offset: 0x0000D114
	private void GetMatchCallback(string jsonMatch)
	{
		Debug.Log("GetMatchCallback FromServer: " + jsonMatch);
		TournamentModel tournamentModel = JsonConvert.DeserializeObject<TournamentModel>(jsonMatch);
		TournamentsController.Instance.SetCurrentMatch(tournamentModel.Rounds[0].Matches[0]);
		HostGameController.Instance.StartTournament(tournamentModel.HostGameData, this.GetExpectedUsers(tournamentModel.Rounds[0].Matches[0]), tournamentModel.Rounds[0].Matches[0].RoomName, (byte)(tournamentModel.Rounds[0].Matches[0].Teams[0].Players.Length * 2));
	}

	// Token: 0x06000302 RID: 770 RVA: 0x0000EFCA File Offset: 0x0000D1CA
	private void CheckMemberCallback(string answer)
	{
		if (bool.Parse(answer))
		{
			this._tournamentOverviewScreenView.SwitchToParticipationScreen();
			return;
		}
		this._tournamentOverviewScreenView.ShowMessage(this._defaultFailureMessage);
	}

	// Token: 0x06000303 RID: 771 RVA: 0x0000EFF4 File Offset: 0x0000D1F4
	private void GetPlayerStatisticsCallback(string accountJson)
	{
		AccountModel playerStatisticsView = JsonConvert.DeserializeObject<AccountModel>(accountJson);
		this._tournamentParticipationScreenView.SetPlayerStatisticsView(playerStatisticsView);
	}

	// Token: 0x06000304 RID: 772 RVA: 0x0000F014 File Offset: 0x0000D214
	private string[] GetExpectedUsers(TournamentMatch match)
	{
		return null;
	}

	// Token: 0x06000305 RID: 773 RVA: 0x0000F018 File Offset: 0x0000D218
	private List<TournamentModel> FiltTournamentList(List<TournamentModel> tournaments)
	{
		List<TournamentModel> list = new List<TournamentModel>(tournaments.Count);
		for (int i = 0; i < tournaments.Count; i++)
		{
			bool flag = true;
			TournamentModel tournamentModel = tournaments[i];
			if ((!tournamentModel.IsEnded && DateTimeOffset.FromUnixTimeSeconds((long)tournamentModel.Date).UtcDateTime < DateTime.Now) != this._tournamentsFilter[0])
			{
				flag = false;
			}
			if (tournamentModel.IsEnded != this._tournamentsFilter[1])
			{
				flag = false;
			}
			if (DateTimeOffset.FromUnixTimeSeconds((long)tournamentModel.Date).UtcDateTime >= DateTime.Now != this._tournamentsFilter[2])
			{
				flag = false;
			}
			if (tournamentModel.Participation != this._tournamentsFilter[3])
			{
				flag = false;
			}
			if (flag)
			{
				list.Add(tournamentModel);
			}
		}
		return list;
	}

	// Token: 0x040001F0 RID: 496
	private TournamensListScreenView _tournamentListScreenView;

	// Token: 0x040001F1 RID: 497
	private TournamentParticipationScreenView _tournamentParticipationScreenView;

	// Token: 0x040001F2 RID: 498
	private TournamentCreateScreenView _tournamentCreateScreenView;

	// Token: 0x040001F3 RID: 499
	private TournamentOverviewScreenView _tournamentOverviewScreenView;

	// Token: 0x040001F4 RID: 500
	private TournamentModel _choosenTournament;

	// Token: 0x040001F5 RID: 501
	private ObserveProcess _observeProcess;

	// Token: 0x040001F6 RID: 502
	private WaitProcess _waitProcess;

	// Token: 0x040001F7 RID: 503
	private float _updateRateTournamentsList = 5f;

	// Token: 0x040001F8 RID: 504
	private float _updateRateTournametnOverview = 5f;

	// Token: 0x040001F9 RID: 505
	private float _updateRateTournamentParticipation = 5f;

	// Token: 0x040001FA RID: 506
	private string _defaultFailureMessage = "FailureMessage";

	// Token: 0x040001FB RID: 507
	private DateTime _unixEpoch;

	// Token: 0x040001FC RID: 508
	private bool[] _tournamentsFilter = new bool[]
	{
		true,
		true,
		true,
		true
	};

	// Token: 0x040001FD RID: 509
	private List<TournamentModel> _tournamentList;

	// Token: 0x040001FE RID: 510
	private SteamNamesController _steamNameController;
}
﻿using System;

// Token: 0x02000093 RID: 147
public class TournamentTeam
{
	// Token: 0x040002C0 RID: 704
	public string Name;

	// Token: 0x040002C1 RID: 705
	public ulong[] Players;
}
﻿using System;
using UnityEngine;
using UnityEngine.EventSystems;

// Token: 0x02000102 RID: 258
public class TournamentTeamPlayerCellView : MonoBehaviour, IPointerEnterHandler, IEventSystemHandler, IPointerExitHandler
{
	// Token: 0x06000682 RID: 1666 RVA: 0x0001E73D File Offset: 0x0001C93D
	public void ViewInit(Action onPointerEnter, Action onPointerExit)
	{
		this._onPointerEnter = onPointerEnter;
		this._onPointerExit = onPointerExit;
	}

	// Token: 0x06000683 RID: 1667 RVA: 0x0001E74D File Offset: 0x0001C94D
	public void OnPointerEnter(PointerEventData eventData)
	{
		this._onPointerEnter();
	}

	// Token: 0x06000684 RID: 1668 RVA: 0x0001E75A File Offset: 0x0001C95A
	public void OnPointerExit(PointerEventData eventData)
	{
		this._onPointerExit();
	}

	// Token: 0x040005BB RID: 1467
	private Action _onPointerEnter = delegate()
	{
	};

	// Token: 0x040005BC RID: 1468
	private Action _onPointerExit = delegate()
	{
	};
}
﻿using System;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000103 RID: 259
public class TournamentTeamView : MonoBehaviour
{
	// Token: 0x06000686 RID: 1670 RVA: 0x0001E7C5 File Offset: 0x0001C9C5
	public void ViewInit(Action<int, int> onPlayerCellChoose, Action<ulong> onCellEnter, Action onCellExit, TournamentTeam tournamentTeam, int index)
	{
		this._onCellChoose = onPlayerCellChoose;
		this._onCellEnter = onCellEnter;
		this._onCellExit = onCellExit;
		this._index = index;
		this._teamNameText.text = tournamentTeam.Name;
		this.SetPlayersButtons(tournamentTeam);
	}

	// Token: 0x06000687 RID: 1671 RVA: 0x0001E800 File Offset: 0x0001CA00
	private void SetPlayersButtons(TournamentTeam tournamentTeam)
	{
		TournamentTeamView.<>c__DisplayClass9_0 CS$<>8__locals1 = new TournamentTeamView.<>c__DisplayClass9_0();
		CS$<>8__locals1.<>4__this = this;
		CS$<>8__locals1.tournamentTeam = tournamentTeam;
		int i;
		int j;
		for (i = 0; i < CS$<>8__locals1.tournamentTeam.Players.Length; i = j + 1)
		{
			RectTransform rectTransform = UnityEngine.Object.Instantiate<RectTransform>(this._playerButtonPrefab, this._content);
			rectTransform.anchoredPosition = new Vector2(rectTransform.anchoredPosition.x, -rectTransform.sizeDelta.y * (float)i);
			Button item = rectTransform.GetComponent<Button>();
			TextMeshProUGUI itemText = item.GetComponentInChildren<TextMeshProUGUI>();
			TournamentTeamPlayerCellView cellView = item.GetComponent<TournamentTeamPlayerCellView>();
			this._steamNameController.GetPlayerName(CS$<>8__locals1.tournamentTeam.Players[i], delegate(string player)
			{
				itemText.text = (string.IsNullOrWhiteSpace(player) ? "Free" : player);
				item.enabled = string.IsNullOrWhiteSpace(player);
				int id = i;
				item.onClick.AddListener(delegate()
				{
					CS$<>8__locals1.<>4__this._onCellChoose(CS$<>8__locals1.<>4__this._index, id);
				});
				ulong p = CS$<>8__locals1.tournamentTeam.Players[i];
				TournamentTeamPlayerCellView cellView = cellView;
				Action onPointerEnter = delegate()
				{
					CS$<>8__locals1.<>4__this._onCellEnter(p);
				};
				Action onPointerExit;
				if ((onPointerExit = CS$<>8__locals1.<>9__3) == null)
				{
					onPointerExit = (CS$<>8__locals1.<>9__3 = delegate()
					{
						CS$<>8__locals1.<>4__this._onCellExit();
					});
				}
				cellView.ViewInit(onPointerEnter, onPointerExit);
			});
			j = i;
		}
	}

	// Token: 0x040005BD RID: 1469
	[SerializeField]
	private TextMeshProUGUI _teamNameText;

	// Token: 0x040005BE RID: 1470
	[SerializeField]
	private RectTransform _playerButtonPrefab;

	// Token: 0x040005BF RID: 1471
	[SerializeField]
	private RectTransform _content;

	// Token: 0x040005C0 RID: 1472
	private Action<int, int> _onCellChoose = delegate(int <p0>, int <p1>)
	{
	};

	// Token: 0x040005C1 RID: 1473
	private Action<ulong> _onCellEnter = delegate(ulong <p0>)
	{
	};

	// Token: 0x040005C2 RID: 1474
	private Action _onCellExit = delegate()
	{
	};

	// Token: 0x040005C3 RID: 1475
	private int _index;

	// Token: 0x040005C4 RID: 1476
	private SteamNamesController _steamNameController = new SteamNamesController();
}
﻿using System;
using PolyAndCode.UI;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000104 RID: 260
public class TournamentVIew : MonoBehaviour, ICell
{
	// Token: 0x06000689 RID: 1673 RVA: 0x0001E9AD File Offset: 0x0001CBAD
	private void OnEnable()
	{
		this._tournamentButton.onClick.AddListener(delegate()
		{
			this._onTournamentButtonClick(this._tournamentId);
		});
	}

	// Token: 0x0600068A RID: 1674 RVA: 0x0001E9CB File Offset: 0x0001CBCB
	private void OnDisable()
	{
		this._tournamentButton.onClick.RemoveAllListeners();
	}

	// Token: 0x0600068B RID: 1675 RVA: 0x0001E9E0 File Offset: 0x0001CBE0
	public void ViewInit(Action<int> onTournamentButtonClick, TournamentModel model)
	{
		this._tournamentId = model.ID;
		this._onTournamentButtonClick = onTournamentButtonClick;
		this._tournamentText.text = model.Name;
		this._tournamentDateText.text = DateTimeOffset.FromUnixTimeSeconds((long)model.CurrentRoundTime).UtcDateTime.ToString("dd'.'MM'.'yy");
		this._tournamentTimeText.text = DateTimeOffset.FromUnixTimeSeconds((long)model.CurrentRoundTime).UtcDateTime.ToString("HH':'mm':'ss");
		if (model.IsEnded)
		{
			this._tournamentStateText.text = LocalizationController.Instance.GetText("DefaultValues", "Ended");
			this._tournamentStateText.color = this._endedColor;
		}
		else if (DateTimeOffset.FromUnixTimeSeconds((long)model.Date).UtcDateTime < DateTime.Now)
		{
			this._tournamentStateText.text = LocalizationController.Instance.GetText("DefaultValues", "Going");
			this._tournamentStateText.color = this._inProgressColor;
		}
		else
		{
			this._tournamentStateText.text = LocalizationController.Instance.GetText("DefaultValues", "Open");
			this._tournamentStateText.color = this._openColor;
		}
		this._tournamentStageText.text = string.Format("{0}/{1}", model.CurrentRound + 1, model.RoundCount);
		if (model.Participation)
		{
			this._tournamentParticipationText.text = LocalizationController.Instance.GetText("DefaultValues", "Yes");
			this._tournamentParticipationText.color = Color.green;
			return;
		}
		this._tournamentParticipationText.text = LocalizationController.Instance.GetText("DefaultValues", "No");
		this._tournamentParticipationText.color = Color.black;
	}

	// Token: 0x040005C5 RID: 1477
	[SerializeField]
	private Button _tournamentButton;

	// Token: 0x040005C6 RID: 1478
	[SerializeField]
	private TextMeshProUGUI _tournamentText;

	// Token: 0x040005C7 RID: 1479
	[SerializeField]
	private TextMeshProUGUI _tournamentStateText;

	// Token: 0x040005C8 RID: 1480
	[SerializeField]
	private TextMeshProUGUI _tournamentStageText;

	// Token: 0x040005C9 RID: 1481
	[SerializeField]
	private TextMeshProUGUI _tournamentDateText;

	// Token: 0x040005CA RID: 1482
	[SerializeField]
	private TextMeshProUGUI _tournamentTimeText;

	// Token: 0x040005CB RID: 1483
	[SerializeField]
	private TextMeshProUGUI _tournamentParticipationText;

	// Token: 0x040005CC RID: 1484
	[SerializeField]
	private Color _openColor;

	// Token: 0x040005CD RID: 1485
	[SerializeField]
	private Color _inProgressColor;

	// Token: 0x040005CE RID: 1486
	[SerializeField]
	private Color _endedColor;

	// Token: 0x040005CF RID: 1487
	private Action<int> _onTournamentButtonClick = delegate(int <p0>)
	{
	};

	// Token: 0x040005D0 RID: 1488
	private int _tournamentId;
}
﻿using System;
using MarsFPSKit;
using UnityEngine;

// Token: 0x020000C4 RID: 196
public class TrailWeapon : MonoBehaviour
{
	// Token: 0x060004C0 RID: 1216 RVA: 0x00016268 File Offset: 0x00014468
	public void ShowTrail(Vector3 startPos, Vector3 endPos, float time)
	{
		this._lifeTimer = time;
		this._lifeTime = this._lifeTimer;
		this._start = Vector3.Lerp(startPos, endPos, 0.2f);
		this._end = endPos;
		this._trail.SetPosition(0, startPos);
		this._trail.SetPosition(1, endPos);
		this._trail.startColor = Color.white;
		this._trail.endColor = Color.white;
		this.isActive = true;
	}

	// Token: 0x060004C1 RID: 1217 RVA: 0x000162E4 File Offset: 0x000144E4
	private void Update()
	{
		if (this.isActive)
		{
			if (this._lifeTimer >= 0f)
			{
				this._lifeTimer -= Time.deltaTime;
				float t = this._lifeTimer / this._lifeTime;
				this._trail.SetPosition(0, Vector3.Lerp(this._end, this._start, t));
				float a = Mathf.Lerp(this._alphaMin, this._alphaMax, t);
				Color color = new Color(1f, 1f, 1f, a);
				this._trail.startColor = color;
				this._trail.endColor = color;
				return;
			}
			this.Death();
		}
	}

	// Token: 0x060004C2 RID: 1218 RVA: 0x00016393 File Offset: 0x00014593
	private void Death()
	{
		UnityEngine.Object.FindObjectOfType<Kit_IngameMain>().objectPooling.DestroyInstantiateable(base.gameObject);
	}

	// Token: 0x040003AC RID: 940
	[SerializeField]
	private LineRenderer _trail;

	// Token: 0x040003AD RID: 941
	public bool isActive;

	// Token: 0x040003AE RID: 942
	private float _lifeTimer;

	// Token: 0x040003AF RID: 943
	private float _lifeTime;

	// Token: 0x040003B0 RID: 944
	private Vector3 _start;

	// Token: 0x040003B1 RID: 945
	private Vector3 _end;

	// Token: 0x040003B2 RID: 946
	private float _alphaMin = -1.5f;

	// Token: 0x040003B3 RID: 947
	private float _alphaMax = 1f;
}
﻿using System;
using UnityEngine;

// Token: 0x0200001C RID: 28
public static class TransformExtensions
{
	// Token: 0x060000DA RID: 218 RVA: 0x000058EC File Offset: 0x00003AEC
	public static void SetLayerRecursively(this Transform trans, int layer)
	{
		trans.gameObject.layer = layer;
		foreach (object obj in trans)
		{
			((Transform)obj).SetLayerRecursively(layer);
		}
	}
}
﻿using System;
using UnityEngine;

// Token: 0x020000F9 RID: 249
public class TransformTest : MonoBehaviour
{
	// Token: 0x06000637 RID: 1591 RVA: 0x0001D494 File Offset: 0x0001B694
	private void Update()
	{
		if (this._transform != null)
		{
			string format = "TransformPosition: {0}";
			Transform transform = this._transform;
			Debug.Log(string.Format(format, (transform != null) ? new Vector3?(transform.position) : null));
		}
	}

	// Token: 0x04000565 RID: 1381
	[SerializeField]
	private Transform _transform;
}
﻿using System;
using UnityEngine;

// Token: 0x02000041 RID: 65
public static class UIExtension
{
	// Token: 0x0600017D RID: 381 RVA: 0x00008750 File Offset: 0x00006950
	public static Vector3[] GetCorners(this RectTransform rectTransform)
	{
		Vector3[] array = new Vector3[4];
		rectTransform.GetWorldCorners(array);
		return array;
	}

	// Token: 0x0600017E RID: 382 RVA: 0x0000876C File Offset: 0x0000696C
	public static float MaxY(this RectTransform rectTransform)
	{
		return rectTransform.GetCorners()[1].y;
	}

	// Token: 0x0600017F RID: 383 RVA: 0x0000877F File Offset: 0x0000697F
	public static float MinY(this RectTransform rectTransform)
	{
		return rectTransform.GetCorners()[0].y;
	}

	// Token: 0x06000180 RID: 384 RVA: 0x00008792 File Offset: 0x00006992
	public static float MaxX(this RectTransform rectTransform)
	{
		return rectTransform.GetCorners()[2].x;
	}

	// Token: 0x06000181 RID: 385 RVA: 0x000087A5 File Offset: 0x000069A5
	public static float MinX(this RectTransform rectTransform)
	{
		return rectTransform.GetCorners()[0].x;
	}
}
﻿using System;
using System.Collections;
using MarsFPSKit;
using MarsFPSKit.Scoreboard;
using Photon.Pun;
using Photon.Realtime;
using TMPro;
using UnityEngine;

// Token: 0x0200010B RID: 267
public class VictoryUI : Kit_VictoryScreenUI, IActiveObject
{
	// Token: 0x1700005E RID: 94
	// (get) Token: 0x060006C0 RID: 1728 RVA: 0x0001FB18 File Offset: 0x0001DD18
	public ScoreboardView Scoreboard
	{
		get
		{
			return this._scoreboard;
		}
	}

	// Token: 0x060006C1 RID: 1729 RVA: 0x0001FB20 File Offset: 0x0001DD20
	public override void CloseUI()
	{
		this.Hide();
	}

	// Token: 0x060006C2 RID: 1730 RVA: 0x0001FB28 File Offset: 0x0001DD28
	public override void DisplayBotWinner(Kit_Bot winner)
	{
		this._winLoseText.text = LocalizationController.Instance.GetText("HUD", "Defeat");
		this._winnerText.text = winner.name;
		this.PlayEndRoundSequence(-1);
	}

	// Token: 0x060006C3 RID: 1731 RVA: 0x0001FB64 File Offset: 0x0001DD64
	public override void DisplayPlayerWinner(Player winner)
	{
		if (winner == PhotonNetwork.LocalPlayer)
		{
			if (PhotonNetwork.PlayerList.Length > 1 || this._main.currentBotManager.bots.Count > 0)
			{
				this._main.gameInformation.statistics.OnWin(this._main);
			}
			this._winLoseText.text = LocalizationController.Instance.GetText("HUD", "Victory");
			this.PlayEndRoundSequence(1);
		}
		else
		{
			this._winLoseText.text = LocalizationController.Instance.GetText("HUD", "Defeat");
			this.PlayEndRoundSequence(-1);
		}
		this._winnerText.text = winner.NickName;
	}

	// Token: 0x060006C4 RID: 1732 RVA: 0x0001FC18 File Offset: 0x0001DE18
	public override void DisplayTeamWinner(int winner)
	{
		if (winner < 0)
		{
			this._winLoseText.text = LocalizationController.Instance.GetText("HUD", "Tie");
			this._winnerText.text = "";
			this.PlayEndRoundSequence(0);
			return;
		}
		if (this._main.assignedTeamID == winner)
		{
			this._winLoseText.text = LocalizationController.Instance.GetText("HUD", "Victory");
			if (PhotonNetwork.PlayerList.Length > 1 || (this._main.currentBotManager != null && this._main.currentBotManager.bots != null && this._main.currentBotManager.bots.Count > 0))
			{
				this._main.gameInformation.statistics.OnWin(this._main);
			}
			this.PlayEndRoundSequence(1);
		}
		else
		{
			this._winLoseText.text = LocalizationController.Instance.GetText("HUD", "Defeat");
			this.PlayEndRoundSequence(-1);
		}
		this._winnerText.text = this._main.gameInformation.allPvpTeams[this._main.assignedTeamID].teamName;
	}

	// Token: 0x060006C5 RID: 1733 RVA: 0x0001FD50 File Offset: 0x0001DF50
	public override void DisplayTeamWinnerWithScores(int winner, int[] scores)
	{
		if (winner < 0)
		{
			this._winLoseText.text = LocalizationController.Instance.GetText("HUD", "Tie");
			this._winnerText.text = "";
			this.PlayEndRoundSequence(0);
			return;
		}
		if (this._main.assignedTeamID == winner)
		{
			this._winLoseText.text = LocalizationController.Instance.GetText("HUD", "Victory");
			if (PhotonNetwork.PlayerList.Length > 1 || (this._main.currentBotManager != null && this._main.currentBotManager.bots != null && this._main.currentBotManager.bots.Count > 0))
			{
				this._main.gameInformation.statistics.OnWin(this._main);
			}
			this.PlayEndRoundSequence(1);
		}
		else
		{
			this._winLoseText.text = LocalizationController.Instance.GetText("HUD", "Defeat");
			this.PlayEndRoundSequence(-1);
		}
		if (this._main.assignedTeamID > 0)
		{
			this._winnerText.text = this._main.gameInformation.allPvpTeams[this._main.assignedTeamID].teamName;
			return;
		}
		this._winnerText.text = "";
	}

	// Token: 0x060006C6 RID: 1734 RVA: 0x0001FEA4 File Offset: 0x0001E0A4
	private void PlayEndRoundSequence(int winner)
	{
		if (this._main.gameInformation.statistics)
		{
			this._main.gameInformation.statistics.Save(this._main);
		}
		this._winLoseSequence.gameObject.SetActive(true);
		this._winLoseSequence.PlaySequence(winner);
		base.StartCoroutine(this.WinLoseSequenceDelay());
	}

	// Token: 0x060006C7 RID: 1735 RVA: 0x0001FF0D File Offset: 0x0001E10D
	public IEnumerator WinLoseSequenceDelay()
	{
		yield return new WaitForSeconds(this._winLoseSequenceDelay);
		this.Show();
		yield break;
	}

	// Token: 0x060006C8 RID: 1736 RVA: 0x0001FF1C File Offset: 0x0001E11C
	private void Show()
	{
		this._canvasGroup.alpha = 1f;
		this._canvasGroup.interactable = true;
		this._canvasGroup.blocksRaycasts = true;
	}

	// Token: 0x060006C9 RID: 1737 RVA: 0x0001FF46 File Offset: 0x0001E146
	private void Hide()
	{
		this._canvasGroup.alpha = 0f;
		this._canvasGroup.interactable = false;
		this._canvasGroup.blocksRaycasts = false;
	}

	// Token: 0x1700005F RID: 95
	// (get) Token: 0x060006CA RID: 1738 RVA: 0x0001FF70 File Offset: 0x0001E170
	public bool IsActive
	{
		get
		{
			return this._canvasGroup.interactable;
		}
	}

	// Token: 0x04000606 RID: 1542
	[SerializeField]
	private TextMeshProUGUI _winLoseText;

	// Token: 0x04000607 RID: 1543
	[SerializeField]
	private TextMeshProUGUI _winnerText;

	// Token: 0x04000608 RID: 1544
	[SerializeField]
	private ScoreboardView _scoreboard;

	// Token: 0x04000609 RID: 1545
	[SerializeField]
	private Kit_ScoreboardMain _kitScoreboard;

	// Token: 0x0400060A RID: 1546
	[SerializeField]
	private Kit_IngameMain _main;

	// Token: 0x0400060B RID: 1547
	[SerializeField]
	private CanvasGroup _canvasGroup;

	// Token: 0x0400060C RID: 1548
	[SerializeField]
	private EndRoundUI _winLoseSequence;

	// Token: 0x0400060D RID: 1549
	[SerializeField]
	private float _winLoseSequenceDelay;
}
﻿using System;
using UnityEngine;

// Token: 0x0200006B RID: 107
public class WaitProcess
{
	// Token: 0x0600030A RID: 778 RVA: 0x0000F181 File Offset: 0x0000D381
	public WaitProcess()
	{
		this._sceneDriver = UnityEngine.Object.FindObjectOfType<SceneDriver>();
	}

	// Token: 0x0600030B RID: 779 RVA: 0x0000F1BC File Offset: 0x0000D3BC
	public void StartProcess(Action action, float time)
	{
		if (!this._isBusy)
		{
			this._action = action;
			this._time = time;
			this._currentTime = 0f;
			this._sceneDriver.OnUpdate += this.Execute;
			this._isBusy = true;
		}
	}

	// Token: 0x0600030C RID: 780 RVA: 0x0000F208 File Offset: 0x0000D408
	private void Execute()
	{
		if (this._currentTime < this._time)
		{
			this._currentTime += Time.deltaTime;
			return;
		}
		this._action();
		this.EndProcess();
	}

	// Token: 0x0600030D RID: 781 RVA: 0x0000F23C File Offset: 0x0000D43C
	public void EndProcess()
	{
		this._sceneDriver.OnUpdate -= this.Execute;
		this._isBusy = false;
	}

	// Token: 0x0600030E RID: 782 RVA: 0x0000F25C File Offset: 0x0000D45C
	public void RestartTimer()
	{
		this._currentTime = 0f;
	}

	// Token: 0x0600030F RID: 783 RVA: 0x0000F269 File Offset: 0x0000D469
	public void OverrideEndTime(float newtime)
	{
		this._time = newtime;
	}

	// Token: 0x040001FF RID: 511
	private SceneDriver _sceneDriver;

	// Token: 0x04000200 RID: 512
	private Action _action = delegate()
	{
	};

	// Token: 0x04000201 RID: 513
	private float _time;

	// Token: 0x04000202 RID: 514
	private float _currentTime;

	// Token: 0x04000203 RID: 515
	private bool _isBusy;
}
﻿using System;
using ExitGames.Client.Photon;
using MarsFPSKit;
using Photon.Pun;
using Photon.Realtime;
using UnityEngine;

// Token: 0x0200006C RID: 108
public class WallCrashController
{
	// Token: 0x06000310 RID: 784 RVA: 0x0000F272 File Offset: 0x0000D472
	public WallCrashController(Kit_IngameMain main)
	{
		this._main = main;
		this.Init();
	}

	// Token: 0x06000311 RID: 785 RVA: 0x0000F287 File Offset: 0x0000D487
	public void WallInteraction(int wallId)
	{
		PhotonNetwork.RaiseEvent(Kit_EventIDs.crashWallEvent, wallId, new RaiseEventOptions
		{
			Receivers = ReceiverGroup.All
		}, SendOptions.SendReliable);
	}

	// Token: 0x06000312 RID: 786 RVA: 0x0000F2AB File Offset: 0x0000D4AB
	public void CrashWall(int wallID)
	{
		this._walls[wallID].CrashWall();
	}

	// Token: 0x06000313 RID: 787 RVA: 0x0000F2BC File Offset: 0x0000D4BC
	public void RestoreAllWalls()
	{
		for (int i = 0; i < this._walls.Length; i++)
		{
			this._walls[i].RestoreWall();
		}
	}

	// Token: 0x06000314 RID: 788 RVA: 0x0000F2E9 File Offset: 0x0000D4E9
	public WallCrashView GetWallByID(int id)
	{
		return this._walls[id];
	}

	// Token: 0x06000315 RID: 789 RVA: 0x0000F2F4 File Offset: 0x0000D4F4
	private void Init()
	{
		this._walls = UnityEngine.Object.FindObjectsOfType<WallCrashView>();
		for (int i = 0; i < this._walls.Length; i++)
		{
			this._walls[i].ViewInit(this, i);
		}
	}

	// Token: 0x04000204 RID: 516
	private WallCrashView[] _walls;

	// Token: 0x04000205 RID: 517
	private Kit_IngameMain _main;
}
﻿using System;
using System.Collections.Generic;
using Photon.Pun;
using UnityEngine;
using UnityEngine.AI;

// Token: 0x020000CF RID: 207
public class WallCrashView : HintableObject
{
	// Token: 0x06000500 RID: 1280 RVA: 0x0001734D File Offset: 0x0001554D
	private void Start()
	{
		this.SetStartPositionsAndRigidBodies();
	}

	// Token: 0x06000501 RID: 1281 RVA: 0x00017355 File Offset: 0x00015555
	public void ViewInit(WallCrashController controller, int id)
	{
		this._controller = controller;
		this._id = id;
		this._wallsPartsRoot.transform.parent = null;
	}

	// Token: 0x06000502 RID: 1282 RVA: 0x00017376 File Offset: 0x00015576
	public void CrashWallRequest()
	{
		this._controller.WallInteraction(this._id);
	}

	// Token: 0x06000503 RID: 1283 RVA: 0x0001738C File Offset: 0x0001558C
	public void CrashWall()
	{
		this.HideGoodWaals();
		this.ShowBreakWalls();
		this._wallCollider.enabled = false;
		this._wallsPartsRoot.SetActive(true);
		this.ApplyForceToRigidbodies();
		this._explosionEffect = UnityEngine.Object.Instantiate<GameObject>(this._explosionPrefub, this._bombPlaceFront.position, Quaternion.identity);
		this._navMeshObstacle.enabled = false;
		this.DestroyAllBombs();
	}

	// Token: 0x06000504 RID: 1284 RVA: 0x000173F8 File Offset: 0x000155F8
	public void RestoreWall()
	{
		this.ShowGoodWalls();
		this.HideBreakWalls();
		this._wallsPartsRoot.SetActive(false);
		this.ResetWallsPartsPositions();
		this._wallCollider.enabled = true;
		UnityEngine.Object.Destroy(this._explosionEffect);
		this._navMeshObstacle.enabled = true;
		this.DestroyAllBombs();
	}

	// Token: 0x06000505 RID: 1285 RVA: 0x0001744C File Offset: 0x0001564C
	public void AddMagneticBomb(GameObject magneticBomb)
	{
		this._magneticBombs.Add(magneticBomb);
	}

	// Token: 0x06000506 RID: 1286 RVA: 0x0001745C File Offset: 0x0001565C
	public Transform GetBombPlace(Vector3 viewDirection)
	{
		float num = Vector3.Dot(viewDirection, base.transform.forward);
		Transform result = (num > 0f) ? this._bombPlaceBack : this._bombPlaceFront;
		this._explosionDirection = ((num > 0f) ? base.transform.forward : (-base.transform.forward));
		return result;
	}

	// Token: 0x06000507 RID: 1287 RVA: 0x000174BC File Offset: 0x000156BC
	private void ShowGoodWalls()
	{
		for (int i = 0; i < this._goodWallsRenderers.Length; i++)
		{
			this._goodWallsRenderers[i].enabled = true;
		}
	}

	// Token: 0x06000508 RID: 1288 RVA: 0x000174EC File Offset: 0x000156EC
	private void ShowBreakWalls()
	{
		for (int i = 0; i < this._breakWallsRenderers.Length; i++)
		{
			this._breakWallsRenderers[i].enabled = true;
		}
	}

	// Token: 0x06000509 RID: 1289 RVA: 0x0001751C File Offset: 0x0001571C
	private void HideGoodWaals()
	{
		for (int i = 0; i < this._goodWallsRenderers.Length; i++)
		{
			this._goodWallsRenderers[i].enabled = false;
		}
	}

	// Token: 0x0600050A RID: 1290 RVA: 0x0001754C File Offset: 0x0001574C
	private void HideBreakWalls()
	{
		for (int i = 0; i < this._breakWallsRenderers.Length; i++)
		{
			this._breakWallsRenderers[i].enabled = false;
		}
	}

	// Token: 0x0600050B RID: 1291 RVA: 0x0001757C File Offset: 0x0001577C
	private void ResetWallsPartsPositions()
	{
		for (int i = 0; i < this._wallsParts.Length; i++)
		{
			this._wallsParts[i].position = this._wallsPartsStartPositions[i];
			this._wallsParts[i].rotation = this._wallsPartsStartRotations[i];
		}
	}

	// Token: 0x0600050C RID: 1292 RVA: 0x000175D0 File Offset: 0x000157D0
	private void SetStartPositionsAndRigidBodies()
	{
		this._wallsPartsStartPositions = new Vector3[this._wallsParts.Length];
		this._wallsPartsStartRotations = new Quaternion[this._wallsParts.Length];
		this._wallsPartsRigidBodies = new Rigidbody[this._wallsParts.Length];
		for (int i = 0; i < this._wallsParts.Length; i++)
		{
			this._wallsPartsStartPositions[i] = this._wallsParts[i].position;
			this._wallsPartsStartRotations[i] = this._wallsParts[i].rotation;
			this._wallsPartsRigidBodies[i] = this._wallsParts[i].GetComponent<Rigidbody>();
		}
	}

	// Token: 0x0600050D RID: 1293 RVA: 0x00017670 File Offset: 0x00015870
	private void DestroyAllBombs()
	{
		for (int i = 0; i < this._magneticBombs.Count; i++)
		{
			PhotonNetwork.Destroy(this._magneticBombs[i]);
		}
		this._magneticBombs.Clear();
	}

	// Token: 0x0600050E RID: 1294 RVA: 0x000176B0 File Offset: 0x000158B0
	private void ApplyForceToRigidbodies()
	{
		for (int i = 0; i < this._wallsPartsRigidBodies.Length; i++)
		{
			this._wallsPartsRigidBodies[i].AddForce(this._explosionDirection * this._explosionStrength);
		}
	}

	// Token: 0x0400046D RID: 1133
	[SerializeField]
	private MeshRenderer[] _goodWallsRenderers;

	// Token: 0x0400046E RID: 1134
	[SerializeField]
	private MeshRenderer[] _breakWallsRenderers;

	// Token: 0x0400046F RID: 1135
	[SerializeField]
	private BoxCollider _wallCollider;

	// Token: 0x04000470 RID: 1136
	[SerializeField]
	private GameObject _wallsPartsRoot;

	// Token: 0x04000471 RID: 1137
	[SerializeField]
	private Transform[] _wallsParts;

	// Token: 0x04000472 RID: 1138
	[SerializeField]
	private Transform _bombPlaceFront;

	// Token: 0x04000473 RID: 1139
	[SerializeField]
	private Transform _bombPlaceBack;

	// Token: 0x04000474 RID: 1140
	[SerializeField]
	private GameObject _explosionPrefub;

	// Token: 0x04000475 RID: 1141
	[SerializeField]
	private NavMeshObstacle _navMeshObstacle;

	// Token: 0x04000476 RID: 1142
	[SerializeField]
	private float _explosionStrength = 100f;

	// Token: 0x04000477 RID: 1143
	private Vector3[] _wallsPartsStartPositions;

	// Token: 0x04000478 RID: 1144
	private Quaternion[] _wallsPartsStartRotations;

	// Token: 0x04000479 RID: 1145
	private Rigidbody[] _wallsPartsRigidBodies;

	// Token: 0x0400047A RID: 1146
	private GameObject _explosionEffect;

	// Token: 0x0400047B RID: 1147
	private WallCrashController _controller;

	// Token: 0x0400047C RID: 1148
	private int _id;

	// Token: 0x0400047D RID: 1149
	private List<GameObject> _magneticBombs = new List<GameObject>();

	// Token: 0x0400047E RID: 1150
	private Vector3 _explosionDirection;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using UnityEngine;

// Token: 0x02000114 RID: 276
public class WeaponAllSlots : MonoBehaviour
{
	// Token: 0x06000722 RID: 1826 RVA: 0x000219FF File Offset: 0x0001FBFF
	public void ViewInit(WeaponCustomizationScreenView screenView, Kit_GameInformation game)
	{
		this._screenView = screenView;
		this._game = game;
	}

	// Token: 0x06000723 RID: 1827 RVA: 0x00021A0F File Offset: 0x0001FC0F
	public void SetView(Kit_WeaponBase weapon)
	{
		this.ClearContent();
		this.CreateSlots(weapon);
		this.SetHeightToContent();
	}

	// Token: 0x06000724 RID: 1828 RVA: 0x00021A24 File Offset: 0x0001FC24
	public void SetHeightToContent()
	{
		float num = 0f;
		for (int i = 0; i < this._slots.Count; i++)
		{
			num += this._slots[i].SetHeightToContent();
		}
		this._slotsContent.sizeDelta = new Vector2(this._slotsContent.sizeDelta.x, num);
	}

	// Token: 0x06000725 RID: 1829 RVA: 0x00021A84 File Offset: 0x0001FC84
	private void ClearContent()
	{
		if (this._slots != null && this._slots.Count > 0)
		{
			for (int i = 0; i < this._slots.Count; i++)
			{
				UnityEngine.Object.Destroy(this._slots[i].gameObject);
			}
		}
		this._slots = new List<WeaponSlot>();
	}

	// Token: 0x06000726 RID: 1830 RVA: 0x00021AE0 File Offset: 0x0001FCE0
	private void CreateSlots(Kit_WeaponBase weapon)
	{
		Kit_WeaponRenderer component = weapon.firstPersonPrefab.GetComponent<Kit_WeaponRenderer>();
		if (!component)
		{
			return;
		}
		for (int i = 0; i < component.attachmentSlots.Length; i++)
		{
			WeaponSlot weaponSlot = UnityEngine.Object.Instantiate<WeaponSlot>(this._prefabWeaponSlot, this._slotsContent);
			weaponSlot.ViewInit(this, component.attachmentSlots[i], i, this._game.GetWeaponCategoryID(weapon.weaponType));
			this._slots.Add(weaponSlot);
		}
	}

	// Token: 0x0400066C RID: 1644
	[SerializeField]
	private RectTransform _slotsContent;

	// Token: 0x0400066D RID: 1645
	[SerializeField]
	private WeaponSlot _prefabWeaponSlot;

	// Token: 0x0400066E RID: 1646
	private List<WeaponSlot> _slots;

	// Token: 0x0400066F RID: 1647
	private WeaponCustomizationScreenView _screenView;

	// Token: 0x04000670 RID: 1648
	private Kit_GameInformation _game;
}
﻿using System;
using MarsFPSKit.Weapons;
using TMPro;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.UI;

// Token: 0x02000115 RID: 277
public class WeaponAttachment : MonoBehaviour
{
	// Token: 0x06000728 RID: 1832 RVA: 0x00021B54 File Offset: 0x0001FD54
	private void OnEnable()
	{
		this._weaponAttachmentButton.onClick.AddListener(delegate()
		{
			this._weaponSlot.OnAttachmentClick(this, this.isOpen);
		});
		StoreController.Instance.inventoryResultEvent.AddListener(new UnityAction(this.Redraw));
		this._buyButton.onClick.AddListener(new UnityAction(this.BuyAttachment));
		this._getRewardButton.onClick.AddListener(new UnityAction(this.GetReward));
	}

	// Token: 0x06000729 RID: 1833 RVA: 0x00021BD0 File Offset: 0x0001FDD0
	private void OnDisable()
	{
		this._weaponAttachmentButton.onClick.RemoveListener(delegate()
		{
			this._weaponSlot.OnAttachmentClick(this, this.isOpen);
		});
		LocalizationController.Instance.OnLanguageChanged -= this.Localize;
		StoreController.Instance.inventoryResultEvent.RemoveListener(new UnityAction(this.Redraw));
		this._buyButton.onClick.RemoveListener(new UnityAction(this.BuyAttachment));
		this._getRewardButton.onClick.RemoveListener(new UnityAction(this.GetReward));
	}

	// Token: 0x0600072A RID: 1834 RVA: 0x00021C64 File Offset: 0x0001FE64
	public void ViewInit(WeaponSlot weaponSlot, Attachment attachment)
	{
		this._weaponSlot = weaponSlot;
		this._attachment = attachment;
		this._itemTaskManager = UnityEngine.Object.FindObjectOfType<ItemTaskManager>();
		if (this._itemTaskManager == null)
		{
			this._itemTaskManager = UnityEngine.Object.Instantiate<ItemTaskManager>(this._itemTaskManagerPrefab);
		}
		if (attachment.info != null)
		{
			this._attachmentMonoBehaviour = attachment.info;
			this.Redraw();
			this._attachmentIcon.sprite = this._attachmentMonoBehaviour.icon;
		}
		else
		{
			this._buyButton.gameObject.SetActive(false);
			this._getRewardButton.gameObject.SetActive(false);
			this._attachmentIcon.gameObject.SetActive(false);
			this._taskDescriptionText.text = "";
			this._taskProgressText.text = "";
			this.isOpen = true;
		}
		this._attachmentNameText.text = LocalizationController.Instance.GetText("Attachments", attachment.name);
		this._attachmentDescriptionText.text = LocalizationController.Instance.GetText("AttachmentsDescription", attachment.name);
		LocalizationController.Instance.OnLanguageChanged += this.Localize;
	}

	// Token: 0x0600072B RID: 1835 RVA: 0x00021D94 File Offset: 0x0001FF94
	private void Redraw()
	{
		if (this._attachment.info != null)
		{
			TaskInfo taskInfo = this._itemTaskManager.GetTaskInfo(this._attachment.info.ID);
			if (StoreController.Instance.openItems.Contains(this._attachment.info.ID) || this._attachment.info.IsStartingItem)
			{
				this.isOpen = true;
				this._buyButton.gameObject.SetActive(false);
				this._getRewardButton.gameObject.SetActive(false);
				this._taskDescriptionText.gameObject.SetActive(false);
				this._taskProgressText.gameObject.SetActive(false);
				return;
			}
			if (taskInfo.targetValue != 0)
			{
				this._taskDescriptionText.gameObject.SetActive(true);
				this._taskProgressText.gameObject.SetActive(true);
				this._taskDescriptionText.text = LocalizationController.Instance.GetText("TasksDescription", taskInfo.description);
				this._taskProgressText.text = string.Format("({0}/{1})", taskInfo.curValue, taskInfo.targetValue);
				if (taskInfo.curValue >= taskInfo.targetValue)
				{
					this._getRewardButton.gameObject.SetActive(true);
					return;
				}
				this._getRewardButton.gameObject.SetActive(false);
				this._buyButton.gameObject.SetActive(true);
				StoreController.Instance.GetItemPrice(this._attachment.info.ID, delegate(string cb)
				{
					this._itemCostText.text = cb;
				});
				return;
			}
			else
			{
				this._taskDescriptionText.gameObject.SetActive(false);
				this._taskProgressText.gameObject.SetActive(false);
				this._getRewardButton.gameObject.SetActive(false);
				this._buyButton.gameObject.SetActive(true);
				StoreController.Instance.GetItemPrice(this._attachment.info.ID, delegate(string cb)
				{
					this._itemCostText.text = cb;
				});
			}
		}
	}

	// Token: 0x0600072C RID: 1836 RVA: 0x00021F9D File Offset: 0x0002019D
	private void GetReward()
	{
		StoreController.Instance.GetRewardItem(this._attachment.info.ID);
	}

	// Token: 0x0600072D RID: 1837 RVA: 0x00021FBC File Offset: 0x000201BC
	public void Select()
	{
		this._backgroundImage.color = Color.white;
		this._attachmentNameText.color = Color.black;
		this._attachmentDescriptionText.color = Color.black;
		this._taskDescriptionText.color = Color.black;
		this._taskProgressText.color = Color.black;
	}

	// Token: 0x0600072E RID: 1838 RVA: 0x0002201C File Offset: 0x0002021C
	public void Deselect()
	{
		this._backgroundImage.color = Color.black;
		this._attachmentNameText.color = Color.white;
		this._attachmentDescriptionText.color = Color.white;
		this._taskDescriptionText.color = Color.white;
		this._taskProgressText.color = Color.white;
	}

	// Token: 0x0600072F RID: 1839 RVA: 0x00022079 File Offset: 0x00020279
	private void BuyAttachment()
	{
		StoreController.Instance.AwakePurchase(this._attachmentMonoBehaviour.ID, 1);
	}

	// Token: 0x06000730 RID: 1840 RVA: 0x00022094 File Offset: 0x00020294
	private void Localize()
	{
		this._attachmentNameText.text = LocalizationController.Instance.GetText("Attachments", this._attachment.name);
		this._attachmentDescriptionText.text = LocalizationController.Instance.GetText("AttachmentsDescription", this._attachment.name);
	}

	// Token: 0x04000671 RID: 1649
	[SerializeField]
	private AttachmentMonoBehaviour _attachmentMonoBehaviour;

	// Token: 0x04000672 RID: 1650
	[SerializeField]
	private TextMeshProUGUI _attachmentNameText;

	// Token: 0x04000673 RID: 1651
	[SerializeField]
	private TextMeshProUGUI _attachmentDescriptionText;

	// Token: 0x04000674 RID: 1652
	[SerializeField]
	private TextMeshProUGUI _taskDescriptionText;

	// Token: 0x04000675 RID: 1653
	[SerializeField]
	private TextMeshProUGUI _taskProgressText;

	// Token: 0x04000676 RID: 1654
	[SerializeField]
	private TextMeshProUGUI _itemCostText;

	// Token: 0x04000677 RID: 1655
	[SerializeField]
	private Button _weaponAttachmentButton;

	// Token: 0x04000678 RID: 1656
	[SerializeField]
	private Button _buyButton;

	// Token: 0x04000679 RID: 1657
	[SerializeField]
	private Button _getRewardButton;

	// Token: 0x0400067A RID: 1658
	[SerializeField]
	private Image _backgroundImage;

	// Token: 0x0400067B RID: 1659
	[SerializeField]
	private Image _attachmentIcon;

	// Token: 0x0400067C RID: 1660
	[SerializeField]
	private ItemTaskManager _itemTaskManagerPrefab;

	// Token: 0x0400067D RID: 1661
	private ItemTaskManager _itemTaskManager;

	// Token: 0x0400067E RID: 1662
	private WeaponSlot _weaponSlot;

	// Token: 0x0400067F RID: 1663
	private Attachment _attachment;

	// Token: 0x04000680 RID: 1664
	private bool isOpen;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x0200010C RID: 268
public class WeaponAttachments : MonoBehaviour
{
	// Token: 0x060006CC RID: 1740 RVA: 0x0001FF85 File Offset: 0x0001E185
	public void ViewInit(Kit_GameInformation game)
	{
		this._game = game;
		this._camera = Camera.main;
		this._observeProcess = new ObserveProcess();
	}

	// Token: 0x060006CD RID: 1741 RVA: 0x0001FFA4 File Offset: 0x0001E1A4
	public void SetView(GameObject weapon, Kit_WeaponBase weaponBase, Kit_GameInformation game)
	{
		this._weaponObject = weapon;
		this._game = game;
		this.ClearContent();
		this.SetButtons(weapon, weaponBase);
		this.UpdateButtonsPositions(Vector2.zero);
	}

	// Token: 0x060006CE RID: 1742 RVA: 0x0001FFD0 File Offset: 0x0001E1D0
	public void HideAllSlots()
	{
		for (int i = 0; i < this._slots.Count; i++)
		{
			this._slots[i].HideSlot();
		}
	}

	// Token: 0x060006CF RID: 1743 RVA: 0x00020004 File Offset: 0x0001E204
	public void UpdateButtonsPositions(Vector2 dummy)
	{
		for (int i = 0; i < this._slots.Count; i++)
		{
			Vector3 position = this._rendererSlots[i].uiPosition.position;
			this._attachmentSlotButtons[i].anchoredPosition = this._renderCamera.WorldToScreenPoint(position);
			this._attachmentSlotButtons[i].anchoredPosition = this._renderCamera.WorldToScreenPoint(position);
			this._slots[i].transform.position = this._attachmentSlotButtons[i].transform.position;
		}
	}

	// Token: 0x060006D0 RID: 1744 RVA: 0x000200B0 File Offset: 0x0001E2B0
	public void UpdateButtonsPositions()
	{
		float timer = 0.1f;
		this._observeProcess.StartProcess(delegate
		{
			if (this._slots != null && timer <= 0f)
			{
				for (int i = 0; i < this._slots.Count; i++)
				{
					Vector3 position = this._rendererSlots[i].uiPosition.position;
					this._attachmentSlotButtons[i].anchoredPosition = this._renderCamera.WorldToScreenPoint(position);
					this._slots[i].transform.position = this._attachmentSlotButtons[i].transform.position;
				}
				this._observeProcess.EndProcess();
				return;
			}
			timer -= Time.deltaTime;
		});
	}

	// Token: 0x060006D1 RID: 1745 RVA: 0x000200EC File Offset: 0x0001E2EC
	public void ClearContent()
	{
		if (this._attachmentSlotButtons != null)
		{
			for (int i = 0; i < this._attachmentSlotButtons.Count; i++)
			{
				UnityEngine.Object.Destroy(this._attachmentSlotButtons[i].gameObject);
			}
			this._attachmentSlotButtons.Clear();
		}
		this._attachmentSlotButtons = new List<RectTransform>();
		if (this._slots != null)
		{
			foreach (WeaponSlot weaponSlot in this._slots)
			{
				UnityEngine.Object.Destroy(weaponSlot.gameObject);
			}
			this._slots.Clear();
		}
		this._slots = new List<WeaponSlot>();
		if (this._uiSlotTransforms != null)
		{
			this._uiSlotTransforms.Clear();
		}
		this._uiSlotTransforms = new List<Transform>();
	}

	// Token: 0x060006D2 RID: 1746 RVA: 0x000201C8 File Offset: 0x0001E3C8
	private void SetButtons(GameObject weapon, Kit_WeaponBase weaponBase)
	{
		Kit_WeaponRenderer component = weapon.GetComponent<Kit_WeaponRenderer>();
		if (!component)
		{
			return;
		}
		this._rendererSlots = component.attachmentSlots;
		for (int i = 0; i < component.attachmentSlots.Length; i++)
		{
			Button button = UnityEngine.Object.Instantiate<Button>(this._attachmentSlotButtonPrefab, this._buttonContainer);
			WeaponSlot slot = button.GetComponentInChildren<WeaponSlot>();
			slot.transform.position = button.transform.position;
			slot.transform.parent = this._slotCardContainer;
			slot.ViewInit(component.attachmentSlots[i], i, this._game.GetWeaponCategoryID(weaponBase.weaponType), weaponBase, this._game, this._weaponDrawer);
			button.onClick.AddListener(delegate()
			{
				this.HideAllSlots();
				slot.ShowSlot();
			});
			this._attachmentSlotButtons.Add(button.GetComponent<RectTransform>());
			this._slots.Add(slot);
		}
	}

	// Token: 0x060006D3 RID: 1747 RVA: 0x000202D4 File Offset: 0x0001E4D4
	private void GetTransforms(Kit_WeaponRenderer weaponRenderer)
	{
		for (int i = 0; i < this._rendererSlots.Length; i++)
		{
			Debug.Log(this._rendererSlots[i].name);
			Transform item = weaponRenderer.transform.Find(this._rendererSlots[i].name);
			this._uiSlotTransforms.Add(item);
		}
		Debug.Log(this._uiSlotTransforms.Count);
	}

	// Token: 0x0400060E RID: 1550
	[SerializeField]
	private Button _attachmentSlotButtonPrefab;

	// Token: 0x0400060F RID: 1551
	[SerializeField]
	private Camera _renderCamera;

	// Token: 0x04000610 RID: 1552
	[SerializeField]
	private WeaponDrawer _weaponDrawer;

	// Token: 0x04000611 RID: 1553
	[SerializeField]
	private Transform _buttonContainer;

	// Token: 0x04000612 RID: 1554
	[SerializeField]
	private Transform _slotCardContainer;

	// Token: 0x04000613 RID: 1555
	private List<RectTransform> _attachmentSlotButtons;

	// Token: 0x04000614 RID: 1556
	private List<WeaponSlot> _slots;

	// Token: 0x04000615 RID: 1557
	private AttachmentSlot[] _rendererSlots;

	// Token: 0x04000616 RID: 1558
	[SerializeField]
	private List<Transform> _uiSlotTransforms;

	// Token: 0x04000617 RID: 1559
	private Camera _camera;

	// Token: 0x04000618 RID: 1560
	private Kit_GameInformation _game;

	// Token: 0x04000619 RID: 1561
	private GameObject _weaponObject;

	// Token: 0x0400061A RID: 1562
	private Kit_WeaponBase _weaponBase;

	// Token: 0x0400061B RID: 1563
	private ObserveProcess _observeProcess;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit.Weapons;
using TMPro;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.UI;

// Token: 0x0200010D RID: 269
public class WeaponCardView : MonoBehaviour
{
	// Token: 0x17000060 RID: 96
	// (get) Token: 0x060006D5 RID: 1749 RVA: 0x00020340 File Offset: 0x0001E540
	public Kit_WeaponBase Weapon
	{
		get
		{
			return this._weapon;
		}
	}

	// Token: 0x060006D6 RID: 1750 RVA: 0x00020348 File Offset: 0x0001E548
	private void OnEnable()
	{
		this._cardButton.onClick.AddListener(delegate()
		{
			this._weaponList.ChooseWeaponCard(this);
		});
		StoreController.Instance.inventoryResultEvent.AddListener(new UnityAction(this.Redraw));
	}

	// Token: 0x060006D7 RID: 1751 RVA: 0x00020381 File Offset: 0x0001E581
	private void OnDisable()
	{
		this._cardButton.onClick.RemoveAllListeners();
		LocalizationController.Instance.OnLanguageChanged -= this.Localize;
	}

	// Token: 0x060006D8 RID: 1752 RVA: 0x000203AC File Offset: 0x0001E5AC
	public void ViewInit(Kit_WeaponBase weapon, WeaponsList weaponList)
	{
		this._weapon = weapon;
		this._weaponList = weaponList;
		if (this._weapon.rarityImage != null)
		{
			this._rarityImage.enabled = true;
			this._rarityImage.sprite = this._weapon.rarityImage;
		}
		if (StoreController.Instance.openItems.Contains(weapon.id) || weapon.isStartingWeapon)
		{
			this._lockImage.enabled = false;
		}
		this._weaponImage.sprite = this._weapon.unlockImage;
		this._weaponText.text = LocalizationController.Instance.GetText("WeaponNames", this._weapon.weaponName);
		LocalizationController.Instance.OnLanguageChanged += this.Localize;
	}

	// Token: 0x060006D9 RID: 1753 RVA: 0x00020478 File Offset: 0x0001E678
	private void Redraw()
	{
		using (List<int>.Enumerator enumerator = StoreController.Instance.openItems.GetEnumerator())
		{
			while (enumerator.MoveNext())
			{
				if (enumerator.Current == this._weapon.id)
				{
					this._lockImage.enabled = false;
				}
			}
		}
	}

	// Token: 0x060006DA RID: 1754 RVA: 0x000204E0 File Offset: 0x0001E6E0
	public void SetSelected(bool isSelected)
	{
		this._backgroundImage.color = (isSelected ? this._white : this._black);
		this._weaponImage.color = (isSelected ? this._black : this._white);
		this._weaponText.color = (isSelected ? this._black : this._white);
	}

	// Token: 0x060006DB RID: 1755 RVA: 0x00020541 File Offset: 0x0001E741
	private void Localize()
	{
		this._weaponText.text = LocalizationController.Instance.GetText("WeaponNames", this._weapon.weaponName);
	}

	// Token: 0x0400061C RID: 1564
	[SerializeField]
	private Button _cardButton;

	// Token: 0x0400061D RID: 1565
	[SerializeField]
	private Image _backgroundImage;

	// Token: 0x0400061E RID: 1566
	[SerializeField]
	private Image _weaponImage;

	// Token: 0x0400061F RID: 1567
	[SerializeField]
	private Image _rarityImage;

	// Token: 0x04000620 RID: 1568
	[SerializeField]
	private Image _lockImage;

	// Token: 0x04000621 RID: 1569
	[SerializeField]
	private TextMeshProUGUI _weaponText;

	// Token: 0x04000622 RID: 1570
	private Kit_WeaponBase _weapon;

	// Token: 0x04000623 RID: 1571
	private WeaponsList _weaponList;

	// Token: 0x04000624 RID: 1572
	private Color _black = new Color(0f, 0f, 0f, 0.9f);

	// Token: 0x04000625 RID: 1573
	private Color _white = Color.white;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using UnityEngine;

// Token: 0x0200010E RID: 270
public class WeaponCategories : MonoBehaviour
{
	// Token: 0x14000013 RID: 19
	// (add) Token: 0x060006DE RID: 1758 RVA: 0x000205A8 File Offset: 0x0001E7A8
	// (remove) Token: 0x060006DF RID: 1759 RVA: 0x000205E0 File Offset: 0x0001E7E0
	public event Action OnCategoryClick = delegate()
	{
	};

	// Token: 0x060006E0 RID: 1760 RVA: 0x00020615 File Offset: 0x0001E815
	public void ViewInit(Kit_GameInformation game, WeaponChooseScreenView screen)
	{
		this._game = game;
		this._screenView = screen;
		this._itemHeight = this._prefab.GetComponent<RectTransform>().sizeDelta.y;
		this.SetCategories();
	}

	// Token: 0x060006E1 RID: 1761 RVA: 0x00020648 File Offset: 0x0001E848
	public void ChooseCategory(WeaponCategoryCardView card)
	{
		this.OnCategoryClick();
		if (card == this._choosenCategoryView)
		{
			return;
		}
		WeaponCategoryCardView choosenCategoryView = this._choosenCategoryView;
		if (choosenCategoryView != null)
		{
			choosenCategoryView.SetSelected(false);
		}
		card.SetSelected(true);
		this._choosenCategoryView = card;
		this._choosenCategory = card.Category;
		this._screenView.ChangeWeaponList(card.Category);
		this.HideAllWeaponsList();
		card.ChooseWeaponByLoadOut();
	}

	// Token: 0x060006E2 RID: 1762 RVA: 0x000206B8 File Offset: 0x0001E8B8
	public void ChooseFirstCategory()
	{
		WeaponCategoryCardView choosenCategoryView = this._choosenCategoryView;
		if (choosenCategoryView != null)
		{
			choosenCategoryView.SetSelected(false);
		}
		this._cards[0].SetSelected(true);
		this._choosenCategoryView = this._cards[0];
		this._choosenCategory = this._cards[0].Category;
		this._screenView.ChangeWeaponList(this._cards[0].Category);
		this._cards[0].ChooseWeaponByLoadOut();
	}

	// Token: 0x060006E3 RID: 1763 RVA: 0x0002073F File Offset: 0x0001E93F
	public void SetWeaponInfo(Kit_WeaponBase weapon)
	{
		this._choosenCategoryView.SetWeaponInfo(weapon.unlockImage, weapon.weaponName);
	}

	// Token: 0x060006E4 RID: 1764 RVA: 0x00020758 File Offset: 0x0001E958
	public void HideAllWeaponsList()
	{
		for (int i = 0; i < this._cards.Count; i++)
		{
			this._cards[i].HideWeaponList();
		}
	}

	// Token: 0x060006E5 RID: 1765 RVA: 0x0002078C File Offset: 0x0001E98C
	public void ShowWeaponList(WeaponCategoryCardView card)
	{
		card.ShowWeaponList();
	}

	// Token: 0x060006E6 RID: 1766 RVA: 0x00020794 File Offset: 0x0001E994
	private void SetCategories()
	{
		string[] allWeaponCategories = this._game.allWeaponCategories;
		this._cards = new List<WeaponCategoryCardView>(allWeaponCategories.Length);
		for (int i = 0; i < allWeaponCategories.Length; i++)
		{
			WeaponCategoryCardView weaponCategoryCardView = UnityEngine.Object.Instantiate<WeaponCategoryCardView>(this._prefab, this._content);
			LoadoutWeapon loadoutWeapon = LoadoutService.Instance.GetSelecetedLoadout().loadoutWeapons[i];
			weaponCategoryCardView.ViewInit(this._game.allWeapons[loadoutWeapon.weaponID].unlockImage, allWeaponCategories[i], this._game.allWeapons[this._game.defaultWeaponsInSlot[i]].weaponName, this, this._screenView, this._game);
			this._cards.Add(weaponCategoryCardView);
		}
	}

	// Token: 0x060006E7 RID: 1767 RVA: 0x00020847 File Offset: 0x0001EA47
	private void SetContentHeight(int count)
	{
		this._content.sizeDelta = new Vector2(this._content.sizeDelta.x, (float)count * this._itemHeight);
	}

	// Token: 0x04000627 RID: 1575
	[SerializeField]
	private RectTransform _content;

	// Token: 0x04000628 RID: 1576
	[SerializeField]
	private WeaponCategoryCardView _prefab;

	// Token: 0x04000629 RID: 1577
	private List<WeaponCategoryCardView> _cards;

	// Token: 0x0400062A RID: 1578
	private WeaponCategoryCardView _choosenCategoryView;

	// Token: 0x0400062B RID: 1579
	private Kit_GameInformation _game;

	// Token: 0x0400062C RID: 1580
	private WeaponChooseScreenView _screenView;

	// Token: 0x0400062D RID: 1581
	private string _choosenCategory;

	// Token: 0x0400062E RID: 1582
	private float _itemHeight;
}
﻿using System;
using MarsFPSKit;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x0200010F RID: 271
public class WeaponCategoryCardView : MonoBehaviour
{
	// Token: 0x17000061 RID: 97
	// (get) Token: 0x060006E9 RID: 1769 RVA: 0x0002089F File Offset: 0x0001EA9F
	public string Category
	{
		get
		{
			return this._category;
		}
	}

	// Token: 0x060006EA RID: 1770 RVA: 0x000208A7 File Offset: 0x0001EAA7
	private void OnEnable()
	{
		this._cardButton.onClick.AddListener(delegate()
		{
			this._categoriesView.ChooseCategory(this);
			this._categoriesView.ShowWeaponList(this);
		});
	}

	// Token: 0x060006EB RID: 1771 RVA: 0x000208C5 File Offset: 0x0001EAC5
	private void OnDisable()
	{
		this._cardButton.onClick.RemoveAllListeners();
		LocalizationController.Instance.OnLanguageChanged -= this.Locolize;
	}

	// Token: 0x060006EC RID: 1772 RVA: 0x000208F0 File Offset: 0x0001EAF0
	public void ViewInit(Sprite weaponImage, string categoryName, string choosenName, WeaponCategories categories, WeaponChooseScreenView chooseScreen, Kit_GameInformation game)
	{
		this._categoryText.text = LocalizationController.Instance.GetText("WeaponCategories", categoryName).ToUpper();
		this._categoriesView = categories;
		this._category = categoryName;
		this.SetWeaponInfo(weaponImage, choosenName);
		this._weaponList.ViewInit(game, chooseScreen, this._category);
		LocalizationController.Instance.OnLanguageChanged += this.Locolize;
	}

	// Token: 0x060006ED RID: 1773 RVA: 0x00020960 File Offset: 0x0001EB60
	public void SetSelected(bool isSelected)
	{
		this._backgroundImage.color = (isSelected ? this._white : this._black);
		this._cardImage.color = (isSelected ? this._black : this._white);
		this._categoryText.color = (isSelected ? this._black : this._white);
		this._weaponText.color = (isSelected ? this._black : this._white);
	}

	// Token: 0x060006EE RID: 1774 RVA: 0x000209DD File Offset: 0x0001EBDD
	public void SetWeaponInfo(Sprite weaponImage, string weaponName)
	{
		this._cardImage.sprite = weaponImage;
		this._weaponText.text = LocalizationController.Instance.GetText("WeaponNames", weaponName);
	}

	// Token: 0x060006EF RID: 1775 RVA: 0x00020A06 File Offset: 0x0001EC06
	public void HideWeaponList()
	{
		this._weaponList.HideWeaponList();
	}

	// Token: 0x060006F0 RID: 1776 RVA: 0x00020A13 File Offset: 0x0001EC13
	public void ShowWeaponList()
	{
		this._weaponList.ShowWeaponList();
	}

	// Token: 0x060006F1 RID: 1777 RVA: 0x00020A20 File Offset: 0x0001EC20
	public void ChooseWeaponByLoadOut()
	{
		this._weaponList.ChooseWeaponCardByLoadout(this._category);
	}

	// Token: 0x060006F2 RID: 1778 RVA: 0x00020A33 File Offset: 0x0001EC33
	private void Locolize()
	{
		this._categoryText.text = LocalizationController.Instance.GetText("WeaponCategories", this._category).ToUpper();
	}

	// Token: 0x0400062F RID: 1583
	[SerializeField]
	private Button _cardButton;

	// Token: 0x04000630 RID: 1584
	[SerializeField]
	private Image _backgroundImage;

	// Token: 0x04000631 RID: 1585
	[SerializeField]
	private Image _cardImage;

	// Token: 0x04000632 RID: 1586
	[SerializeField]
	private TextMeshProUGUI _categoryText;

	// Token: 0x04000633 RID: 1587
	[SerializeField]
	private TextMeshProUGUI _weaponText;

	// Token: 0x04000634 RID: 1588
	[SerializeField]
	private WeaponsList _weaponList;

	// Token: 0x04000635 RID: 1589
	private Color _black = new Color(0f, 0f, 0f, 0.9f);

	// Token: 0x04000636 RID: 1590
	private Color _white = Color.white;

	// Token: 0x04000637 RID: 1591
	private WeaponCategories _categoriesView;

	// Token: 0x04000638 RID: 1592
	private string _category;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000110 RID: 272
public class WeaponChooseScreenView : BaseMenuView
{
	// Token: 0x060006F5 RID: 1781 RVA: 0x00020AA8 File Offset: 0x0001ECA8
	private void OnEnable()
	{
		this._backButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(this._previousMenu);
			LoadoutService.Instance.Save();
		});
		this._dragView.OnMosueDrag += this._weaponDrawer.RotateModel;
		this._dragView.OnMosueDrag += this._weaponAttachments.UpdateButtonsPositions;
		this._dragView.OnMouseClick += this._weaponCategories.HideAllWeaponsList;
		this._dragView.OnMouseClick += this._weaponAttachments.HideAllSlots;
		this._dragView.OnMouseClick += this._weaponInfoView.ShowInfo;
		this._weaponDrawer.OnCorrected += this._weaponAttachments.UpdateButtonsPositions;
		this._weaponCategories.OnCategoryClick += this._weaponInfoView.HideInfo;
	}

	// Token: 0x060006F6 RID: 1782 RVA: 0x00020B98 File Offset: 0x0001ED98
	private void OnDisable()
	{
		this._backButton.onClick.RemoveAllListeners();
		this._dragView.OnMosueDrag -= this._weaponDrawer.RotateModel;
		this._dragView.OnMosueDrag -= this._weaponAttachments.UpdateButtonsPositions;
		this._dragView.OnMouseClick -= this._weaponCategories.HideAllWeaponsList;
		this._dragView.OnMouseClick -= this._weaponAttachments.HideAllSlots;
		this._dragView.OnMouseClick -= this._weaponInfoView.ShowInfo;
		this._weaponDrawer.OnCorrected -= this._weaponAttachments.UpdateButtonsPositions;
		this._weaponCategories.OnCategoryClick -= this._weaponInfoView.HideInfo;
	}

	// Token: 0x060006F7 RID: 1783 RVA: 0x00005F78 File Offset: 0x00004178
	public void ChangeWeaponList(string category)
	{
	}

	// Token: 0x060006F8 RID: 1784 RVA: 0x00020C7C File Offset: 0x0001EE7C
	public void ChangeWeapon(Kit_WeaponBase weapon, bool isOpen)
	{
		this._weaponInfoView.SetView(weapon);
		this._weaponCategories.SetWeaponInfo(weapon);
		this._menuController.MainMenuData.WeaponToCustomize = weapon;
		if (isOpen)
		{
			LoadoutService.Instance.WeaponSelect(weapon.canFitIntoSlots[0], this._game.GetWeaponID(weapon));
			this._weaponDrawer.RedrawFirstPerson(this._game.GetWeaponCategoryID(weapon.weaponType), false, null);
			this._weaponAttachments.SetView(this._weaponDrawer.WeaponPreview, weapon, this._game);
			return;
		}
		this._weaponDrawer.RedrawFirstPerson(this._game.GetWeaponCategoryID(weapon.weaponType), false, weapon);
		this._weaponAttachments.ClearContent();
	}

	// Token: 0x060006F9 RID: 1785 RVA: 0x00020D38 File Offset: 0x0001EF38
	public override void MenuInit(ISwitchableMenu menuController)
	{
		base.MenuInit(menuController);
		this._game = Resources.Load<Kit_GameInformation>("Game");
		this._weaponDrawer.ViewInit(this._game);
		this._weaponCategories.ViewInit(this._game, this);
		this._weaponInfoView.ViewInit(this);
		this._weaponAttachments.ViewInit(this._game);
		this._weaponCategories.ChooseFirstCategory();
	}

	// Token: 0x060006FA RID: 1786 RVA: 0x00020DA7 File Offset: 0x0001EFA7
	public void OnClickWeapon()
	{
		this._weaponInfoView.ShowInfo();
	}

	// Token: 0x04000639 RID: 1593
	[SerializeField]
	private WeaponCategories _weaponCategories;

	// Token: 0x0400063A RID: 1594
	[SerializeField]
	private WeaponInfoView _weaponInfoView;

	// Token: 0x0400063B RID: 1595
	[SerializeField]
	private Button _backButton;

	// Token: 0x0400063C RID: 1596
	[SerializeField]
	private WeaponDrawer _weaponDrawer;

	// Token: 0x0400063D RID: 1597
	[SerializeField]
	private DragView _dragView;

	// Token: 0x0400063E RID: 1598
	[SerializeField]
	private WeaponAttachments _weaponAttachments;

	// Token: 0x0400063F RID: 1599
	private Kit_GameInformation _game;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000116 RID: 278
public class WeaponCustomizationScreenView : BaseMenuView
{
	// Token: 0x06000736 RID: 1846 RVA: 0x0002210D File Offset: 0x0002030D
	private void OnEnable()
	{
		this._backButton.onClick.AddListener(delegate()
		{
			this._menuController.SwitchMenu(this._previousMenu);
			LoadoutService.Instance.Save();
		});
		this._dragView.OnMosueDrag += this._weaponDrawer.RotateModel;
	}

	// Token: 0x06000737 RID: 1847 RVA: 0x00022147 File Offset: 0x00020347
	private void OnDisable()
	{
		this._backButton.onClick.RemoveAllListeners();
		this._dragView.OnMosueDrag -= this._weaponDrawer.RotateModel;
	}

	// Token: 0x06000738 RID: 1848 RVA: 0x00022175 File Offset: 0x00020375
	public override void MenuInit(ISwitchableMenu menuController)
	{
		base.MenuInit(menuController);
		this._game = Resources.Load<Kit_GameInformation>("Game");
		this._weaponDrawer.ViewInit(this._game);
		this._allSlots.ViewInit(this, this._game);
	}

	// Token: 0x06000739 RID: 1849 RVA: 0x000221B1 File Offset: 0x000203B1
	public override void ShowMenu(bool isAnimated)
	{
		base.ShowMenu(isAnimated);
		this.SetView();
	}

	// Token: 0x0600073A RID: 1850 RVA: 0x000221C0 File Offset: 0x000203C0
	public void SetView()
	{
		this._weapon = this._menuController.MainMenuData.WeaponToCustomize;
		this._allSlots.SetView(this._weapon);
		this._weaponDrawer.RedrawFirstPerson(this._game.GetWeaponCategoryID(this._weapon.weaponType), false, null);
	}

	// Token: 0x04000681 RID: 1665
	[SerializeField]
	private WeaponAllSlots _allSlots;

	// Token: 0x04000682 RID: 1666
	[SerializeField]
	private WeaponDrawer _weaponDrawer;

	// Token: 0x04000683 RID: 1667
	[SerializeField]
	private Button _backButton;

	// Token: 0x04000684 RID: 1668
	[SerializeField]
	private DragView _dragView;

	// Token: 0x04000685 RID: 1669
	private Kit_GameInformation _game;

	// Token: 0x04000686 RID: 1670
	private Kit_WeaponBase _weapon;
}
﻿using System;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using UnityEngine;

// Token: 0x02000117 RID: 279
public class WeaponDrawer : MonoBehaviour
{
	// Token: 0x14000014 RID: 20
	// (add) Token: 0x0600073D RID: 1853 RVA: 0x00022218 File Offset: 0x00020418
	// (remove) Token: 0x0600073E RID: 1854 RVA: 0x00022250 File Offset: 0x00020450
	public event Action OnCorrected = delegate()
	{
	};

	// Token: 0x17000062 RID: 98
	// (get) Token: 0x0600073F RID: 1855 RVA: 0x00022285 File Offset: 0x00020485
	public GameObject WeaponPreview
	{
		get
		{
			return this.weaponPreviewObjectFirstPerson;
		}
	}

	// Token: 0x06000740 RID: 1856 RVA: 0x0002228D File Offset: 0x0002048D
	public void ViewInit(Kit_GameInformation game)
	{
		this._game = game;
	}

	// Token: 0x06000741 RID: 1857 RVA: 0x00022298 File Offset: 0x00020498
	public void RedrawFirstPerson(int currentSelectedWeaponCategory, bool onlyUpdateCustomization = false, Kit_WeaponBase weapon = null)
	{
		if (!this.weaponPreviewObjectFirstPerson)
		{
			onlyUpdateCustomization = false;
		}
		if (onlyUpdateCustomization)
		{
			Kit_WeaponRenderer component = this.weaponPreviewObjectFirstPerson.GetComponent<Kit_WeaponRenderer>();
			if (component)
			{
				component.SetAttachments(LoadoutService.Instance.GetSelecetedLoadout().loadoutWeapons[currentSelectedWeaponCategory].attachments, null, null);
				return;
			}
		}
		else
		{
			if (this.weaponPreviewObjectFirstPerson)
			{
				UnityEngine.Object.Destroy(this.weaponPreviewObjectFirstPerson);
			}
			if (weapon != null)
			{
				this.weaponPreviewObjectFirstPerson = UnityEngine.Object.Instantiate<GameObject>(weapon.firstPersonPrefab, this._weaponPreviewFirstPersonGo);
			}
			else
			{
				this.weaponPreviewObjectFirstPerson = UnityEngine.Object.Instantiate<GameObject>(this._game.allWeapons[LoadoutService.Instance.GetSelecetedLoadout().loadoutWeapons[currentSelectedWeaponCategory].weaponID].firstPersonPrefab, this._weaponPreviewFirstPersonGo);
			}
			Kit_WeaponRenderer component2 = this.weaponPreviewObjectFirstPerson.GetComponent<Kit_WeaponRenderer>();
			this.CallCorrector(this.weaponPreviewObjectFirstPerson);
			if (component2)
			{
				if (component2.anim)
				{
					component2.anim.enabled = false;
				}
				if (component2.legacyAnim)
				{
					component2.legacyAnim.enabled = false;
				}
				for (int i = 0; i < component2.hideInCustomiazionMenu.Length; i++)
				{
					component2.hideInCustomiazionMenu[i].enabled = false;
				}
				component2.transform.localPosition = component2.customizationMenuOffset;
				if (weapon != null)
				{
					int[] array = new int[LoadoutService.Instance.GetSelecetedLoadout().loadoutWeapons[currentSelectedWeaponCategory].attachments.Length];
					for (int j = 0; j < array.Length; j++)
					{
						array[j] = 0;
					}
					component2.SetAttachments(array, null, null);
				}
				else
				{
					component2.SetAttachments(LoadoutService.Instance.GetSelecetedLoadout().loadoutWeapons[currentSelectedWeaponCategory].attachments, null, null);
				}
			}
			Kit_MeleeRenderer component3 = this.weaponPreviewObjectFirstPerson.GetComponent<Kit_MeleeRenderer>();
			if (component3)
			{
				if (component3.anim)
				{
					component3.anim.enabled = false;
				}
				if (component3.legacyAnim)
				{
					component3.legacyAnim.enabled = false;
				}
				for (int k = 0; k < component3.hideInCustomiazionMenu.Length; k++)
				{
					component3.hideInCustomiazionMenu[k].enabled = false;
				}
				component3.transform.localPosition = component3.customizationMenuOffset;
			}
			Kit_GrenadeRenderer component4 = this.weaponPreviewObjectFirstPerson.GetComponent<Kit_GrenadeRenderer>();
			if (component4)
			{
				if (component4.anim)
				{
					component4.anim.enabled = false;
				}
				if (component4.legacyAnim)
				{
					component4.legacyAnim.enabled = false;
				}
				for (int l = 0; l < component4.hideInCustomiazionMenu.Length; l++)
				{
					component4.hideInCustomiazionMenu[l].enabled = false;
				}
				component4.transform.localPosition = component4.customizationMenuOffset;
			}
		}
	}

	// Token: 0x06000742 RID: 1858 RVA: 0x00022554 File Offset: 0x00020754
	public void RedrawAttachment(int currentSelectedWeaponCategory, int slot, int attachment)
	{
		Kit_WeaponRenderer component = this.weaponPreviewObjectFirstPerson.GetComponent<Kit_WeaponRenderer>();
		if (component)
		{
			int[] array = new int[LoadoutService.Instance.GetSelecetedLoadout().loadoutWeapons[currentSelectedWeaponCategory].attachments.Length];
			LoadoutService.Instance.GetSelecetedLoadout().loadoutWeapons[currentSelectedWeaponCategory].attachments.CopyTo(array, 0);
			array[slot] = attachment;
			for (int i = 0; i < array.Length; i++)
			{
				if (i < component.attachmentSlots.Length)
				{
					for (int j = 0; j < component.attachmentSlots[i].attachments.Length; j++)
					{
						if (j == array[i])
						{
							for (int k = 0; k < component.attachmentSlots[i].attachments[j].attachmentBehaviours.Length; k++)
							{
								component.attachmentSlots[i].attachments[j].attachmentBehaviours[k].Selected(null, AttachmentUseCase.FirstPerson);
							}
						}
						else
						{
							for (int l = 0; l < component.attachmentSlots[i].attachments[j].attachmentBehaviours.Length; l++)
							{
								component.attachmentSlots[i].attachments[j].attachmentBehaviours[l].Unselected(null, AttachmentUseCase.FirstPerson);
							}
						}
					}
				}
			}
		}
	}

	// Token: 0x06000743 RID: 1859 RVA: 0x00022687 File Offset: 0x00020887
	public void RotateModel(Vector2 value)
	{
		this._weaponRotationPoint.Rotate(Vector3.up, value.x);
	}

	// Token: 0x06000744 RID: 1860 RVA: 0x000226A0 File Offset: 0x000208A0
	private void ClearWeaponOffset(Transform root)
	{
		Transform[] componentsInChildren = root.GetComponentsInChildren<Transform>();
		for (int i = 1; i < 2; i++)
		{
			componentsInChildren[i].localPosition = Vector3.zero;
		}
	}

	// Token: 0x06000745 RID: 1861 RVA: 0x000226D0 File Offset: 0x000208D0
	private void CallCorrector(GameObject weapon)
	{
		WeaponOverviewPositionCorrector component = weapon.GetComponent<WeaponOverviewPositionCorrector>();
		if (component)
		{
			component.CorrectPosition();
		}
		this.OnCorrected();
	}

	// Token: 0x04000688 RID: 1672
	[SerializeField]
	private Transform _weaponPreviewFirstPersonGo;

	// Token: 0x04000689 RID: 1673
	[SerializeField]
	private Transform _weaponRotationPoint;

	// Token: 0x0400068A RID: 1674
	[SerializeField]
	private GameObject _camera;

	// Token: 0x0400068B RID: 1675
	private GameObject weaponPreviewObjectFirstPerson;

	// Token: 0x0400068C RID: 1676
	private Kit_GameInformation _game;
}
﻿using System;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000111 RID: 273
public class WeaponFeaturesLine : MonoBehaviour
{
	// Token: 0x060006FD RID: 1789 RVA: 0x00020DB4 File Offset: 0x0001EFB4
	private void OnDisable()
	{
		LocalizationController.Instance.OnLanguageChanged -= this.Localize;
	}

	// Token: 0x060006FE RID: 1790 RVA: 0x00020DCC File Offset: 0x0001EFCC
	public void ViewInit(WeaponFeatureType featureType, float featureValue)
	{
		this._featureType = featureType;
		this._statNameText.text = LocalizationController.Instance.GetText("WeaponFeature", featureType.ToString());
		Vector2 vector = BoundsWeaponValues.Instance.FeatureBoundValues[featureType];
		float fillAmount = Mathf.InverseLerp(vector.x, vector.y, featureValue);
		this._statBarImage.fillAmount = fillAmount;
		if (featureType == WeaponFeatureType.Accuracy)
		{
			this._statValueText.text = (1f / featureValue).ToString("F0");
		}
		else
		{
			this._statValueText.text = featureValue.ToString("F0");
		}
		LocalizationController.Instance.OnLanguageChanged += this.Localize;
	}

	// Token: 0x060006FF RID: 1791 RVA: 0x00020E89 File Offset: 0x0001F089
	private void Localize()
	{
		this._statNameText.text = LocalizationController.Instance.GetText("WeaponFeature", this._featureType.ToString());
	}

	// Token: 0x04000640 RID: 1600
	[SerializeField]
	private TextMeshProUGUI _statNameText;

	// Token: 0x04000641 RID: 1601
	[SerializeField]
	private Image _statBarImage;

	// Token: 0x04000642 RID: 1602
	[SerializeField]
	private TextMeshProUGUI _statValueText;

	// Token: 0x04000643 RID: 1603
	private WeaponFeatureType _featureType;
}
﻿using System;

// Token: 0x02000070 RID: 112
public enum WeaponFeatureType
{
	// Token: 0x04000224 RID: 548
	None,
	// Token: 0x04000225 RID: 549
	Damage,
	// Token: 0x04000226 RID: 550
	Accuracy,
	// Token: 0x04000227 RID: 551
	Range,
	// Token: 0x04000228 RID: 552
	Recoil,
	// Token: 0x04000229 RID: 553
	BulletsPerMag,
	// Token: 0x0400022A RID: 554
	RPM = 600
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit.Weapons;
using TMPro;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.UI;

// Token: 0x02000112 RID: 274
public class WeaponInfoView : MonoBehaviour
{
	// Token: 0x06000701 RID: 1793 RVA: 0x00020EB8 File Offset: 0x0001F0B8
	private void OnEnable()
	{
		this._buyButton.onClick.AddListener(new UnityAction(this.OnBuyClick));
		this._getRewardButton.onClick.AddListener(new UnityAction(this.GetReward));
		StoreController.Instance.inventoryResultEvent.AddListener(new UnityAction(this.Redraw));
	}

	// Token: 0x06000702 RID: 1794 RVA: 0x00020F18 File Offset: 0x0001F118
	private void OnDisable()
	{
		LocalizationController.Instance.OnLanguageChanged -= this.Localize;
		this._buyButton.onClick.RemoveListener(new UnityAction(this.OnBuyClick));
		this._getRewardButton.onClick.RemoveListener(new UnityAction(this.GetReward));
		StoreController.Instance.inventoryResultEvent.RemoveListener(new UnityAction(this.Redraw));
	}

	// Token: 0x06000703 RID: 1795 RVA: 0x00020F90 File Offset: 0x0001F190
	public void ViewInit(WeaponChooseScreenView screen)
	{
		this._screenView = screen;
		this._weaponImageTransform = this._weaponImage.GetComponent<RectTransform>();
		this._itemTaskManager = UnityEngine.Object.FindObjectOfType<ItemTaskManager>();
		if (this._itemTaskManager == null)
		{
			this._itemTaskManager = UnityEngine.Object.Instantiate<ItemTaskManager>(this._itemTaskManagerPrefab);
		}
		this._lerpProcess = new LerpProcess();
	}

	// Token: 0x06000704 RID: 1796 RVA: 0x00020FEC File Offset: 0x0001F1EC
	public void SetView(Kit_WeaponBase weapon)
	{
		this._weapon = weapon;
		foreach (ItemIcon itemIcon in this._itemiconData.itemIcons)
		{
			if (itemIcon.id == this._weapon.id)
			{
				this._weaponImage.sprite = itemIcon.icon;
				break;
			}
		}
		this._weaponName = weapon.weaponName;
		this._weaponNameText.text = LocalizationController.Instance.GetText("WeaponNames", weapon.weaponName);
		this._weaponDescriptionText.text = LocalizationController.Instance.GetText("WeaponDescriptions", weapon.weaponName);
		this.Redraw();
		if (this._weaponFeaturesViews != null)
		{
			this.ClearContent();
		}
		else
		{
			this._weaponFeaturesViews = new List<WeaponFeaturesLine>();
		}
		Dictionary<WeaponFeatureType, float> weaponsFeatures = weapon.GetWeaponsFeatures();
		this.SetWeaponFeatures(weaponsFeatures);
		LocalizationController.Instance.OnLanguageChanged += this.Localize;
	}

	// Token: 0x06000705 RID: 1797 RVA: 0x000210FC File Offset: 0x0001F2FC
	private void Redraw()
	{
		TaskInfo taskInfo = this._itemTaskManager.GetTaskInfo(this._weapon.id);
		if (StoreController.Instance.openItems.Contains(this._weapon.id) || this._weapon.isStartingWeapon)
		{
			this._lockImage.SetActive(false);
			this._buyButton.gameObject.SetActive(false);
			this._taskContent.SetActive(false);
			this._getRewardButton.gameObject.SetActive(false);
			return;
		}
		this._lockImage.SetActive(true);
		if (taskInfo.targetValue == 0)
		{
			this._getRewardButton.gameObject.SetActive(false);
			this._taskContent.SetActive(true);
			this._buyButton.gameObject.SetActive(true);
			StoreController.Instance.GetItemPrice(this._weapon.id, delegate(string cb)
			{
				this._weaponCostText.text = cb;
			});
			return;
		}
		this._taskContent.SetActive(true);
		this._taskDescriptionKey = taskInfo.description;
		this._weaponTaskDescriptionText.text = LocalizationController.Instance.GetText("TasksDescription", this._taskDescriptionKey);
		this._weaponTaskProgressText.text = string.Format("({0}/{1})", taskInfo.curValue, taskInfo.targetValue);
		if (taskInfo.curValue >= taskInfo.targetValue)
		{
			this._getRewardButton.gameObject.SetActive(true);
			return;
		}
		this._getRewardButton.gameObject.SetActive(false);
		this._buyButton.gameObject.SetActive(true);
		StoreController.Instance.GetItemPrice(this._weapon.id, delegate(string cb)
		{
			this._weaponCostText.text = cb;
		});
	}

	// Token: 0x06000706 RID: 1798 RVA: 0x000212B1 File Offset: 0x0001F4B1
	public void GetReward()
	{
		StoreController.Instance.GetRewardItem(this._weapon.id);
	}

	// Token: 0x06000707 RID: 1799 RVA: 0x000212C8 File Offset: 0x0001F4C8
	public void ShowInfo()
	{
		if (!this._isActive)
		{
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._canvasGroup.alpha = Mathf.Lerp(0f, 1f, this._showCurve.Evaluate(t));
				if (t >= 1f)
				{
					this._canvasGroup.interactable = true;
					this._canvasGroup.blocksRaycasts = true;
					this._isActive = true;
				}
			}, this._showTime);
		}
	}

	// Token: 0x06000708 RID: 1800 RVA: 0x000212EF File Offset: 0x0001F4EF
	public void HideInfo()
	{
		if (this._isActive)
		{
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._canvasGroup.alpha = Mathf.Lerp(1f, 0f, this._showCurve.Evaluate(t));
				if (t >= 1f)
				{
					this._canvasGroup.interactable = false;
					this._canvasGroup.blocksRaycasts = false;
					this._isActive = false;
				}
			}, this._showTime);
		}
	}

	// Token: 0x06000709 RID: 1801 RVA: 0x00021318 File Offset: 0x0001F518
	private void ClearContent()
	{
		for (int i = 0; i < this._weaponFeaturesViews.Count; i++)
		{
			UnityEngine.Object.Destroy(this._weaponFeaturesViews[i].gameObject);
		}
		this._weaponFeaturesViews.Clear();
	}

	// Token: 0x0600070A RID: 1802 RVA: 0x0002135C File Offset: 0x0001F55C
	private void SetWeaponFeatures(Dictionary<WeaponFeatureType, float> weaponFeatures)
	{
		foreach (KeyValuePair<WeaponFeatureType, float> keyValuePair in weaponFeatures)
		{
			WeaponFeaturesLine weaponFeaturesLine = UnityEngine.Object.Instantiate<WeaponFeaturesLine>(this._prefub, this._featuresTransform);
			weaponFeaturesLine.ViewInit(keyValuePair.Key, keyValuePair.Value);
			this._weaponFeaturesViews.Add(weaponFeaturesLine);
		}
	}

	// Token: 0x0600070B RID: 1803 RVA: 0x000213D8 File Offset: 0x0001F5D8
	private void OnBuyClick()
	{
		StoreController.Instance.AwakePurchase(this._weapon.id, 1);
	}

	// Token: 0x0600070C RID: 1804 RVA: 0x000213F0 File Offset: 0x0001F5F0
	private void Localize()
	{
		this._weaponNameText.text = LocalizationController.Instance.GetText("WeaponNames", this._weaponName);
		this._weaponDescriptionText.text = LocalizationController.Instance.GetText("WeaponDescriptions", this._weaponName);
		if (this._taskContent.activeSelf)
		{
			this._weaponTaskDescriptionText.text = LocalizationController.Instance.GetText("TasksDescription", this._taskDescriptionKey);
		}
	}

	// Token: 0x04000644 RID: 1604
	[SerializeField]
	private Image _weaponImage;

	// Token: 0x04000645 RID: 1605
	[SerializeField]
	private GameObject _lockImage;

	// Token: 0x04000646 RID: 1606
	[SerializeField]
	private GameObject _taskContent;

	// Token: 0x04000647 RID: 1607
	[SerializeField]
	private TextMeshProUGUI _weaponNameText;

	// Token: 0x04000648 RID: 1608
	[SerializeField]
	private TextMeshProUGUI _weaponDescriptionText;

	// Token: 0x04000649 RID: 1609
	[SerializeField]
	private TextMeshProUGUI _weaponTaskDescriptionText;

	// Token: 0x0400064A RID: 1610
	[SerializeField]
	private TextMeshProUGUI _weaponTaskProgressText;

	// Token: 0x0400064B RID: 1611
	[SerializeField]
	private TextMeshProUGUI _weaponCostText;

	// Token: 0x0400064C RID: 1612
	[SerializeField]
	private RectTransform _featuresTransform;

	// Token: 0x0400064D RID: 1613
	[SerializeField]
	private WeaponFeaturesLine _prefub;

	// Token: 0x0400064E RID: 1614
	[SerializeField]
	private CanvasGroup _canvasGroup;

	// Token: 0x0400064F RID: 1615
	[SerializeField]
	private AnimationCurve _showCurve;

	// Token: 0x04000650 RID: 1616
	[SerializeField]
	private Button _buyButton;

	// Token: 0x04000651 RID: 1617
	[SerializeField]
	private Button _getRewardButton;

	// Token: 0x04000652 RID: 1618
	[SerializeField]
	private float _showTime;

	// Token: 0x04000653 RID: 1619
	[SerializeField]
	private ItemTaskManager _itemTaskManagerPrefab;

	// Token: 0x04000654 RID: 1620
	[SerializeField]
	private BigItemIcon _itemiconData;

	// Token: 0x04000655 RID: 1621
	private string _taskDescriptionKey;

	// Token: 0x04000656 RID: 1622
	private ItemTaskManager _itemTaskManager;

	// Token: 0x04000657 RID: 1623
	private List<WeaponFeaturesLine> _weaponFeaturesViews;

	// Token: 0x04000658 RID: 1624
	private WeaponChooseScreenView _screenView;

	// Token: 0x04000659 RID: 1625
	private Kit_WeaponBase _weapon;

	// Token: 0x0400065A RID: 1626
	private RectTransform _weaponImageTransform;

	// Token: 0x0400065B RID: 1627
	private float _imageSizeDeviner = 2f;

	// Token: 0x0400065C RID: 1628
	private LerpProcess _lerpProcess;

	// Token: 0x0400065D RID: 1629
	private bool _isActive = true;

	// Token: 0x0400065E RID: 1630
	private string _weaponName;
}
﻿using System;
using UnityEngine;

// Token: 0x02000118 RID: 280
public class WeaponOverviewPositionCorrector : MonoBehaviour
{
	// Token: 0x06000747 RID: 1863 RVA: 0x0002272A File Offset: 0x0002092A
	private void OnEnable()
	{
		this._wait = new WaitProcess();
	}

	// Token: 0x06000748 RID: 1864 RVA: 0x00022737 File Offset: 0x00020937
	private void OnDestroy()
	{
		this._wait.EndProcess();
	}

	// Token: 0x06000749 RID: 1865 RVA: 0x00022744 File Offset: 0x00020944
	public void CorrectPosition()
	{
		this._wait.StartProcess(delegate
		{
			if (base.transform != null && this._correctOffset != null)
			{
				base.transform.localPosition += this._correctOffset.localPosition;
				base.transform.localRotation = this._correctOffset.localRotation;
			}
		}, this._waitTime);
	}

	// Token: 0x0400068D RID: 1677
	[SerializeField]
	private Transform _correctOffset;

	// Token: 0x0400068E RID: 1678
	private WaitProcess _wait;

	// Token: 0x0400068F RID: 1679
	private float _waitTime = 0.1f;
}
﻿using System;
using UnityEngine;

// Token: 0x020000C7 RID: 199
public class WeaponPositionSetterPlayerModelPart : MonoBehaviour
{
	// Token: 0x060004D9 RID: 1241 RVA: 0x00016BFC File Offset: 0x00014DFC
	public void SetupWeapon(int id, Transform weaponTransform)
	{
		if (id < this.WeaponsPositions.Length)
		{
			weaponTransform.localPosition = this.WeaponsPositions[id].localPosition;
			weaponTransform.localRotation = this.WeaponsPositions[id].localRotation;
			return;
		}
		weaponTransform.localPosition = this.WeaponsPositions[0].localPosition;
		weaponTransform.localRotation = this.WeaponsPositions[0].localRotation;
	}

	// Token: 0x040003F4 RID: 1012
	[SerializeField]
	private Transform[] WeaponsPositions;
}
﻿using System;
using UnityEngine;

// Token: 0x020000C8 RID: 200
public class WeaponPositionSetterWeaponPart : MonoBehaviour
{
	// Token: 0x060004DB RID: 1243 RVA: 0x00016C61 File Offset: 0x00014E61
	private void Start()
	{
		base.GetComponentInParent<WeaponPositionSetterPlayerModelPart>().SetupWeapon(this._weaponID, this._weaponTransform);
	}

	// Token: 0x040003F5 RID: 1013
	[SerializeField]
	private Transform _weaponTransform;

	// Token: 0x040003F6 RID: 1014
	[SerializeField]
	private int _weaponID;
}
﻿using System;
using UnityEngine;

// Token: 0x0200011A RID: 282
public class WeaponSightCameraView : MonoBehaviour
{
	// Token: 0x0600075A RID: 1882 RVA: 0x00022C3C File Offset: 0x00020E3C
	public void SetSightsCameras(bool value)
	{
		if (this._sightCameras != null)
		{
			for (int i = 0; i < this._sightCameras.Length; i++)
			{
				this._sightCameras[i].SetActive(value);
			}
		}
	}

	// Token: 0x040006A2 RID: 1698
	[SerializeField]
	private GameObject[] _sightCameras;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using TMPro;
using UnityEngine;

// Token: 0x02000113 RID: 275
public class WeaponsList : MonoBehaviour
{
	// Token: 0x06000712 RID: 1810 RVA: 0x0002154C File Offset: 0x0001F74C
	public void ViewInit(Kit_GameInformation game, WeaponChooseScreenView screen, string category)
	{
		this._game = game;
		this._screenView = screen;
		this._itemHeight = this._prefab.GetComponent<RectTransform>().sizeDelta.y;
		this._lerpProcess = new LerpProcess();
		if (this._cards != null)
		{
			this.ClearContent();
		}
		else
		{
			this._cards = new List<WeaponCardView>();
		}
		this._choosenCard = null;
		List<Kit_WeaponBase> weapons = this.GetWeapons(category);
		this.SetWeaponCards(weapons);
		this.SetContentSize(weapons.Count);
		this._weaponListHead.text = LocalizationController.Instance.GetText("WeaponCategories", category);
	}

	// Token: 0x06000713 RID: 1811 RVA: 0x000215E8 File Offset: 0x0001F7E8
	public void SetView(string category)
	{
		if (this._cards != null)
		{
			this.ClearContent();
		}
		else
		{
			this._cards = new List<WeaponCardView>();
		}
		this._choosenCard = null;
		List<Kit_WeaponBase> weapons = this.GetWeapons(category);
		this.SetWeaponCards(weapons);
		this.SetContentSize(weapons.Count);
		this._weaponListHead.text = LocalizationController.Instance.GetText("WeaponCategories", category);
		this.ChooseWeaponCardByLoadout(category);
	}

	// Token: 0x06000714 RID: 1812 RVA: 0x00021654 File Offset: 0x0001F854
	public void ChooseWeaponCard(WeaponCardView weaponCard)
	{
		WeaponCardView choosenCard = this._choosenCard;
		if (choosenCard != null)
		{
			choosenCard.SetSelected(false);
		}
		weaponCard.SetSelected(true);
		this._choosenCard = weaponCard;
		if (StoreController.Instance.openItems.Contains(weaponCard.Weapon.id) || weaponCard.Weapon.isStartingWeapon)
		{
			this._screenView.ChangeWeapon(weaponCard.Weapon, true);
		}
		else
		{
			this._screenView.ChangeWeapon(weaponCard.Weapon, false);
		}
		this.HideWeaponList();
		this._screenView.OnClickWeapon();
	}

	// Token: 0x06000715 RID: 1813 RVA: 0x000216E1 File Offset: 0x0001F8E1
	public Kit_GameInformation GetGameInformation()
	{
		return this._game;
	}

	// Token: 0x06000716 RID: 1814 RVA: 0x000216E9 File Offset: 0x0001F8E9
	public void ChooseLoadoutWeaponCard(WeaponCardView weaponCard)
	{
		WeaponCardView choosenCard = this._choosenCard;
		if (choosenCard != null)
		{
			choosenCard.SetSelected(false);
		}
		weaponCard.SetSelected(true);
		this._choosenCard = weaponCard;
		this._screenView.ChangeWeapon(weaponCard.Weapon, true);
		this.HideWeaponList();
	}

	// Token: 0x06000717 RID: 1815 RVA: 0x00021724 File Offset: 0x0001F924
	public void ShowWeaponList()
	{
		if (!this._isActive)
		{
			this._canvasGroup.interactable = true;
			this._canvasGroup.blocksRaycasts = true;
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._canvasGroup.alpha = Mathf.Lerp(0f, 1f, this._showCurve.Evaluate(t));
			}, this._showTime);
			this._isActive = true;
		}
	}

	// Token: 0x06000718 RID: 1816 RVA: 0x00021778 File Offset: 0x0001F978
	public void HideWeaponList()
	{
		if (this._isActive)
		{
			this._canvasGroup.interactable = false;
			this._canvasGroup.blocksRaycasts = false;
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._canvasGroup.alpha = Mathf.Lerp(1f, 0f, this._showCurve.Evaluate(t));
			}, this._showTime);
			this._isActive = false;
		}
	}

	// Token: 0x06000719 RID: 1817 RVA: 0x000217CC File Offset: 0x0001F9CC
	private void ClearContent()
	{
		for (int i = 0; i < this._cards.Count; i++)
		{
			UnityEngine.Object.Destroy(this._cards[i].gameObject);
		}
		this._cards.Clear();
	}

	// Token: 0x0600071A RID: 1818 RVA: 0x00021810 File Offset: 0x0001FA10
	private List<Kit_WeaponBase> GetWeapons(string category)
	{
		List<Kit_WeaponBase> list = new List<Kit_WeaponBase>();
		Kit_WeaponBase[] allWeapons = this._game.allWeapons;
		for (int i = 0; i < allWeapons.Length; i++)
		{
			if (allWeapons[i].weaponType == category)
			{
				list.Add(allWeapons[i]);
			}
		}
		return list;
	}

	// Token: 0x0600071B RID: 1819 RVA: 0x00021858 File Offset: 0x0001FA58
	private void SetWeaponCards(List<Kit_WeaponBase> weaponsList)
	{
		for (int i = 0; i < weaponsList.Count; i++)
		{
			WeaponCardView weaponCardView = UnityEngine.Object.Instantiate<WeaponCardView>(this._prefab, this._content);
			weaponCardView.ViewInit(weaponsList[i], this);
			this._cards.Add(weaponCardView);
		}
	}

	// Token: 0x0600071C RID: 1820 RVA: 0x000218A2 File Offset: 0x0001FAA2
	private void SetContentSize(int count)
	{
		this._content.sizeDelta = new Vector2(this._content.sizeDelta.x, (float)count * this._itemHeight);
	}

	// Token: 0x0600071D RID: 1821 RVA: 0x000218D0 File Offset: 0x0001FAD0
	public void ChooseWeaponCardByLoadout(string category)
	{
		Loadout selecetedLoadout = LoadoutService.Instance.GetSelecetedLoadout();
		int weaponCategoryID = this._game.GetWeaponCategoryID(category);
		if (weaponCategoryID < 0)
		{
			return;
		}
		LoadoutWeapon loadoutWeapon = selecetedLoadout.loadoutWeapons[weaponCategoryID];
		Kit_WeaponBase y = this._game.allWeapons[loadoutWeapon.weaponID];
		for (int i = 0; i < this._cards.Count; i++)
		{
			if (this._cards[i].Weapon == y)
			{
				this.ChooseLoadoutWeaponCard(this._cards[i]);
				return;
			}
		}
	}

	// Token: 0x0600071E RID: 1822 RVA: 0x00021960 File Offset: 0x0001FB60
	private void LoadoutDebugLog(Loadout loadout)
	{
		for (int i = 0; i < loadout.loadoutWeapons.Length; i++)
		{
			LoadoutWeapon loadoutWeapon = loadout.loadoutWeapons[i];
			Debug.Log(string.Format("category {0}; loadoutWeapon[{1}]: {2}", i, i, loadoutWeapon.weaponID));
		}
	}

	// Token: 0x0400065F RID: 1631
	[SerializeField]
	private TextMeshProUGUI _weaponListHead;

	// Token: 0x04000660 RID: 1632
	[SerializeField]
	private RectTransform _content;

	// Token: 0x04000661 RID: 1633
	[SerializeField]
	private WeaponCardView _prefab;

	// Token: 0x04000662 RID: 1634
	[SerializeField]
	private CanvasGroup _canvasGroup;

	// Token: 0x04000663 RID: 1635
	[SerializeField]
	private AnimationCurve _showCurve;

	// Token: 0x04000664 RID: 1636
	[SerializeField]
	private float _showTime;

	// Token: 0x04000665 RID: 1637
	private List<WeaponCardView> _cards;

	// Token: 0x04000666 RID: 1638
	private WeaponCardView _choosenCard;

	// Token: 0x04000667 RID: 1639
	private Kit_GameInformation _game;

	// Token: 0x04000668 RID: 1640
	private WeaponChooseScreenView _screenView;

	// Token: 0x04000669 RID: 1641
	private LerpProcess _lerpProcess;

	// Token: 0x0400066A RID: 1642
	private bool _isActive;

	// Token: 0x0400066B RID: 1643
	private float _itemHeight;
}
﻿using System;
using System.Collections.Generic;
using MarsFPSKit;
using MarsFPSKit.Weapons;
using TMPro;
using UnityEngine;

// Token: 0x02000119 RID: 281
public class WeaponSlot : MonoBehaviour
{
	// Token: 0x0600074C RID: 1868 RVA: 0x000227D8 File Offset: 0x000209D8
	private void OnDisable()
	{
		LocalizationController.Instance.OnLanguageChanged -= this.Localize;
	}

	// Token: 0x0600074D RID: 1869 RVA: 0x000227F0 File Offset: 0x000209F0
	public void ViewInit(AttachmentSlot slot, int attachmentSlot, int weaponCategoryID, Kit_WeaponBase weapon, Kit_GameInformation game, WeaponDrawer weaponDrawer)
	{
		this._slot = slot;
		this._slotNameText.text = LocalizationController.Instance.GetText("Attachments", slot.name);
		this._prefabTransform = this._prefabAttachment.GetComponent<RectTransform>();
		this._slotNameTransform = this._slotNameText.GetComponent<RectTransform>();
		this.SetAttachments();
		this.SetStartValue(weaponCategoryID, attachmentSlot);
		this._lerpProcess = new LerpProcess();
		this._weapon = weapon;
		this._game = game;
		this._weaponDrawer = weaponDrawer;
		this._slotIndex = attachmentSlot;
		LocalizationController.Instance.OnLanguageChanged += this.Localize;
	}

	// Token: 0x0600074E RID: 1870 RVA: 0x00022894 File Offset: 0x00020A94
	public void ViewInit(WeaponAllSlots allSlots, AttachmentSlot slot, int attachmentSlot, int weaponCategoryID)
	{
		this._allSlots = allSlots;
		this._slot = slot;
		this._slotNameText.text = LocalizationController.Instance.GetText("Attachments", slot.name);
		this._prefabTransform = this._prefabAttachment.GetComponent<RectTransform>();
		this._slotNameTransform = this._slotNameText.GetComponent<RectTransform>();
		this.SetAttachments();
		this.SetStartValue(weaponCategoryID, attachmentSlot);
	}

	// Token: 0x0600074F RID: 1871 RVA: 0x00022900 File Offset: 0x00020B00
	public void OnAttachmentClick(WeaponAttachment attachment, bool isOpen)
	{
		WeaponAttachment currentAttachment = this._currentAttachment;
		if (currentAttachment != null)
		{
			currentAttachment.Deselect();
		}
		this._currentAttachment = attachment;
		this._currentAttachment.Select();
		this.SelectAttachment(this._slotIndex, this._attachments.IndexOf(attachment), isOpen);
		this.HideSlot();
	}

	// Token: 0x06000750 RID: 1872 RVA: 0x00022950 File Offset: 0x00020B50
	public float SetHeightToContent()
	{
		float num = this._slotNameTransform.sizeDelta.y + this._prefabTransform.sizeDelta.y * (float)this._slot.attachments.Length;
		this._slotContent.sizeDelta = new Vector2(this._slotContent.sizeDelta.x, num);
		return num;
	}

	// Token: 0x06000751 RID: 1873 RVA: 0x000229B0 File Offset: 0x00020BB0
	public void ShowSlot()
	{
		if (!this._isActive)
		{
			this._canvasGroup.interactable = true;
			this._canvasGroup.blocksRaycasts = true;
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._canvasGroup.alpha = Mathf.Lerp(0f, 1f, this._showCurve.Evaluate(t));
			}, this._showTime);
			this._isActive = true;
		}
	}

	// Token: 0x06000752 RID: 1874 RVA: 0x00022A04 File Offset: 0x00020C04
	public void HideSlot()
	{
		if (this._isActive)
		{
			this._canvasGroup.interactable = false;
			this._canvasGroup.blocksRaycasts = false;
			this._lerpProcess.StartProcess(delegate(float t)
			{
				this._canvasGroup.alpha = Mathf.Lerp(1f, 0f, this._showCurve.Evaluate(t));
			}, this._showTime);
			this._isActive = false;
		}
	}

	// Token: 0x06000753 RID: 1875 RVA: 0x00022A58 File Offset: 0x00020C58
	public void SelectAttachment(int choosenSlot, int choosenAttachment, bool isOpen)
	{
		if (isOpen)
		{
			LoadoutService.Instance.SelectAttachment(this._game.GetWeaponCategoryID(this._weapon.weaponType), choosenSlot, choosenAttachment);
			this._weaponDrawer.RedrawFirstPerson(this._game.GetWeaponCategoryID(this._weapon.weaponType), true, null);
			return;
		}
		this._weaponDrawer.RedrawAttachment(this._game.GetWeaponCategoryID(this._weapon.weaponType), choosenSlot, choosenAttachment);
	}

	// Token: 0x06000754 RID: 1876 RVA: 0x00022AD4 File Offset: 0x00020CD4
	private void SetAttachments()
	{
		this._attachments = new List<WeaponAttachment>();
		for (int i = 0; i < this._slot.attachments.Length; i++)
		{
			WeaponAttachment weaponAttachment = UnityEngine.Object.Instantiate<WeaponAttachment>(this._prefabAttachment, this._slotContent);
			weaponAttachment.ViewInit(this, this._slot.attachments[i]);
			this._attachments.Add(weaponAttachment);
		}
		this._slotContent.sizeDelta = new Vector2(1f, this._prefabAttachment.GetComponent<RectTransform>().sizeDelta.y * (float)this._slot.attachments.Length);
	}

	// Token: 0x06000755 RID: 1877 RVA: 0x00022B70 File Offset: 0x00020D70
	private void SetStartValue(int weaponCategoryID, int attachmentSlot)
	{
		int index = LoadoutService.Instance.GetSelecetedLoadout().loadoutWeapons[weaponCategoryID].attachments[attachmentSlot];
		WeaponAttachment currentAttachment = this._currentAttachment;
		if (currentAttachment != null)
		{
			currentAttachment.Deselect();
		}
		this._currentAttachment = this._attachments[index];
		this._currentAttachment.Select();
	}

	// Token: 0x06000756 RID: 1878 RVA: 0x00022BC4 File Offset: 0x00020DC4
	private void Localize()
	{
		this._slotNameText.text = LocalizationController.Instance.GetText("Attachments", this._slot.name);
	}

	// Token: 0x04000690 RID: 1680
	[SerializeField]
	private TextMeshProUGUI _slotNameText;

	// Token: 0x04000691 RID: 1681
	[SerializeField]
	private RectTransform _slotContent;

	// Token: 0x04000692 RID: 1682
	[SerializeField]
	private WeaponAttachment _prefabAttachment;

	// Token: 0x04000693 RID: 1683
	[SerializeField]
	private CanvasGroup _canvasGroup;

	// Token: 0x04000694 RID: 1684
	[SerializeField]
	private AnimationCurve _showCurve;

	// Token: 0x04000695 RID: 1685
	[SerializeField]
	private float _showTime;

	// Token: 0x04000696 RID: 1686
	private List<WeaponAttachment> _attachments;

	// Token: 0x04000697 RID: 1687
	private WeaponAttachment _currentAttachment;

	// Token: 0x04000698 RID: 1688
	private AttachmentSlot _slot;

	// Token: 0x04000699 RID: 1689
	private WeaponAllSlots _allSlots;

	// Token: 0x0400069A RID: 1690
	private RectTransform _prefabTransform;

	// Token: 0x0400069B RID: 1691
	private RectTransform _slotNameTransform;

	// Token: 0x0400069C RID: 1692
	private LerpProcess _lerpProcess;

	// Token: 0x0400069D RID: 1693
	private bool _isActive;

	// Token: 0x0400069E RID: 1694
	private Kit_WeaponBase _weapon;

	// Token: 0x0400069F RID: 1695
	private Kit_GameInformation _game;

	// Token: 0x040006A0 RID: 1696
	private WeaponDrawer _weaponDrawer;

	// Token: 0x040006A1 RID: 1697
	private int _slotIndex;
}
﻿using System;
using System.Collections;
using UnityEngine;

// Token: 0x0200003E RID: 62
[RequireComponent(typeof(MeshFilter))]
public class WFX_BulletHoleDecal : MonoBehaviour
{
	// Token: 0x0600016B RID: 363 RVA: 0x00008088 File Offset: 0x00006288
	private void Awake()
	{
		this.color = base.GetComponent<Renderer>().material.GetColor("_TintColor");
		this.orgAlpha = this.color.a;
	}

	// Token: 0x0600016C RID: 364 RVA: 0x000080B8 File Offset: 0x000062B8
	private void OnEnable()
	{
		int num = UnityEngine.Random.Range(0, (int)(this.frames.x * this.frames.y));
		int num2 = (int)((float)num % this.frames.x);
		int num3 = (int)((float)num / this.frames.y);
		Vector2[] array = new Vector2[4];
		for (int i = 0; i < 4; i++)
		{
			array[i].x = (WFX_BulletHoleDecal.quadUVs[i].x + (float)num2) * (1f / this.frames.x);
			array[i].y = (WFX_BulletHoleDecal.quadUVs[i].y + (float)num3) * (1f / this.frames.y);
		}
		base.GetComponent<MeshFilter>().mesh.uv = array;
		if (this.randomRotation)
		{
			base.transform.Rotate(0f, 0f, UnityEngine.Random.Range(0f, 360f), Space.Self);
		}
		this.life = this.lifetime;
		this.fadeout = this.life * (this.fadeoutpercent / 100f);
		this.color.a = this.orgAlpha;
		base.GetComponent<Renderer>().material.SetColor("_TintColor", this.color);
		base.StopAllCoroutines();
		base.StartCoroutine("holeUpdate");
	}

	// Token: 0x0600016D RID: 365 RVA: 0x00008218 File Offset: 0x00006418
	private IEnumerator holeUpdate()
	{
		while (this.life > 0f)
		{
			this.life -= Time.deltaTime;
			if (this.life <= this.fadeout)
			{
				this.color.a = Mathf.Lerp(0f, this.orgAlpha, this.life / this.fadeout);
				base.GetComponent<Renderer>().material.SetColor("_TintColor", this.color);
			}
			yield return null;
		}
		yield break;
	}

	// Token: 0x040000DF RID: 223
	private static Vector2[] quadUVs = new Vector2[]
	{
		new Vector2(0f, 0f),
		new Vector2(0f, 1f),
		new Vector2(1f, 0f),
		new Vector2(1f, 1f)
	};

	// Token: 0x040000E0 RID: 224
	public float lifetime = 10f;

	// Token: 0x040000E1 RID: 225
	public float fadeoutpercent = 80f;

	// Token: 0x040000E2 RID: 226
	public Vector2 frames;

	// Token: 0x040000E3 RID: 227
	public bool randomRotation;

	// Token: 0x040000E4 RID: 228
	public bool deactivate;

	// Token: 0x040000E5 RID: 229
	private float life;

	// Token: 0x040000E6 RID: 230
	private float fadeout;

	// Token: 0x040000E7 RID: 231
	private Color color;

	// Token: 0x040000E8 RID: 232
	private float orgAlpha;
}
﻿using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// Token: 0x02000010 RID: 16
public class WFX_Demo : MonoBehaviour
{
	// Token: 0x0600006C RID: 108 RVA: 0x00003320 File Offset: 0x00001520
	private void OnMouseDown()
	{
		RaycastHit raycastHit = default(RaycastHit);
		if (base.GetComponent<Collider>().Raycast(Camera.main.ScreenPointToRay(Input.mousePosition), out raycastHit, 9999f))
		{
			GameObject gameObject = this.spawnParticle();
			if (!gameObject.name.StartsWith("WFX_MF"))
			{
				gameObject.transform.position = raycastHit.point + gameObject.transform.position;
			}
		}
	}

	// Token: 0x0600006D RID: 109 RVA: 0x00003394 File Offset: 0x00001594
	public GameObject spawnParticle()
	{
		GameObject gameObject = UnityEngine.Object.Instantiate<GameObject>(this.ParticleExamples[this.exampleIndex]);
		if (gameObject.name.StartsWith("WFX_MF"))
		{
			gameObject.transform.parent = this.ParticleExamples[this.exampleIndex].transform.parent;
			gameObject.transform.localPosition = this.ParticleExamples[this.exampleIndex].transform.localPosition;
			gameObject.transform.localRotation = this.ParticleExamples[this.exampleIndex].transform.localRotation;
		}
		else if (gameObject.name.Contains("Hole"))
		{
			gameObject.transform.parent = this.bulletholes.transform;
		}
		this.SetActiveCrossVersions(gameObject, true);
		return gameObject;
	}

	// Token: 0x0600006E RID: 110 RVA: 0x00003460 File Offset: 0x00001660
	private void SetActiveCrossVersions(GameObject obj, bool active)
	{
		obj.SetActive(active);
		for (int i = 0; i < obj.transform.childCount; i++)
		{
			obj.transform.GetChild(i).gameObject.SetActive(active);
		}
	}

	// Token: 0x0600006F RID: 111 RVA: 0x000034A4 File Offset: 0x000016A4
	private void OnGUI()
	{
		GUILayout.BeginArea(new Rect(5f, 20f, (float)(Screen.width - 10), 60f));
		GUILayout.BeginHorizontal(Array.Empty<GUILayoutOption>());
		GUILayout.Label("Effect: " + this.ParticleExamples[this.exampleIndex].name, new GUILayoutOption[]
		{
			GUILayout.Width(280f)
		});
		if (GUILayout.Button("<", new GUILayoutOption[]
		{
			GUILayout.Width(30f)
		}))
		{
			this.prevParticle();
		}
		if (GUILayout.Button(">", new GUILayoutOption[]
		{
			GUILayout.Width(30f)
		}))
		{
			this.nextParticle();
		}
		GUILayout.FlexibleSpace();
		GUILayout.Label("Click on the ground to spawn the selected effect", Array.Empty<GUILayoutOption>());
		GUILayout.FlexibleSpace();
		if (GUILayout.Button(this.rotateCam ? "Pause Camera" : "Rotate Camera", new GUILayoutOption[]
		{
			GUILayout.Width(110f)
		}))
		{
			this.rotateCam = !this.rotateCam;
		}
		if (GUILayout.Button(base.GetComponent<Renderer>().enabled ? "Hide Ground" : "Show Ground", new GUILayoutOption[]
		{
			GUILayout.Width(90f)
		}))
		{
			base.GetComponent<Renderer>().enabled = !base.GetComponent<Renderer>().enabled;
		}
		if (GUILayout.Button(this.slowMo ? "Normal Speed" : "Slow Motion", new GUILayoutOption[]
		{
			GUILayout.Width(100f)
		}))
		{
			this.slowMo = !this.slowMo;
			if (this.slowMo)
			{
				Time.timeScale = 0.33f;
			}
			else
			{
				Time.timeScale = 1f;
			}
		}
		GUILayout.EndHorizontal();
		GUILayout.BeginHorizontal(Array.Empty<GUILayoutOption>());
		GUILayout.Label("Ground texture: " + this.groundTextureStr, new GUILayoutOption[]
		{
			GUILayout.Width(160f)
		});
		if (GUILayout.Button("<", new GUILayoutOption[]
		{
			GUILayout.Width(30f)
		}))
		{
			this.prevTexture();
		}
		if (GUILayout.Button(">", new GUILayoutOption[]
		{
			GUILayout.Width(30f)
		}))
		{
			this.nextTexture();
		}
		GUILayout.EndHorizontal();
		GUILayout.EndArea();
		if (this.m4.GetComponent<Renderer>().enabled)
		{
			GUILayout.BeginArea(new Rect(5f, (float)(Screen.height - 100), (float)(Screen.width - 10), 90f));
			this.rotate_m4 = GUILayout.Toggle(this.rotate_m4, "AutoRotate Weapon", new GUILayoutOption[]
			{
				GUILayout.Width(250f)
			});
			GUI.enabled = !this.rotate_m4;
			float num = this.m4.transform.localEulerAngles.x;
			num = ((num > 90f) ? (num - 180f) : num);
			float num2 = this.m4.transform.localEulerAngles.y;
			float num3 = this.m4.transform.localEulerAngles.z;
			num = GUILayout.HorizontalSlider(num, 0f, 179f, new GUILayoutOption[]
			{
				GUILayout.Width(256f)
			});
			num2 = GUILayout.HorizontalSlider(num2, 0f, 359f, new GUILayoutOption[]
			{
				GUILayout.Width(256f)
			});
			num3 = GUILayout.HorizontalSlider(num3, 0f, 359f, new GUILayoutOption[]
			{
				GUILayout.Width(256f)
			});
			if (GUI.changed)
			{
				if (num > 90f)
				{
					num += 180f;
				}
				this.m4.transform.localEulerAngles = new Vector3(num, num2, num3);
				Debug.Log(num);
			}
			GUILayout.EndArea();
		}
	}

	// Token: 0x06000070 RID: 112 RVA: 0x00003855 File Offset: 0x00001A55
	private IEnumerator RandomSpawnsCoroutine()
	{
		for (;;)
		{
			GameObject gameObject = this.spawnParticle();
			if (this.orderedSpawns)
			{
				gameObject.transform.position = base.transform.position + new Vector3(this.order, gameObject.transform.position.y, 0f);
				this.order -= this.step;
				if (this.order < -this.range)
				{
					this.order = this.range;
				}
			}
			else
			{
				gameObject.transform.position = base.transform.position + new Vector3(UnityEngine.Random.Range(-this.range, this.range), 0f, UnityEngine.Random.Range(-this.range, this.range)) + new Vector3(0f, gameObject.transform.position.y, 0f);
			}
			yield return new WaitForSeconds(float.Parse(this.randomSpawnsDelay));
		}
		yield break;
	}

	// Token: 0x06000071 RID: 113 RVA: 0x00003864 File Offset: 0x00001A64
	private void Update()
	{
		if (Input.GetKeyDown(KeyCode.LeftArrow))
		{
			this.prevParticle();
		}
		else if (Input.GetKeyDown(KeyCode.RightArrow))
		{
			this.nextParticle();
		}
		if (this.rotateCam)
		{
			Camera.main.transform.RotateAround(Vector3.zero, Vector3.up, this.cameraSpeed * Time.deltaTime);
		}
		if (this.rotate_m4)
		{
			this.m4.transform.Rotate(new Vector3(0f, 40f, 0f) * Time.deltaTime, Space.World);
		}
	}

	// Token: 0x06000072 RID: 114 RVA: 0x000038FC File Offset: 0x00001AFC
	private void prevTexture()
	{
		int num = this.groundTextures.IndexOf(this.groundTextureStr);
		num--;
		if (num < 0)
		{
			num = this.groundTextures.Count - 1;
		}
		this.groundTextureStr = this.groundTextures[num];
		this.selectMaterial();
	}

	// Token: 0x06000073 RID: 115 RVA: 0x0000394C File Offset: 0x00001B4C
	private void nextTexture()
	{
		int num = this.groundTextures.IndexOf(this.groundTextureStr);
		num++;
		if (num >= this.groundTextures.Count)
		{
			num = 0;
		}
		this.groundTextureStr = this.groundTextures[num];
		this.selectMaterial();
	}

	// Token: 0x06000074 RID: 116 RVA: 0x00003998 File Offset: 0x00001B98
	private void selectMaterial()
	{
		string a = this.groundTextureStr;
		if (a == "Concrete")
		{
			base.GetComponent<Renderer>().material = this.concrete;
			this.walls.transform.GetChild(0).GetComponent<Renderer>().material = this.concreteWall;
			this.walls.transform.GetChild(1).GetComponent<Renderer>().material = this.concreteWall;
			return;
		}
		if (a == "Wood")
		{
			base.GetComponent<Renderer>().material = this.wood;
			this.walls.transform.GetChild(0).GetComponent<Renderer>().material = this.woodWall;
			this.walls.transform.GetChild(1).GetComponent<Renderer>().material = this.woodWall;
			return;
		}
		if (a == "Metal")
		{
			base.GetComponent<Renderer>().material = this.metal;
			this.walls.transform.GetChild(0).GetComponent<Renderer>().material = this.metalWall;
			this.walls.transform.GetChild(1).GetComponent<Renderer>().material = this.metalWall;
			return;
		}
		if (!(a == "Checker"))
		{
			return;
		}
		base.GetComponent<Renderer>().material = this.checker;
		this.walls.transform.GetChild(0).GetComponent<Renderer>().material = this.checkerWall;
		this.walls.transform.GetChild(1).GetComponent<Renderer>().material = this.checkerWall;
	}

	// Token: 0x06000075 RID: 117 RVA: 0x00003B36 File Offset: 0x00001D36
	private void prevParticle()
	{
		this.exampleIndex--;
		if (this.exampleIndex < 0)
		{
			this.exampleIndex = this.ParticleExamples.Length - 1;
		}
		this.showHideStuff();
	}

	// Token: 0x06000076 RID: 118 RVA: 0x00003B65 File Offset: 0x00001D65
	private void nextParticle()
	{
		this.exampleIndex++;
		if (this.exampleIndex >= this.ParticleExamples.Length)
		{
			this.exampleIndex = 0;
		}
		this.showHideStuff();
	}

	// Token: 0x06000077 RID: 119 RVA: 0x00003B94 File Offset: 0x00001D94
	private void showHideStuff()
	{
		if (this.ParticleExamples[this.exampleIndex].name.StartsWith("WFX_MF Spr"))
		{
			this.m4.GetComponent<Renderer>().enabled = true;
		}
		else
		{
			this.m4.GetComponent<Renderer>().enabled = false;
		}
		if (this.ParticleExamples[this.exampleIndex].name.StartsWith("WFX_MF FPS"))
		{
			this.m4fps.GetComponent<Renderer>().enabled = true;
		}
		else
		{
			this.m4fps.GetComponent<Renderer>().enabled = false;
		}
		if (this.ParticleExamples[this.exampleIndex].name.StartsWith("WFX_BImpact"))
		{
			this.SetActiveCrossVersions(this.walls, true);
			Renderer[] componentsInChildren = this.bulletholes.GetComponentsInChildren<Renderer>();
			for (int i = 0; i < componentsInChildren.Length; i++)
			{
				componentsInChildren[i].enabled = true;
			}
		}
		else
		{
			this.SetActiveCrossVersions(this.walls, false);
			Renderer[] componentsInChildren = this.bulletholes.GetComponentsInChildren<Renderer>();
			for (int i = 0; i < componentsInChildren.Length; i++)
			{
				componentsInChildren[i].enabled = false;
			}
		}
		if (this.ParticleExamples[this.exampleIndex].name.Contains("Wood"))
		{
			this.groundTextureStr = "Wood";
			this.selectMaterial();
			return;
		}
		if (this.ParticleExamples[this.exampleIndex].name.Contains("Concrete"))
		{
			this.groundTextureStr = "Concrete";
			this.selectMaterial();
			return;
		}
		if (this.ParticleExamples[this.exampleIndex].name.Contains("Metal"))
		{
			this.groundTextureStr = "Metal";
			this.selectMaterial();
			return;
		}
		if (this.ParticleExamples[this.exampleIndex].name.Contains("Dirt") || this.ParticleExamples[this.exampleIndex].name.Contains("Sand") || this.ParticleExamples[this.exampleIndex].name.Contains("SoftBody"))
		{
			this.groundTextureStr = "Checker";
			this.selectMaterial();
			return;
		}
		if (this.ParticleExamples[this.exampleIndex].name == "WFX_Explosion")
		{
			this.groundTextureStr = "Checker";
			this.selectMaterial();
		}
	}

	// Token: 0x04000025 RID: 37
	public float cameraSpeed = 10f;

	// Token: 0x04000026 RID: 38
	public bool orderedSpawns = true;

	// Token: 0x04000027 RID: 39
	public float step = 1f;

	// Token: 0x04000028 RID: 40
	public float range = 5f;

	// Token: 0x04000029 RID: 41
	private float order = -5f;

	// Token: 0x0400002A RID: 42
	public GameObject walls;

	// Token: 0x0400002B RID: 43
	public GameObject bulletholes;

	// Token: 0x0400002C RID: 44
	public GameObject[] ParticleExamples;

	// Token: 0x0400002D RID: 45
	private int exampleIndex;

	// Token: 0x0400002E RID: 46
	private string randomSpawnsDelay = "0.5";

	// Token: 0x0400002F RID: 47
	private bool randomSpawns;

	// Token: 0x04000030 RID: 48
	private bool slowMo;

	// Token: 0x04000031 RID: 49
	private bool rotateCam = true;

	// Token: 0x04000032 RID: 50
	public Material wood;

	// Token: 0x04000033 RID: 51
	public Material concrete;

	// Token: 0x04000034 RID: 52
	public Material metal;

	// Token: 0x04000035 RID: 53
	public Material checker;

	// Token: 0x04000036 RID: 54
	public Material woodWall;

	// Token: 0x04000037 RID: 55
	public Material concreteWall;

	// Token: 0x04000038 RID: 56
	public Material metalWall;

	// Token: 0x04000039 RID: 57
	public Material checkerWall;

	// Token: 0x0400003A RID: 58
	private string groundTextureStr = "Checker";

	// Token: 0x0400003B RID: 59
	private List<string> groundTextures = new List<string>(new string[]
	{
		"Concrete",
		"Wood",
		"Metal",
		"Checker"
	});

	// Token: 0x0400003C RID: 60
	public GameObject m4;

	// Token: 0x0400003D RID: 61
	public GameObject m4fps;

	// Token: 0x0400003E RID: 62
	private bool rotate_m4 = true;
}
﻿using System;
using UnityEngine;

// Token: 0x02000011 RID: 17
public class WFX_Demo_DeleteAfterDelay : MonoBehaviour
{
	// Token: 0x06000079 RID: 121 RVA: 0x00003E6B File Offset: 0x0000206B
	private void Update()
	{
		this.delay -= Time.deltaTime;
		if (this.delay < 0f)
		{
			UnityEngine.Object.Destroy(base.gameObject);
		}
	}

	// Token: 0x0400003F RID: 63
	public float delay = 1f;
}
﻿using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

// Token: 0x02000012 RID: 18
public class WFX_Demo_New : MonoBehaviour
{
	// Token: 0x0600007B RID: 123 RVA: 0x00003EAC File Offset: 0x000020AC
	private void Awake()
	{
		List<GameObject> list = new List<GameObject>();
		int childCount = base.transform.childCount;
		for (int i = 0; i < childCount; i++)
		{
			GameObject gameObject = base.transform.GetChild(i).gameObject;
			list.Add(gameObject);
		}
		list.AddRange(this.AdditionalEffects);
		this.ParticleExamples = list.ToArray();
		this.defaultCamPosition = Camera.main.transform.position;
		this.defaultCamRotation = Camera.main.transform.rotation;
		base.StartCoroutine("CheckForDeletedParticles");
		this.UpdateUI();
	}

	// Token: 0x0600007C RID: 124 RVA: 0x00003F44 File Offset: 0x00002144
	private void Update()
	{
		if (Input.GetKeyDown(KeyCode.LeftArrow))
		{
			this.prevParticle();
		}
		else if (Input.GetKeyDown(KeyCode.RightArrow))
		{
			this.nextParticle();
		}
		else if (Input.GetKeyDown(KeyCode.Delete))
		{
			this.destroyParticles();
		}
		if (Input.GetMouseButtonDown(0))
		{
			RaycastHit raycastHit = default(RaycastHit);
			if (this.groundCollider.Raycast(Camera.main.ScreenPointToRay(Input.mousePosition), out raycastHit, 9999f))
			{
				GameObject gameObject = this.spawnParticle();
				if (!gameObject.name.StartsWith("WFX_MF"))
				{
					gameObject.transform.position = raycastHit.point + gameObject.transform.position;
				}
			}
		}
		float axis = Input.GetAxis("Mouse ScrollWheel");
		if (axis != 0f)
		{
			Camera.main.transform.Translate(Vector3.forward * ((axis < 0f) ? -1f : 1f), Space.Self);
		}
		if (Input.GetMouseButtonDown(2))
		{
			Camera.main.transform.position = this.defaultCamPosition;
			Camera.main.transform.rotation = this.defaultCamRotation;
		}
	}

	// Token: 0x0600007D RID: 125 RVA: 0x0000406C File Offset: 0x0000226C
	public void OnToggleGround()
	{
		Color white = Color.white;
		this.groundRenderer.enabled = !this.groundRenderer.enabled;
		white.a = (this.groundRenderer.enabled ? 1f : 0.33f);
		this.groundBtn.color = white;
		this.groundLabel.color = white;
	}

	// Token: 0x0600007E RID: 126 RVA: 0x000040D0 File Offset: 0x000022D0
	public void OnToggleCamera()
	{
		Color white = Color.white;
		CFX_Demo_RotateCamera.rotating = !CFX_Demo_RotateCamera.rotating;
		white.a = (CFX_Demo_RotateCamera.rotating ? 1f : 0.33f);
		this.camRotBtn.color = white;
		this.camRotLabel.color = white;
	}

	// Token: 0x0600007F RID: 127 RVA: 0x00004124 File Offset: 0x00002324
	public void OnToggleSlowMo()
	{
		Color white = Color.white;
		this.slowMo = !this.slowMo;
		if (this.slowMo)
		{
			Time.timeScale = 0.33f;
			white.a = 1f;
		}
		else
		{
			Time.timeScale = 1f;
			white.a = 0.33f;
		}
		this.slowMoBtn.color = white;
		this.slowMoLabel.color = white;
	}

	// Token: 0x06000080 RID: 128 RVA: 0x00004194 File Offset: 0x00002394
	public void OnPreviousEffect()
	{
		this.prevParticle();
	}

	// Token: 0x06000081 RID: 129 RVA: 0x0000419C File Offset: 0x0000239C
	public void OnNextEffect()
	{
		this.nextParticle();
	}

	// Token: 0x06000082 RID: 130 RVA: 0x000041A4 File Offset: 0x000023A4
	private void UpdateUI()
	{
		this.EffectLabel.text = this.ParticleExamples[this.exampleIndex].name;
		this.EffectIndexLabel.text = string.Format("{0}/{1}", (this.exampleIndex + 1).ToString("00"), this.ParticleExamples.Length.ToString("00"));
	}

	// Token: 0x06000083 RID: 131 RVA: 0x00004210 File Offset: 0x00002410
	public GameObject spawnParticle()
	{
		GameObject gameObject = UnityEngine.Object.Instantiate<GameObject>(this.ParticleExamples[this.exampleIndex]);
		gameObject.transform.position = new Vector3(0f, gameObject.transform.position.y, 0f);
		gameObject.SetActive(true);
		if (gameObject.name.StartsWith("WFX_MF"))
		{
			gameObject.transform.parent = this.ParticleExamples[this.exampleIndex].transform.parent;
			gameObject.transform.localPosition = this.ParticleExamples[this.exampleIndex].transform.localPosition;
			gameObject.transform.localRotation = this.ParticleExamples[this.exampleIndex].transform.localRotation;
		}
		else if (gameObject.name.Contains("Hole"))
		{
			gameObject.transform.parent = this.bulletholes.transform;
		}
		ParticleSystem component = gameObject.GetComponent<ParticleSystem>();
		if (component != null && component.main.loop)
		{
			component.gameObject.AddComponent<CFX_AutoStopLoopedEffect>();
			component.gameObject.AddComponent<CFX_AutoDestructShuriken>();
		}
		this.onScreenParticles.Add(gameObject);
		return gameObject;
	}

	// Token: 0x06000084 RID: 132 RVA: 0x00004348 File Offset: 0x00002548
	private IEnumerator CheckForDeletedParticles()
	{
		for (;;)
		{
			yield return new WaitForSeconds(5f);
			for (int i = this.onScreenParticles.Count - 1; i >= 0; i--)
			{
				if (this.onScreenParticles[i] == null)
				{
					this.onScreenParticles.RemoveAt(i);
				}
			}
		}
		yield break;
	}

	// Token: 0x06000085 RID: 133 RVA: 0x00004357 File Offset: 0x00002557
	private void prevParticle()
	{
		this.exampleIndex--;
		if (this.exampleIndex < 0)
		{
			this.exampleIndex = this.ParticleExamples.Length - 1;
		}
		this.UpdateUI();
		this.showHideStuff();
	}

	// Token: 0x06000086 RID: 134 RVA: 0x0000438C File Offset: 0x0000258C
	private void nextParticle()
	{
		this.exampleIndex++;
		if (this.exampleIndex >= this.ParticleExamples.Length)
		{
			this.exampleIndex = 0;
		}
		this.UpdateUI();
		this.showHideStuff();
	}

	// Token: 0x06000087 RID: 135 RVA: 0x000043C0 File Offset: 0x000025C0
	private void destroyParticles()
	{
		for (int i = this.onScreenParticles.Count - 1; i >= 0; i--)
		{
			if (this.onScreenParticles[i] != null)
			{
				UnityEngine.Object.Destroy(this.onScreenParticles[i]);
			}
			this.onScreenParticles.RemoveAt(i);
		}
	}

	// Token: 0x06000088 RID: 136 RVA: 0x00004418 File Offset: 0x00002618
	private void prevTexture()
	{
		int num = this.groundTextures.IndexOf(this.groundTextureStr);
		num--;
		if (num < 0)
		{
			num = this.groundTextures.Count - 1;
		}
		this.groundTextureStr = this.groundTextures[num];
		this.selectMaterial();
	}

	// Token: 0x06000089 RID: 137 RVA: 0x00004468 File Offset: 0x00002668
	private void nextTexture()
	{
		int num = this.groundTextures.IndexOf(this.groundTextureStr);
		num++;
		if (num >= this.groundTextures.Count)
		{
			num = 0;
		}
		this.groundTextureStr = this.groundTextures[num];
		this.selectMaterial();
	}

	// Token: 0x0600008A RID: 138 RVA: 0x000044B4 File Offset: 0x000026B4
	private void selectMaterial()
	{
		string a = this.groundTextureStr;
		if (a == "Concrete")
		{
			this.ground.GetComponent<Renderer>().material = this.concrete;
			this.walls.transform.GetChild(0).GetComponent<Renderer>().material = this.concreteWall;
			this.walls.transform.GetChild(1).GetComponent<Renderer>().material = this.concreteWall;
			return;
		}
		if (a == "Wood")
		{
			this.ground.GetComponent<Renderer>().material = this.wood;
			this.walls.transform.GetChild(0).GetComponent<Renderer>().material = this.woodWall;
			this.walls.transform.GetChild(1).GetComponent<Renderer>().material = this.woodWall;
			return;
		}
		if (a == "Metal")
		{
			this.ground.GetComponent<Renderer>().material = this.metal;
			this.walls.transform.GetChild(0).GetComponent<Renderer>().material = this.metalWall;
			this.walls.transform.GetChild(1).GetComponent<Renderer>().material = this.metalWall;
			return;
		}
		if (!(a == "Checker"))
		{
			return;
		}
		this.ground.GetComponent<Renderer>().material = this.checker;
		this.walls.transform.GetChild(0).GetComponent<Renderer>().material = this.checkerWall;
		this.walls.transform.GetChild(1).GetComponent<Renderer>().material = this.checkerWall;
	}

	// Token: 0x0600008B RID: 139 RVA: 0x00004668 File Offset: 0x00002868
	private void showHideStuff()
	{
		if (this.ParticleExamples[this.exampleIndex].name.StartsWith("WFX_MF Spr"))
		{
			this.m4.GetComponent<Renderer>().enabled = true;
			Camera.main.transform.position = new Vector3(-2.482457f, 3.263842f, -0.004924395f);
			Camera.main.transform.eulerAngles = new Vector3(20f, 90f, 0f);
		}
		else
		{
			this.m4.GetComponent<Renderer>().enabled = false;
		}
		if (this.ParticleExamples[this.exampleIndex].name.StartsWith("WFX_MF FPS"))
		{
			this.m4fps.GetComponent<Renderer>().enabled = true;
		}
		else
		{
			this.m4fps.GetComponent<Renderer>().enabled = false;
		}
		if (this.ParticleExamples[this.exampleIndex].name.StartsWith("WFX_BImpact"))
		{
			this.walls.SetActive(true);
			Renderer[] componentsInChildren = this.bulletholes.GetComponentsInChildren<Renderer>();
			for (int i = 0; i < componentsInChildren.Length; i++)
			{
				componentsInChildren[i].enabled = true;
			}
		}
		else
		{
			this.walls.SetActive(false);
			Renderer[] componentsInChildren = this.bulletholes.GetComponentsInChildren<Renderer>();
			for (int i = 0; i < componentsInChildren.Length; i++)
			{
				componentsInChildren[i].enabled = false;
			}
		}
		if (this.ParticleExamples[this.exampleIndex].name.Contains("Wood"))
		{
			this.groundTextureStr = "Wood";
			this.selectMaterial();
			return;
		}
		if (this.ParticleExamples[this.exampleIndex].name.Contains("Concrete"))
		{
			this.groundTextureStr = "Concrete";
			this.selectMaterial();
			return;
		}
		if (this.ParticleExamples[this.exampleIndex].name.Contains("Metal"))
		{
			this.groundTextureStr = "Metal";
			this.selectMaterial();
			return;
		}
		if (this.ParticleExamples[this.exampleIndex].name.Contains("Dirt") || this.ParticleExamples[this.exampleIndex].name.Contains("Sand") || this.ParticleExamples[this.exampleIndex].name.Contains("SoftBody"))
		{
			this.groundTextureStr = "Checker";
			this.selectMaterial();
			return;
		}
		if (this.ParticleExamples[this.exampleIndex].name == "WFX_Explosion")
		{
			this.groundTextureStr = "Checker";
			this.selectMaterial();
		}
	}

	// Token: 0x04000040 RID: 64
	public Renderer groundRenderer;

	// Token: 0x04000041 RID: 65
	public Collider groundCollider;

	// Token: 0x04000042 RID: 66
	[Space]
	[Space]
	public Image slowMoBtn;

	// Token: 0x04000043 RID: 67
	public Text slowMoLabel;

	// Token: 0x04000044 RID: 68
	public Image camRotBtn;

	// Token: 0x04000045 RID: 69
	public Text camRotLabel;

	// Token: 0x04000046 RID: 70
	public Image groundBtn;

	// Token: 0x04000047 RID: 71
	public Text groundLabel;

	// Token: 0x04000048 RID: 72
	[Space]
	public Text EffectLabel;

	// Token: 0x04000049 RID: 73
	public Text EffectIndexLabel;

	// Token: 0x0400004A RID: 74
	public GameObject[] AdditionalEffects;

	// Token: 0x0400004B RID: 75
	public GameObject ground;

	// Token: 0x0400004C RID: 76
	public GameObject walls;

	// Token: 0x0400004D RID: 77
	public GameObject bulletholes;

	// Token: 0x0400004E RID: 78
	public GameObject m4;

	// Token: 0x0400004F RID: 79
	public GameObject m4fps;

	// Token: 0x04000050 RID: 80
	public Material wood;

	// Token: 0x04000051 RID: 81
	public Material concrete;

	// Token: 0x04000052 RID: 82
	public Material metal;

	// Token: 0x04000053 RID: 83
	public Material checker;

	// Token: 0x04000054 RID: 84
	public Material woodWall;

	// Token: 0x04000055 RID: 85
	public Material concreteWall;

	// Token: 0x04000056 RID: 86
	public Material metalWall;

	// Token: 0x04000057 RID: 87
	public Material checkerWall;

	// Token: 0x04000058 RID: 88
	private string groundTextureStr = "Checker";

	// Token: 0x04000059 RID: 89
	private List<string> groundTextures = new List<string>(new string[]
	{
		"Concrete",
		"Wood",
		"Metal",
		"Checker"
	});

	// Token: 0x0400005A RID: 90
	private GameObject[] ParticleExamples;

	// Token: 0x0400005B RID: 91
	private int exampleIndex;

	// Token: 0x0400005C RID: 92
	private bool slowMo;

	// Token: 0x0400005D RID: 93
	private Vector3 defaultCamPosition;

	// Token: 0x0400005E RID: 94
	private Quaternion defaultCamRotation;

	// Token: 0x0400005F RID: 95
	private List<GameObject> onScreenParticles = new List<GameObject>();
}
﻿using System;
using UnityEngine;

// Token: 0x02000013 RID: 19
public class WFX_Demo_RandomDir : MonoBehaviour
{
	// Token: 0x0600008D RID: 141 RVA: 0x00004944 File Offset: 0x00002B44
	private void Awake()
	{
		base.transform.eulerAngles = new Vector3(UnityEngine.Random.Range(this.min.x, this.max.x), UnityEngine.Random.Range(this.min.y, this.max.y), UnityEngine.Random.Range(this.min.z, this.max.z));
	}

	// Token: 0x04000060 RID: 96
	public Vector3 min = new Vector3(0f, 0f, 0f);

	// Token: 0x04000061 RID: 97
	public Vector3 max = new Vector3(0f, 360f, 0f);
}
﻿using System;
using UnityEngine;

// Token: 0x02000014 RID: 20
public class WFX_Demo_Wall : MonoBehaviour
{
	// Token: 0x0600008F RID: 143 RVA: 0x000049F0 File Offset: 0x00002BF0
	private void OnMouseDown()
	{
		RaycastHit raycastHit = default(RaycastHit);
		if (base.GetComponent<Collider>().Raycast(Camera.main.ScreenPointToRay(Input.mousePosition), out raycastHit, 9999f))
		{
			GameObject gameObject = this.demo.spawnParticle();
			gameObject.transform.position = raycastHit.point;
			gameObject.transform.rotation = Quaternion.FromToRotation(Vector3.forward, raycastHit.normal);
		}
	}

	// Token: 0x04000062 RID: 98
	public WFX_Demo_New demo;
}
﻿using System;
using System.Collections;
using UnityEngine;

// Token: 0x0200003F RID: 63
[RequireComponent(typeof(Light))]
public class WFX_LightFlicker : MonoBehaviour
{
	// Token: 0x06000170 RID: 368 RVA: 0x000082B8 File Offset: 0x000064B8
	private void Start()
	{
		this.timer = this.time;
		base.StartCoroutine("Flicker");
	}

	// Token: 0x06000171 RID: 369 RVA: 0x000082D2 File Offset: 0x000064D2
	private IEnumerator Flicker()
	{
		for (;;)
		{
			base.GetComponent<Light>().enabled = !base.GetComponent<Light>().enabled;
			do
			{
				this.timer -= Time.deltaTime;
				yield return null;
			}
			while (this.timer > 0f);
			this.timer = this.time;
		}
		yield break;
	}

	// Token: 0x040000E9 RID: 233
	public float time = 0.05f;

	// Token: 0x040000EA RID: 234
	private float timer;
}
