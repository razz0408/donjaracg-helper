<script setup>
const props = defineProps(['produceIdol', 'handIdols'])
</script>

<script>
import UnitData from "../../assets/unit_data.json";
import CuteIdols from "../../assets/idol_data_cute.json";
import CoolIdols from "../../assets/idol_data_cool.json";
import PassionIdols from "../../assets/idol_data_passion.json";

export default {
  data() {
    return {
      unitData: UnitData.unit_data,
      cuteIdols: CuteIdols.idol_data,
      coolIdols: CoolIdols.idol_data,
      passionIdols: PassionIdols.idol_data,
      idolData: [...CuteIdols.idol_data, ...CoolIdols.idol_data, ...PassionIdols.idol_data],
      score: 0,
      // TODO: 設定で切り替えられるようにする
      useLocalRule: false,
      spScoreA: 60,
      spScoreB: 42,
      spScoreC: 42,
      type_pattern: ['cute', 'cool', 'passion'],
      status_pattern: ['Vi', 'Da', 'Vo'],
      stageUnits: []
    }
  },
  computed: {
  },
  methods: {
    // アイドル名のリストからアイドルデータ (実質牌データ) を返す
    getIdolsByNames: function(nameArray) {
      var idols = [];

      for(var idolName of nameArray) {
        for(var idol of this.idolData) {
          if(idol.name === idolName) {
            idols.push(idol);
            continue;
          }
        }
      }

      return idols;
    },

    getFilteredHandsArray(idols) {
      // Cute - Vi Da Vo
      var handsCuVi = this.getFilteredHands(idols, "cute", "Vi");
      var handsCuDa = this.getFilteredHands(idols, "cute", "Da");
      var handsCuVo = this.getFilteredHands(idols, "cute", "Vo");

      // Cool - Vi Da Vo
      var handsCoVi = this.getFilteredHands(idols, "cool", "Vi");
      var handsCoDa = this.getFilteredHands(idols, "cool", "Da");
      var handsCoVo = this.getFilteredHands(idols, "cool", "Vo");
      
      // Passion - Vi Da Vo
      var handsPaVi = this.getFilteredHands(idols, "passion", "Vi");
      var handsPaDa = this.getFilteredHands(idols, "passion", "Da");
      var handsPaVo = this.getFilteredHands(idols, "passion", "Vo");

      return [handsCuVi, handsCuDa, handsCuVo, handsCoVi, handsCoDa, handsCoVo, handsPaVi, handsPaDa, handsPaVo];
    },

    checkStatusUnit(idols, value) {
      var filteredHands = this.getFilteredHandsArray(idols);
      
      // 各属性の5枚セットのセット数を抽出
      // Cute
      var setCuVi, setCuDa, setCuVo = 0;
      setCuVi = filteredHands[0].length % value == 0 ? filteredHands[0].length / value : 0
      setCuDa = filteredHands[1].length % value == 0 ? filteredHands[1].length / value : 0
      setCuVo = filteredHands[2].length % value == 0 ? filteredHands[2].length / value : 0

      // Cool
      var setCoVi, setCoDa, setCoVo = 0;
      setCoVi = filteredHands[3].length % value == 0 ? filteredHands[3].length / value : 0
      setCoDa = filteredHands[4].length % value == 0 ? filteredHands[4].length / value : 0
      setCoVo = filteredHands[5].length % value == 0 ? filteredHands[5].length / value : 0

      // Passion
      var setPaVi, setPaDa, setPaVo = 0;
      setPaVi = filteredHands[6].length % value == 0 ? filteredHands[6].length / value : 0
      setPaDa = filteredHands[7].length % value == 0 ? filteredHands[7].length / value : 0
      setPaVo = filteredHands[8].length % value == 0 ? filteredHands[8].length / value : 0
      
      var setCounts = [setCuVi, setCuDa, setCuVo, setCoVi, setCoDa, setCoVo, setPaVi, setPaDa, setPaVo];

      // 5スターユニット                 / ノーマルライブユニット          / スタートダッシュライブユニット
      // 5人ユニ + 2人セット + 2人セット / 3人ユニ + 3人セット + 3人セット / 3人ユニ + 2人ユニ + 2人セット + 2人セット
      if ((idols.length === 4 || idols.length === 6) && !setCounts.every(count => count === 0) && setCounts.filter(num => num === 1).length === 2) {
        const resultIndexes = setCounts.flatMap((s, i) => (s === 1 ? i : []));
        console.log(resultIndexes);

        for(var resulitIndex of resultIndexes) {
          var generic_unit = this.getGenericUnitData(resulitIndex, filteredHands[resulitIndex]);

          if(!this.stageUnits.some(unitObj => unitObj === generic_unit)) {
            this.stageUnits.push(generic_unit);
          }
        }
        return true;
      } else if(!(idols.length === 4 || idols.length === 6) && setCounts.some(count => count === 1)) {
        var unitInHands = filteredHands[setCounts.indexOf(1)];

        if(value === 2 || value === 3) {
          var generic_unit = this.getGenericUnitData(setCounts.indexOf(1), filteredHands[setCounts.indexOf(1)]);

          if(!this.stageUnits.some(unitObj => unitObj === generic_unit)) {
            this.stageUnits.push(generic_unit);
            return true;
          }
        }

        if(value === 4 || value === 5 || value === 6) {
          for(var unitIdols of this.sliceUnits(unitInHands, value)) {
            var generic_unit = this.getGenericUnitData(setCounts.indexOf(1), unitIdols);

            if(!this.stageUnits.some(unitObj => unitObj === generic_unit)) {
              this.stageUnits.push(generic_unit);
            }
          }
          return true;
        }
        
        return false;
      } else {
        return false;
      }
    },

    sliceUnits(array, value) {
      switch(value) {
        case 4:
          return [array.slice(0,2), array.slice(2)];
        case 5:
          return [array.slice(0,3), array.slice(3)];
        case 6:
          return [array.slice(0,3), array.slice(3)];
          
        default:
          console.log(array)
          console.log(value)
          break;
      }
    },

    // オールマイティを除外した、アイドル名のリストを返す
    getIdolNames(handIdols) {
      var idols = [];
      
      // ユニット用に抽出
      for(var idol of handIdols) {
        // オールマイティ除外
        if(idol.name == "オールマイティ") {
          // スキップ
          continue;
        }

        // アイドル名抽出
        idols.push(idol.name);
      }

      return idols;
    },

    // 手牌の各属性・各ステータスのアイドルを抽出して返す
    getFilteredHands(handIdols, type, status) {
      if(this.type_pattern.includes(type) && this.status_pattern.includes(status)) {
        return handIdols.filter((idol) => idol.type == type && idol.first_status == status);
      } else {
        return handIdols;
      }
    },

    // ユニット結成分を除外してアイドル名のリストを返す
    getExcludeUnitMembers(idols, unitMembers) {
      const excludeIdols = [...idols];

      for(var unitMember of unitMembers) {
        if(excludeIdols.includes(unitMember)) {
          var index = excludeIdols.indexOf(unitMember);
          excludeIdols.splice(index, 1);
        }
      }

      return excludeIdols;
    },

    // メンバーがキュートのみの3人ユニットを返す
    getCuteTrioUnits() {
      var cuteIdols = this.getIdolNames(this.cuteIdols);
      var cuteUnits = this.unitData.filter((unit) => unit.members.split(',').length == 3)
      .filter((unit) => unit.members.split(',').every(unitMember => cuteIdols.includes(unitMember)));

      return cuteUnits;
    },
    
    // メンバーがクールのみの3人ユニットを返す
    getCoolTrioUnits() {
      var coolIdols = this.getIdolNames(this.coolIdols);
      var coolUnits = this.unitData.filter((unit) => unit.members.split(',').length == 3)
      .filter((unit) => unit.members.split(',').every(unitMember => coolIdols.includes(unitMember)));

      return coolUnits;
    },

    // メンバーがパッションのみの3人ユニットを返す
    getPassionTrioUnits() {
      var passionIdols = this.getIdolNames(this.passionIdols);
      var passionUnits = this.unitData.filter((unit) => unit.members.split(',').length == 3)
      .filter((unit) => unit.members.split(',').every(unitMember => passionIdols.includes(unitMember)));

      return passionUnits;
    },

    // [60万人] 歴代シンデレラガール
    getCinderellaGirls() {
      return this.unitData.filter((unit) => unit.unit_name === "歴代シンデレラガール");
    },
    
    // (ローカル役)
    // [60万人] 9人以上 12人未満
    // 例: Project:Krone, ETERNITY MEMORIES, EVERLASTING
    getConcealedUnits() {
      return this.unitData.filter((unit) => unit.members.split(',').length >= 9 && unit.members.split(',').length < 12 && unit.unit_name != "歴代シンデレラガール"); 
    },

    // [48万人] WONDERFUL M@GIC!! / 歴代シンデレラガールズ 以外の 12人以上ユニット
    // 例: CINDERELLA PROJECT
    getMagicUnits() {
      return this.unitData.filter((unit) => unit.members.split(',').length >= 12 && unit.unit_name != "歴代シンデレラガール"); 
    },

    // (ローカル役)
    // [42万人] 7 + 2 | SPライブセットA
    getSeventhUnits() {
      return this.unitData.filter((unit) => unit.members.split(',').length == 7);
    },

    // (ローカル役)
    // [42万人] 6 + 3 | SPライブセットB
    getSextetUnits() {
      return this.unitData.filter((unit) => unit.members.split(',').length == 6);
    },

    // [36万人] 5スターユニット
    getFiveStarUnits() {
      return this.unitData.filter((unit) => unit.members.split(',').length == 5);
    },

    // [36万人] カルテットユニット
    getQuartetUnits() {
      return this.unitData.filter((unit) => unit.members.split(',').length == 4);
    },

    // [12万人 ~ 24万人] トリコロールユニット / ノーマルライブユニット
    getTrioUnits() {
      return this.unitData.filter((unit) => unit.members.split(',').length == 3);
    },

    // [6万人 ～ 36万人] スタートダッシュライブユニット / カルテットユニット / 5スターユニット
    getDuoUnits() {
      return this.unitData.filter((unit) => unit.members.split(',').length == 2);
    },

    getCombinations(arr, number) {
      let ans = []
      if (arr.length < number) {
        return console.log("[ERROR] getCombinationsの第2引数は第1引数の配列数より少ない数としてください。")
      }
      if (number === 1) {
        for (let i = 0; i < arr.length; i++) {
            ans[i] = [arr[i]]
        }
      } else {
        for (let i = 0; i < arr.length; i++) {
          let parts = arr.slice(0)
          parts.splice(i, 1)[0]
          let row = this.getCombinations(parts, number - 1)
          for (let j = 0; j < row.length; j++) {
            ans.push([arr[i]].concat(row[j]))
          }
        }
      }
      return ans;
    },

    getGroupableIdols(units) {
      // ユニットに所属しているアイドル名を抽出
      const targetIdols = [];

      for(var unit of units) {
        for (var member of unit.members.split(',')) {
          if(!targetIdols.some(waitingMember => waitingMember === member)) {
            targetIdols.push(member);
          }
        }
      }

      return targetIdols;
    },

    

    // units: (配列) ユニット情報
    // memberLimit: ユニット毎のメンバーの数制限
    // unitValue: ユニットを組む数
    getMatchedUnits(units, memberLimit, unitValue) {
      // ユニットに所属しているアイドル名を抽出
      const targetIdols = this.getGroupableIdols(units);
      
      // ユニットを組む数 * ユニットを組む数 = 対象アイドルの数 の時だけ処理するようにする
      if(targetIdols.length === memberLimit * unitValue) {
        for(var unitCombination of this.getCombinations(units, unitValue)) {
          let tempExcludeIdols = [...targetIdols];
          let tempUnits = [];

          for(var unit of unitCombination) {
            tempExcludeIdols = this.getExcludeUnitMembers(tempExcludeIdols, unit.members.split(','))
            tempUnits.push(unit)

            console.log("Call: getMatchedUnits():for-in-for")
          }

          if(tempExcludeIdols.length === 0) {
            console.log(tempUnits);
            return tempUnits;
          }
        }
      }
      
      return [];
    },

    
    // 人数ピッタリにならなくて getMatchedUnits() に投げられない場合に使う
    getMatchedUnitsAll(units, memberLimit, unitValue) {
      // ユニットに所属しているアイドル名を抽出
      const targetIdols = this.getGroupableIdols(units);

      if(targetIdols.length > memberLimit * unitValue) {
        let tempUnits = [];
        let tempExcludeIdols = [...targetIdols];
        
        for(var unitCombination of this.getCombinations(units, unitValue)) {
          if(this.getGroupableIdols(unitCombination).length === memberLimit * unitValue) {
            if(!tempUnits.some(unitArray => unitArray === unitCombination)) {
              tempUnits.push(unitCombination);
            }
          }
        }

        if(tempUnits.length > 0) {
          return tempUnits;
        }
      }

      return [];
    },

    getMatchedIdols(unitArray) {
      return unitArray.map(unit => unit.members.split(','))
                      .reduce((a, b) => a.concat(b))
                      .filter((v, i, a) => a.indexOf(v) === i);
    },

    // 各属性 + ステータス セット のユニットデータを動的に出力
    getGenericUnitData(index, unitIdols) {
      // [handsCuVi, handsCuDa, handsCuVo, handsCoVi, handsCoDa, handsCoVo, handsPaVi, handsPaDa, handsPaVo]
      var unitName = "ステータスセット ";
      var memberList = [];
      var members = "";

      for(var idol of unitIdols) {
        memberList.push(idol.name);
      }

      if(memberList.length != 0) {
        members = memberList.join(',');
      }

      switch(index) {
        case 0:
          unitName += '(Cute/Visual)';
          break;
        case 1:
          unitName += '(Cute/Dance)';
          break;
        case 2:
          unitName += '(Cute/Vocal)';
          break;
        case 3:
          unitName += '(Cool/Visual)';
          break;
        case 4:
          unitName += '(Cool/Dance)';
          break;
        case 5:
          unitName += '(Cool/Vocal)';
          break;
        case 6:
          unitName += '(Passion/Visual)';
          break;
        case 7:
          unitName += '(Passion/Dance)';
          break;
        case 8:
          unitName += '(Passion/Vocal)';
          break;
      }

      return {
        "song": false,
        "unit_name": unitName,
        "yomi": "",
        "members": members
      }
    },

    // [6] 5スターユニット
    // return 36;
    // 5人ユニット + (2人ユニット or 2個セット) * 2
    checkFiveStarUnits(idols) {
      // 結成済みユニットリストの初期化
      this.stageUnits = [];
      
      // 2人ユニット
      const units_duo = this.getDuoUnits();
      // 5人ユニット
      const units_fivestars = this.getFiveStarUnits();

      var unit_members = [];
      // デュオユニット結成フラグ
      var flag_duo_51 = false;
      var flag_duo_52 = false;

      for(var fivestar_unit of units_fivestars) {
        console.log("Fivestar Search")
        unit_members = fivestar_unit.members.split(',');

        var isFiveStarUnit = unit_members.every(unit_member => idols.includes(unit_member));
        
        if(isFiveStarUnit) {
          if(!this.stageUnits.some(unitObj => unitObj === fivestar_unit)) {
            this.stageUnits.push(fivestar_unit);
          }

          // ユニット結成分を除外
          idols = this.getExcludeUnitMembers(idols, unit_members);
          

          // ユニット総当り検索用パターン作成
          var temp_units = this.getCombinations(idols, 4);
          var temp_result = [];

          for(var temp_unit of temp_units) {
            flag_duo_51 = false;
            flag_duo_52 = false;

            // 2:2 で分割
            var temp_duo = [temp_unit.slice(0,2), temp_unit.slice(2)];
            
            // 組み合わせ 1
            for(var duo_unit of units_duo) {
              unit_members = duo_unit.members.split(',');
              if (unit_members.every(unit_member => temp_duo[0].includes(unit_member))) {
                flag_duo_51 = true;

                if(!this.stageUnits.some(unitObj => unitObj === duo_unit)) {
                  this.stageUnits.push(duo_unit);
                }
                
                break;
              }
            }

            // 組み合わせ 2
            for(var duo_unit of units_duo) {
              unit_members = duo_unit.members.split(',');
              if (unit_members.every(unit_member => temp_duo[1].includes(unit_member))) {
                flag_duo_52 = true;

                if(!this.stageUnits.some(unitObj => unitObj === duo_unit)) {
                  this.stageUnits.push(duo_unit);
                }

                break;
              }
            }

            if(flag_duo_51 && flag_duo_52) {
              break;
            }

            temp_result.push([flag_duo_51, flag_duo_52])
          }

          // 5人ユニ + 2人ユニ + 2人ユニ
          if(flag_duo_51 && flag_duo_52) {
            return 36;
          }

          // 残った牌データを取得
          var rest_idols = this.getIdolsByNames(idols);
          
          // 5人ユニ + 2人ユニ + 2人セット
          if(temp_result.some(values => values.includes(true))) {
            if(this.checkStatusUnit(rest_idols, 2)) {
              return 36;
            } 
          }

          // 5人ユニ + 2人セット + 2人セット
          if(idols.length === 4) {
            if(this.checkStatusUnit(rest_idols, 4)) {
              return 36;
            } else if(this.checkStatusUnit(rest_idols, 2)) {
              return 36;
            }
          }
        }
      }

      return 0;
    },

    // [7] カルテットユニット
    // return 36;
    // 4人ユニット + (3人ユニット or 3個セット) + (2人ユニット or 2個セット)
    checkQuartetUnits(idols) {
      // 結成済みユニットリストの初期化
      this.stageUnits = [];

      // 4人ユニット
      const units_quartet = this.getQuartetUnits();

      // 3人ユニット
      const units_trio = this.getTrioUnits();

      // 2人ユニット
      const units_duo = this.getDuoUnits();

      var unit_members = [];
      // カルテットユニット結成フラグ
      var flag_quartet_71 = false;
      var flag_quartet_72 = false;

      for(var quartet_unit of units_quartet) {
        console.log("Quartet Search")
        unit_members = quartet_unit.members.split(',');
        var isQuartetUnit = unit_members.every(unit_member => idols.includes(unit_member));

        if(isQuartetUnit) {
          // ユニット結成分を除外
          idols = this.getExcludeUnitMembers(idols, unit_members);

          if(!this.stageUnits.some(unitObj => unitObj === quartet_unit)) {
            this.stageUnits.push(quartet_unit);
          }

          // 3:2 で分割、ユニット総当り検索
          var temp_units = this.getCombinations(idols, 5);
          var temp_result = [];

          for(var temp_unit of temp_units) {
            flag_quartet_71 = false;
            flag_quartet_72 = false;

            // 3:2 で分割
            var temp_idols = [temp_unit.slice(0,3), temp_unit.slice(3)];

            // 組み合わせ 1
            for(var trio_unit of units_trio) {
              unit_members = trio_unit.members.split(',');
              if (unit_members.every(unit_member => temp_idols[0].includes(unit_member))) {
                flag_quartet_71 = true;

                if(!this.stageUnits.some(unitObj => unitObj === trio_unit)) {
                  this.stageUnits.push(trio_unit);
                }
                
                break;
              }
            }

            // 組み合わせ 2
            for(var duo_unit of units_duo) {
              unit_members = duo_unit.members.split(',');
              if (unit_members.every(unit_member => temp_idols[1].includes(unit_member))) {
                flag_quartet_72 = true;

                if(!this.stageUnits.some(unitObj => unitObj === duo_unit)) {
                  this.stageUnits.push(duo_unit);
                }

                break;
              }
            }

            if(flag_quartet_71 && flag_quartet_72) {
              break;
            }

            temp_result.push([flag_quartet_71, flag_quartet_72])
          }

          // 4人ユニ + 3人ユニ + 2人ユニ
          if(flag_quartet_71 && flag_quartet_72) {
            return 36;
          }

          // 残った牌データを取得
          var rest_idols = this.getIdolsByNames(idols);
          console.log(rest_idols);

          // 4人ユニ + (3人セット + 2人セット)
          if(this.checkStatusUnit(rest_idols, 5)) {
            return 36;
          }

          // 4人ユニ + 3人ユニ + 2人セット
          if(temp_result.some(values => values[0] === true)) {
            if(this.checkStatusUnit(rest_idols, 2)) {
              return 36;
            }
          }

          // 4人ユニ + 3人セット + 2人ユニ
          if(temp_result.some(values => values[1] === true)) {
            // 現在結成可能な2人ユニットのリストを絞り込む
            var tempDuoUnits = this.stageUnits.filter(unitObj => unitObj.members.split(',').length == 2);
            // ユニット一覧を初期化 (2人ユニットを除外)
            this.stageUnits = this.stageUnits.filter(unitObj => !tempDuoUnits.includes(unitObj));

            for(var unit of tempDuoUnits) {
              var i = this.getExcludeUnitMembers(idols, unit.members.split(','));

              var excludeUnit = this.getIdolsByNames(i);

              if(this.checkStatusUnit(excludeUnit, 3)) {
                this.stageUnits.push(unit);
                return 36;
              }
            }
          }

          // 4人ユニ + 3人セット + 2人セット
          if(this.checkStatusUnit(rest_idols, 2) && this.checkStatusUnit(rest_idols, 3)) {
            return 36;
          }
        }
      }

      return 0;
    },

    // [8] トリコロールユニット        / [9] ノーマルライブユニット
    // return 24;                      / return 12;
    // 各タイプ それぞれで 3人ユニット / (3人ユニット) + (3人ユニット or 3個セット) * 2
    checkTrioUnit(idols) {
      console.log("Check Trio Unit")
      // 結成済みユニットリストの初期化
      this.stageUnits = [];
      // 3人ユニット
      const trioUnits = this.getTrioUnits()

      // トリコロールユニットチェック用
      const cuteUnits = this.getCuteTrioUnits();
      const coolUnits = this.getCoolTrioUnits();
      const passionUnits = this.getPassionTrioUnits();

      // 結成可能な3人ユニット
      const groupableTrioUnits = [...trioUnits].filter((unitObj) => unitObj.members.split(',').every((unitMember) => idols.includes(unitMember)));
      let tempTrioUnits = [];
      
      // 結成可能な3人ユニットがあった場合
      if(groupableTrioUnits.length > 0) {

        let matchedUnits = []
        matchedUnits = this.getMatchedUnits(groupableTrioUnits, 3, 3);

        // 3人ユニ + 3人ユニ + 3人ユニ
        if(matchedUnits.length === 3) {
          this.stageUnits = this.stageUnits.concat(matchedUnits);
          var countCute = this.stageUnits.filter((unit) => cuteUnits.includes(unit)).length;
          var countCool = this.stageUnits.filter((unit) => coolUnits.includes(unit)).length;
          var countPassion = this.stageUnits.filter((unit) => passionUnits.includes(unit)).length;

          // トリコロール判定
          if([countCute, countCool, countPassion].every(count => count === 1)) {
            return 24;
          } else {
            // トリコロール以外の 3人ユニ * 3 パターン
            return 12;
          }
        }

        matchedUnits = this.getMatchedUnits(groupableTrioUnits, 3, 2);

        if(matchedUnits.length == 2) {
          console.log(matchedUnits.length);

          const matchedIdols = this.getGroupableIdols(matchedUnits);
          const restIdols = this.getIdolsByNames(this.getExcludeUnitMembers([...idols], matchedIdols));
          // 残りの3人でステータスセットが組めるか？
          if(this.checkStatusUnit(restIdols, 3)) {
            this.stageUnits = this.stageUnits.concat(matchedUnits);
            return 12;
          }

        } else {
          const matchedUnitsAll = this.getMatchedUnitsAll(groupableTrioUnits, 3, 2);

          // 総当りチェック
          for(var matchedUnitPattern of matchedUnitsAll) {
            const matchedIdols = this.getGroupableIdols(matchedUnitPattern);
            const restIdols = this.getIdolsByNames(this.getExcludeUnitMembers([...idols], matchedIdols));

            // 残りの3人でステータスセットが組めるか？
            if(this.checkStatusUnit(restIdols, 3)) {
              this.stageUnits = this.stageUnits.concat(matchedUnitPattern);
              return 12;
            }
          }
        }

        if(groupableTrioUnits.length === 1) {
          const matchedIdols = this.getGroupableIdols(groupableTrioUnits);
          const restIdols = this.getIdolsByNames(this.getExcludeUnitMembers([...idols], matchedIdols));
          console.log(restIdols)
          // 残りの6人でステータスセットが組めるか？
          if(this.checkStatusUnit(restIdols, 6) || this.checkStatusUnit(restIdols, 3)) {
            this.stageUnits = this.stageUnits.concat(groupableTrioUnits);
            return 12;
          }
        }
      }

      return 0;
    },

    // [10] スタートダッシュライブユニット
    // return 6;
    // (3人ユニット or 3個セット) + (2人ユニット or 2個セット) * 3
    // ※2つ以上のユニットが必要
    checkStartDashUnit(idols) {
      // 結成済みユニットリストの初期化
      this.stageUnits = [];
      // 3人ユニット
      const trioUnits = this.getTrioUnits()
      // 2人ユニット
      const duoUnits = this.getDuoUnits();

      console.log("StartDash Search")

      // 結成可能な3人ユニット
      const groupableTrioUnits = [...trioUnits].filter((unitObj) => unitObj.members.split(',').every((unitMember) => idols.includes(unitMember)));
      let tempTrioUnits = [];

      // 結成可能な3人ユニットがあった場合
      if(groupableTrioUnits.length > 0) {
        // 処理をまとめるために配列にする
        if(groupableTrioUnits.length === 1) {
          tempTrioUnits = [groupableTrioUnits.shift()]
        } else if(groupableTrioUnits.length > 1) {
          tempTrioUnits = [...groupableTrioUnits];
        }

        for(var trioUnit of tempTrioUnits) {
          // ユニット情報の登録
          this.stageUnits.push(trioUnit);
          
          const trioUnitMembers = trioUnit.members.split(',');
          idols = this.getExcludeUnitMembers(idols, trioUnitMembers);
          // console.log(idols)

          const groupableDuoUnits = [...duoUnits].filter((unitObj) => unitObj.members.split(',').every((unitMember) => idols.includes(unitMember)));

          let tempDuoUnits = [];

          // 結成可能な2人ユニットがいた場合
          if(groupableDuoUnits.length > 0) {

            // 処理をまとめるために配列にする
            if(groupableDuoUnits.length === 1) {
              tempDuoUnits = [groupableDuoUnits.shift()]
            } else if(groupableDuoUnits.length > 1) {
              tempDuoUnits = [...groupableDuoUnits];
            }

            console.log(tempDuoUnits);

            // 3件以上
            if(tempDuoUnits.length > 2) {
              let flagFailure = false;
              let matchedUnits = [];
              matchedUnits = this.getMatchedUnits(tempDuoUnits, 2, 2);

              // 3人ユニ + 2人ユニ + 2人ユニ + 2人セット
              if(matchedUnits.length > 0 && matchedUnits.length === 2) {
                this.stageUnits = this.stageUnits.concat(matchedUnits);

                const matchedIdols = this.getMatchedIdols(matchedUnits);

                const restIdols = this.getIdolsByNames(this.getExcludeUnitMembers([...idols], matchedIdols));
                console.log(restIdols)

                // 残りの2人でステータスセットが組めるか？
                if(this.checkStatusUnit(restIdols, 2)) {
                  return 6;
                }
              }
              
              flagFailure = true;
              matchedUnits = this.getMatchedUnits(tempDuoUnits, 2, 3);

              // 3人ユニ + 2人ユニ + 2人セット + 2人セット で重複があったパターン
              if(flagFailure && matchedUnits.length === 0) {
                for(var duoUnit of tempDuoUnits) {
                  console.log(duoUnit);
                  // 3人ユニ + 2人ユニ + 2人セット + 2人セット
                  const matchedIdols = this.getMatchedIdols([duoUnit]);
                  const restIdols = this.getIdolsByNames(this.getExcludeUnitMembers([...idols], matchedIdols));
                  
                  if(restIdols.length === 4) {
                    if(this.checkStatusUnit(restIdols, 4)) {
                      this.stageUnits = this.stageUnits.concat(duoUnit);
                      return 6;
                    } else if(this.checkStatusUnit(restIdols, 2)) {
                      this.stageUnits = this.stageUnits.concat(duoUnit);
                      return 6;
                    }
                  }
                }
              }

              // 3人ユニ + 2人ユニ + 2人ユニ + 2人ユニ
              if(flagFailure && matchedUnits.length > 0 && matchedUnits.length === 3) {
                this.stageUnits = this.stageUnits.concat(matchedUnits);
                return 6;
              }

            } else if(tempDuoUnits.length === 2) {
              // 対象ユニット数が2つだった時
              let matchedUnits = []
              matchedUnits = this.getMatchedUnits(tempDuoUnits, 2, 2);
              console.log(matchedUnits);

              // 重複無しで 2人ユニット を 2つ作れるか？
              if(matchedUnits.length > 0 && matchedUnits.length === 2) {
                this.stageUnits = this.stageUnits.concat(matchedUnits);
                const matchedIdols = this.getMatchedIdols(matchedUnits);
                const restIdols = this.getIdolsByNames(this.getExcludeUnitMembers([...idols], matchedIdols));

                // 残りの2人でステータスセットが組めるか？
                if(this.checkStatusUnit(restIdols, 2)) {
                  return 6;
                } else {
                  // 結成済みユニットリストの初期化
                  this.stageUnits = [];
                  return 0;
                }
              } else {
                for(var duoUnit of tempDuoUnits) {
                  console.log(duoUnit);
                  // 3人ユニ + 2人ユニ + 2人セット + 2人セット
                  const matchedIdols = this.getMatchedIdols([duoUnit]);
                  const restIdols = this.getIdolsByNames(this.getExcludeUnitMembers([...idols], matchedIdols));
                  
                  if(restIdols.length === 4) {
                    if(this.checkStatusUnit(restIdols, 4)) {
                      this.stageUnits = this.stageUnits.concat(duoUnit);
                      return 6;
                    } else if(this.checkStatusUnit(restIdols, 2)) {
                      this.stageUnits = this.stageUnits.concat(duoUnit);
                      return 6;
                    }
                  }
                }
              }
            } else if(tempDuoUnits.length === 1) {
              // 対象ユニット数が1つだった時
              const duoUnit = tempDuoUnits.shift()
              // 3人ユニ + 2人ユニ + 2人セット + 2人セット
              this.stageUnits = this.stageUnits.concat(duoUnit);
              const matchedIdols = this.getMatchedIdols(this.stageUnits);
              const restIdols = this.getIdolsByNames(this.getExcludeUnitMembers([...idols], matchedIdols));
              
              if(restIdols.length === 4) {
                if(this.checkStatusUnit(restIdols, 4)) {
                  return 6;
                } else if(this.checkStatusUnit(restIdols, 2)) {
                  return 6;
                }
              }
            }
          }
        }
      } else {
        // 結成可能な3人ユニットがなかった場合
        const groupableDuoUnits = [...duoUnits].filter((unitObj) => unitObj.members.split(',').every((unitMember) => idols.includes(unitMember)));
        
        // 2人ユニットが2つ以上ヒットしなかったら無視
        if(groupableDuoUnits.length > 1) {
          let matchedUnits = [];

          console.log(groupableDuoUnits);
          console.log(groupableDuoUnits.length);
          
          matchedUnits = this.getMatchedUnits(groupableDuoUnits, 2, 3);

          // 3人セット + 2人ユニ + 2人ユニ + 2人ユニ
          if(matchedUnits.length > 0 && matchedUnits.length === 3) {
            const matchedIdols = this.getGroupableIdols(matchedUnits);
            const restIdols = this.getIdolsByNames(this.getExcludeUnitMembers([...idols], matchedIdols));
            console.log(restIdols)

            // 残りの3人でステータスセットが組めるか？
            if(this.checkStatusUnit(restIdols, 3)) {
              this.stageUnits = this.stageUnits.concat(matchedUnits);
              return 6;
            }
          } else {
            // 3人セット + 2人セット + 2人ユニ + 2人ユニ
            matchedUnits = this.getMatchedUnitsAll(groupableDuoUnits, 2, 2);

            // 総当りチェック
            for(var matchedUnitPattern of matchedUnits) {
              const matchedIdols = this.getGroupableIdols(matchedUnitPattern);
              const restIdols = this.getIdolsByNames(this.getExcludeUnitMembers([...idols], matchedIdols));

              // 残りの5人でステータスセットが組めるか？
              if(this.checkStatusUnit(restIdols, 5) || this.checkStatusUnit(restIdols, 2) && this.checkStatusUnit(restIdols, 3)) {
                this.stageUnits = this.stageUnits.concat(matchedUnitPattern);
                return 6;
              }
            }
          }
        }
      }

      return 0;
    },

    getScore(handIdols) {
      console.log("Call: getScore()")

      this.stageUnits = [];
      var idols = []
      var almighty_idols = [];
      var fans = 0;

      var useLocalRule = this.useLocalRule;

      var flag_almighty_cute = false;
      var flag_almighty_cool = false;
      var flag_almighty_passion = false;

      var sp_score_a = this.spScoreA;
      var sp_score_b = this.spScoreB;
      var sp_score_c = this.spScoreC;

      // 歴代シンデレラガールズ
      const unit_cg = this.getCinderellaGirls().shift();
      // WONDERFUL M@GIC!! / CINDERELLA PROJECT
      const units_magic = this.getMagicUnits();

      //// ローカル役
      // 9人以上 12人未満 大型ユニット
      const units_concealed = this.getConcealedUnits();

      if(handIdols.length < 9) { 
        return 0;
      } else {
        if(handIdols.length == 9) {
          idols = this.getIdolNames(handIdols);

          // オールマイティ抽出
          for(var idol of handIdols) {
            // オールマイティ以外スキップ
            if(idol.name != "オールマイティ") {
              continue;
            }
            if(idol.type == "cute") {
              flag_almighty_cute = true;
            }

            if(idol.type == "cool") {
              flag_almighty_cool = true;
            }

            if(idol.type == "passion") {
              flag_almighty_passion = true;
            }

            almighty_idols.push(idol);
          }

          // [4] シンデレラガールズ ユニット
          var isAllCG = idols.every(idol => unit_cg.members.includes(idol));

          if(isAllCG) {
            return 60;
          }

          // [5] WONDERFUL M@GIC!! ユニット / CINDERELLA PROJECT
          for(var magic_unit of units_magic) {
            console.log("WONDERFUL M@GIC Search")
            var isMagicUnit = idols.every(idol => magic_unit.members.includes(idol));
            
            if(isMagicUnit) {
              return 48;
            }
          }
          
          // ローカル役チェック
          if(useLocalRule) {
            // (ローカル役)         
            // [60万人] SPライブセットA
            // 例: Project:Krone, ETERNITY MEMORIES, EVERLASTING 
            for(var concealed_unit of units_concealed) {
              var isConcealedUnit = idols.every(idol => concealed_unit.members.includes(idol));

              if(isConcealedUnit) {
                return sp_score_a;
              }
            }

            // (ローカル役)
            // [42万人] 7 + 2 | SPライブセットB
            // return sp_score_b;


            // (ローカル役)
            // [42万人] 6 + 3 | SPライブセットC
            // return sp_score_c;
          }
          
          // [6] 5スターユニット
          // return 36;
          // 5人ユニット + (2人ユニット or 2個セット) * 2
          fans = this.checkFiveStarUnits(idols);
          if (fans > 0) {
            return fans;
          } else {
            // 初期化
            this.stageUnits = [];
          }

          // [7] カルテットユニット
          // return 36;
          // 4人ユニット + (3人ユニット or 3個セット) + (2人ユニット or 2個セット)
          fans = this.checkQuartetUnits(idols);
          if (fans > 0) {
            return fans;
          } else {
            // 初期化
            this.stageUnits = [];
          }

          // [8] トリコロールユニット
          // return 24;
          // 各タイプ それぞれで 3人ユニット

          // [9] ノーマルライブユニット
          // return 12;
          // (3人ユニット) + (3人ユニット or 3個セット) * 2
          fans = this.checkTrioUnit(idols);
          if (fans > 0) {
            return fans;
          } else {
            // 初期化
            this.stageUnits = [];
          }

          // [10] スタートダッシュライブユニット
          // return 6;
          // (3人ユニット or 3個セット) + (2人ユニット or 2個セット) * 3
          // ※2つ以上のユニットが必要
          fans = this.checkStartDashUnit(idols);
          if (fans > 0) {
            return fans;
          } else {
            // 初期化
            this.stageUnits = [];
          }

          // 手牌の各属性・各ステータスのアイドルを抽出
          // Cute - Vi Da Vo
          var handsCuVi = this.getFilteredHands(handIdols, "cute", "Vi");
          var handsCuDa = this.getFilteredHands(handIdols, "cute", "Da");
          var handsCuVo = this.getFilteredHands(handIdols, "cute", "Vo");

          // Cool - Vi Da Vo
          var handsCoVi = this.getFilteredHands(handIdols, "cool", "Vi");
          var handsCoDa = this.getFilteredHands(handIdols, "cool", "Da");
          var handsCoVo = this.getFilteredHands(handIdols, "cool", "Vo");
          
          // Passion - Vi Da Vo
          var handsPaVi = this.getFilteredHands(handIdols, "passion", "Vi");
          var handsPaDa = this.getFilteredHands(handIdols, "passion", "Da");
          var handsPaVo = this.getFilteredHands(handIdols, "passion", "Vo");

          // 各属性の3枚セットのセット数を抽出
          // Cute
          var setCuVi, setCuDa, setCuVo = 0;
          setCuVi = handsCuVi.length % 3 == 0 ? handsCuVi.length / 3 : 0
          setCuDa = handsCuDa.length % 3 == 0 ? handsCuDa.length / 3 : 0
          setCuVo = handsCuVo.length % 3 == 0 ? handsCuVo.length / 3 : 0

          // Cool
          var setCoVi, setCoDa, setCoVo = 0;
          setCoVi = handsCoVi.length % 3 == 0 ? handsCoVi.length / 3 : 0
          setCoDa = handsCoDa.length % 3 == 0 ? handsCoDa.length / 3 : 0
          setCoVo = handsCoVo.length % 3 == 0 ? handsCoVo.length / 3 : 0

          // Passion
          var setPaVi, setPaDa, setPaVo = 0;
          setPaVi = handsPaVi.length % 3 == 0 ? handsPaVi.length / 3 : 0
          setPaDa = handsPaDa.length % 3 == 0 ? handsPaDa.length / 3 : 0
          setPaVo = handsPaVo.length % 3 == 0 ? handsPaVo.length / 3 : 0

          // [1] Cute / Cool / Passion アンサンブルセット
          var setCounts = [setCuVi, setCuDa, setCuVo, setCoVi, setCoDa, setCoVo, setPaVi, setPaDa, setPaVo];

          if(setCounts.some(count => count === 3)) {
            return 42;
          }

          // [2] Cute / Cool / Passion シンフォニーセット
          var cuteSymphony = setCuVi + setCuDa + setCuVo;
          var coolSymphony = setCoVi + setCoDa + setCoVo;
          var passionSymphony = setPaVi + setPaDa + setPaVo;

          if(cuteSymphony == 3 || coolSymphony == 3 || passionSymphony == 3) {
            return 24;
          }

          // [3] リハーサルライブセット
          var rehearsal_set = cuteSymphony + coolSymphony + passionSymphony;

          if(rehearsal_set == 3) {
            return 3;
          }
        }

        return 0;
      }
    }
  },
  watch: {
    // 点数計算
    handIdols:function(handIdols, produceIdol) {
      if(handIdols.length < 9) { 
        return 0;
      } else {
        var idols = [];
        const score = this.getScore(handIdols);

        for(var idol of handIdols) {
          idols.push(idol.name);
        }

        if(idols.some(idol => idol === produceIdol.name)) {
          this.score = score * 2;
        } else {
          this.score = score;
        }
      }
    }
  }
}
</script>

<template>
  <div class="module-wrapper">
    {{ produceIdol }}
    <br>
    <!-- {{ getCuteTrioUnits() }} -->
    <br>
    <!-- {{ getScoreWithProduceIdol(handIdols, produceIdol) }} -->
    
    {{ score }}
    <br>
    <!-- {{ getCinderellaGirls }} -->
    <br>
    {{ stageUnits }}
  </div>
</template>