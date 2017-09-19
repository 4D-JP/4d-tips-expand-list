# 4d-tips-expand-list
階層リストを再帰的に展開あるいは収縮する例題

<img width="345" alt="2017-09-19 12 20 55" src="https://user-images.githubusercontent.com/10509075/30574488-2b1e2be8-9d35-11e7-8f15-04b191dcfd6e.png">

```
C_LONGINT($1;$l)
C_BOOLEAN($2;$expand)
C_POINTER($3;$4;$5)

$l:=$1
$expand:=$2

C_LONGINT($itemRef;$sublistRef)
C_TEXT($itemText)
C_BOOLEAN($expanded)

If ($expand)
	
	ARRAY LONGINT($selects;0)
	$select:=Selected list items($l;$selects;*)
	
	For ($i;1;Count list items($l;*))
		SELECT LIST ITEMS BY POSITION($l;$i)
		GET LIST ITEM($l;*;$itemRef;$itemText;$sublistRef;$expanded)
		SET LIST ITEM($l;*;$itemText;$itemRef;$sublistRef;$expand)
	End for 
	
	SELECT LIST ITEMS BY REFERENCE($l;$select;$selects)
	
Else 
	
	If (Count parameters=5)
		$lists:=$3
		$sublists:=$4
		$labels:=$5
	Else 
		ARRAY LONGINT($_lists;0)
		ARRAY LONGINT($_sublists;0)
		ARRAY TEXT($_labels;0)
		$lists:=->$_lists
		$sublists:=->$_sublists
		$labels:=->$_labels
	End if 
	
	For ($i;1;Count list items($l))
		GET LIST ITEM($l;$i;$itemRef;$itemText;$sublistRef;$expanded)
		If (Is a list($sublistRef))
			EXECUTE METHOD(Current method name;*;$sublistRef;$expand;$lists;$sublists;$labels)
			If (Not($expand)) & ($expanded)
				APPEND TO ARRAY($lists->;$itemRef)
				APPEND TO ARRAY($sublists->;$sublistRef)
				APPEND TO ARRAY($labels->;$itemText)
			End if 
		End if 
	End for 
	
	If (Count parameters#5)
		For ($i;1;Size of array($_lists))
			$itemRef:=$_lists{$i}
			$sublistRef:=$_sublists{$i}
			$itemText:=$_labels{$i}
			SET LIST ITEM($l;$itemRef;$itemText;$itemRef;$sublistRef;$expand)
		End for 
	End if 
	
End if 
```
