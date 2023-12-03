# rocky
automaton
// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © dembaha & @ rocksody

//@version=5
indicator('Automaton v1', overlay=true,  max_bars_back=500, max_labels_count=500)

////////////////////////////// ~~~~~~~~~~~~~~~~~~ \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\
//                                   INPUTS                                   \\
////////////////////////////// ~~~~~~~~~~~~~~~~~~ \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\

tf_multip1  = input.int( 1, minval= 1, maxval= 1440, 
             title = "TimeFrame", group = "TimeFrame", inline="1")
tf_period1  = input.string(title="", defval= "Hour", 
             options=["Chart TF", "Minute", "Hour", "Day", "Week", "Month"], 
             group = "TimeFrame", inline="1")


// Styling & Coloring
// Table Position
in_table_pos = input.string(title="Table Location  ", defval= "Top Right", 
     options =["Top Right",  "Middle Right",  "Bottom Right", 
               "Top Center", "Middle Center", "Bottom Center", 
               "Top Left",   "Middle Left",   "Bottom Left"], 
     group= "Table Styling", inline= "1")

// Table Size
in_table_size = input.string(title="Table Size    ", defval="Small", 
     options=["Auto",  "Huge",  "Large", "Normal", "Small", "Tiny"], 
     group= "Table Styling" , inline= "2")

// Color
column_title_bgcol  = input.color(color.gray  , title="Column Title Color", 
                      group = "Table Styling" , inline="3")
cell_bgcol          = input.color(#aaaaaa     , title="Cell Color    ", 
                      group = "Table Styling" , inline="4")
cell_txtcol         = input.color(color.white , title="Text Color       ", 
                      group = "Table Styling" , inline="5")


// Indicators
//SFP
lookback = 489

is_opposite = input.bool(false, "Candle should be opposite direction", group="Indicators - SFP")
plen = input.int(21, "Pivot Lenght", 1, 99, group="Indicators - SFP")
textcolor = input.color(color.orange, "Text Color")
position = input.string(position.top_right, "Table Position", [position.top_right, position.top_left, position.bottom_right, position.bottom_left])
textcolortbl = input.color(color.orange, "Table Text Color")


//STYLES

upcol = input(defval=color.green, title='HHLL Up Color', group="Styles")
dncol = input(defval=color.red, title='HHLL Down Color', group="Styles")
uplabeltextcol = input(defval=color.white, title='Label Text Up Color', group="Styles")
dnlabeltextcol = input(defval=color.white, title='Label Text Down Color', group="Styles")
uplabelcol = input(defval=color.green, title='Label BG Up Color', group="Styles")
dnlabelcol = input(defval=color.red, title='Label BG Down Color', group="Styles")
zzstyle = input.string(defval='Dashed', title='Zig Zag Line Style', options=['Dashed', 'Dotted'], group="Styles")
zzwidth = input.int(defval=1, title='Zig zag Line Width', minval=1, maxval=4, group="Styles")

//PATTERNS

length = input.int(defval=10, title='Pivot strength', minval=2, maxval=20, group="Patterns")
showzz1 = input(false, title='Show Zigzag', group="Patterns")
showbosline = input(false, title='Show BOS Lines', group="Patterns")
showhhll1 = input(true, title='Show HH/LL', group="Patterns")
showPatternMatch = input(true, title='Show Pattern Matches', group="Patterns")

//BULL PATTERN
bullCond1 = input.string(defval=' HL,LH,LL,HH,HL', title='Bull 1', inline="11", group="Bull Pattern")
bullTxt1 = input.string(defval='phl0', title='', inline="11", group="Bull Pattern")
bullBool1 = input.bool(true, "", inline="11", group = "Bull Pattern")
bullCond2 = input.string(defval='HH,HL,HH,HL', title='Bull 2', inline="22", group="Bull Pattern")
bullTxt2 = input.string(defval='phl1', title='', inline="22", group="Bull Pattern")
bullBool2 = input.bool(true, "", inline="22", group = "Bull Pattern")
bullCond3 = input.string(defval='LH,LL,LH,HL,HH,HL', title='Bull 3', inline="33", group="Bull Pattern")
bullTxt3 = input.string(defval='phl2', title='', inline="33", group="Bull Pattern")
bullBool3 = input.bool(true, "", inline="33", group = "Bull Pattern")
bullCond4 = input.string(defval='LH,LL,HH,HL,HH,HL', title='Bull 4', inline="44", group="Bull Pattern")
bullTxt4 = input.string(defval='phl3', title='', inline="44", group="Bull Pattern")
bullBool4 = input.bool(true, "", inline="44", group = "Bull Pattern")
bullCond5 = input.string(defval='LH,LL,HH,LL,HH,HL', title='Bull 5', inline="55", group="Bull Pattern")
bullTxt5 = input.string(defval='phl4', title='', inline="55", group="Bull Pattern")
bullBool5 = input.bool(true, "", inline="55", group = "Bull Pattern")
bullCond6 = input.string(defval='HL,HH,HL,HH,LL', title='Bull 6', inline="66", group="Bull Pattern")
bullTxt6 = input.string(defval='fbos', title='', inline="66", group="Bull Pattern")
bullBool6 = input.bool(true, "", inline="66", group = "Bull Pattern")
bullCond7 = input.string(defval='LL,LH,LL,HH,HL', title='Bull 7', inline="77", group="Bull Pattern")
bullTxt7 = input.string(defval='boshl', title='', inline="77", group="Bull Pattern")
bullBool7 = input.bool(true, "", inline="77", group = "Bull Pattern")

//BEAR PATTERN
bearCond1 = input.string(defval='LH,HL,HH,LL,LH', title='Bear 1', inline="11", group="Bear Pattern")
bearTxt1 = input.string(defval='plh0', title='', inline="11", group="Bear Pattern")
bearBool1 = input.bool(true, "", inline="11", group = "Bear Pattern")
bearCond2 = input.string(defval='LL,LH,LL,LH', title='Bear 2', inline="22", group="Bear Pattern")
bearTxt2 = input.string(defval='plh1', title='', inline="22", group="Bear Pattern")
bearBool2 = input.bool(true, "", inline="22", group = "Bear Pattern")
bearCond3 = input.string(defval='HL,HH,HL,LH,LL,LH', title='Bear 3', inline="33", group="Bear Pattern")
bearTxt3 = input.string(defval='plh2', title='', inline="33", group="Bear Pattern")
bearBool3 = input.bool(true, "", inline="33", group = "Bear Pattern")
bearCond4 = input.string(defval='HL,HH,LL,LH,LL,LH', title='Bear 4', inline="44", group="Bear Pattern")
bearTxt4 = input.string(defval='plh3', title='', inline="44", group="Bear Pattern")
bearBool4 = input.bool(true, "", inline="44", group = "Bear Pattern")
bearCond5 = input.string(defval='HL,HH,LL,HH,LL,LH', title='Bear 5', inline="55", group="Bear Pattern")
bearTxt5 = input.string(defval='plh4', title='', inline="55", group="Bear Pattern")
bearBool5 = input.bool(true, "", inline="55", group = "Bear Pattern")
bearCond6 = input.string(defval='LH,LL,LH,LL,HH', title='Bear 6', inline="66", group="Bear Pattern")
bearTxt6 = input.string(defval='fbos', title='', inline="66", group="Bear Pattern")
bearBool6 = input.bool(true, "", inline="66", group = "Bear Pattern")
bearCond7 = input.string(defval='HH,HL,HH,LL,LH', title='Bear 7', inline="77", group="Bear Pattern")
bearTxt7 = input.string(defval='boslh', title='', inline="77", group="Bear Pattern")
bearBool7 = input.bool(true, "", inline="77", group = "Bear Pattern")

var max_array_size = 10
////////////////////////////// ~~~~~~~~~~~~~~~~~~ \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\
//                                   SYMBOLS                                  \\
////////////////////////////// ~~~~~~~~~~~~~~~~~~ \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\
// Flag
u01 = input.bool(true,  title = "", group = 'Symbols', inline = 's01')
u02 = input.bool(true,  title = "", group = 'Symbols', inline = 's02')
u03 = input.bool(true,  title = "", group = 'Symbols', inline = 's03')
u04 = input.bool(true,  title = "", group = 'Symbols', inline = 's04')
u05 = input.bool(true,  title = "", group = 'Symbols', inline = 's05')
u06 = input.bool(true,  title = "", group = 'Symbols', inline = 's06')
u07 = input.bool(true,  title = "", group = 'Symbols', inline = 's07')
u08 = input.bool(true,  title = "", group = 'Symbols', inline = 's08')
u09 = input.bool(true,  title = "", group = 'Symbols', inline = 's09')
u10 = input.bool(true,  title = "", group = 'Symbols', inline = 's10')


// Symbols
s01 = input.symbol('BINANCE:BTCUSDTPERP',  group = 'Symbols', inline = 's01')
s02 = input.symbol('BINANCE:XRPUSDTPERP',  group = 'Symbols', inline = 's02')
s03 = input.symbol('BINANCE:DOGEUSDTPERP', group = 'Symbols', inline = 's03')
s04 = input.symbol('BINANCE:BNBUSDTPERP',  group = 'Symbols', inline = 's04')
s05 = input.symbol('BINANCE:ETHUSDTPERP',  group = 'Symbols', inline = 's05')
s06 = input.symbol('BINANCE:MATICUSDTPERP',  group = 'Symbols', inline = 's06')
s07 = input.symbol('BINANCE:SOLUSDTPERP',  group = 'Symbols', inline = 's07')
s08 = input.symbol('BINANCE:ARBUSDTPERP',  group = 'Symbols', inline = 's08')
s09 = input.symbol('BINANCE:DOTUSDTPERP',  group = 'Symbols', inline = 's09')
s10 = input.symbol('BINANCE:AVAXUSDTPERP',  group = 'Symbols', inline = 's10')

////////////////////////////// ~~~~~~~~~~~~~~~~~~ \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\
//                                CALCULATIONS                                \\
////////////////////////////// ~~~~~~~~~~~~~~~~~~ \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\

// PHL for functions

// Timeframe for security functions
TF(tf_period, tf_multip) => 
    switch tf_period 
        "Minute" => str.tostring(tf_multip)
        "Hour"   => str.tostring(tf_multip*60)
        "Day"    => str.tostring(tf_multip) + "D"
        "Week"   => str.tostring(tf_multip) + "W"
        "Month"  => str.tostring(tf_multip) + "M"
        => timeframe.period


// Timeframe shortcut form
tf_form( tf_period, tf_multip) =>
    if tf_period != "Chart TF"
        switch tf_period
            "Minute" => str.tostring(tf_multip)  + "min"
            "Hour"   => str.tostring(tf_multip)  + "H"
            "Day"    => str.tostring(tf_multip)  + "D"
            "Week"   => str.tostring(tf_multip)  + "W"
            "Month"  => str.tostring(tf_multip)  + "M"
    
    else 
        switch 
            timeframe.isminutes and  timeframe.multiplier % 60 != 0 
                                 => str.tostring(timeframe.multiplier)    + "min"
                                 
            timeframe.isminutes and  timeframe.multiplier % 60 == 0 
                                 => str.tostring(timeframe.multiplier/60) + "H"
                                 
            timeframe.isdaily    => str.tostring(timeframe.multiplier)    + "D"
            timeframe.isweekly   => str.tostring(timeframe.multiplier)    + "W"
            timeframe.ismonthly  => str.tostring(timeframe.multiplier)    + "M"


// TimeFrame OutPut
tf_form = array.from(tf_form(tf_period1, tf_multip1))
// Get Table Position
table_pos(p) => 
    switch p
        "Top Right"     => position.top_right     
        "Middle Right"  => position.middle_right   
        "Bottom Right"  => position.bottom_right  
        "Top Center"    => position.top_center    
        "Middle Center" => position.middle_center
        "Bottom Center" => position.bottom_center
        "Top Left"      => position.top_left      
        "Middle Left"   => position.middle_left 
        => position.bottom_left


// Get Table Size
table_size(s) => 
    switch s
        "Auto"   => size.auto   
        "Huge"   => size.huge   
        "Large"  => size.large  
        "Normal" => size.normal 
        "Small"  => size.small
        => size.tiny

//SFP

ph = ta.pivothigh(plen, 0)
pl = ta.pivotlow(plen, 0)

f_get_candle(_index) =>
    [open[_index], high[_index], low[_index], close[_index], bar_index[_index]]
    

f_is_candle_up(_open, _close) =>
    _open < _close
    

f_sfp() =>
    [so, sh, sl, sc, si] = f_get_candle(0)
    
    // High SFP
    hc1 = ph
    maxp = high[1]
    hc2 = false
    hx = 0
    hy = 0.0
    for i=1 to lookback
        [co, ch, cl, cc, ci] = f_get_candle(i)
        if ch >= sh
            break
        if ch < sh and ch > math.max(so, sc) and ph[bar_index - ci] and ch > maxp
            hc2 := true
            hx := bar_index[i]
            hy := ch
        if ch > maxp
            maxp := ch
    
    hcs = hc1 and hc2
    
    // Low SFP
    lc1 = pl
    minp = low[1]
    lc2 = false
    lx = 0
    ly = 0.0
    for i=2 to lookback
        [co, ch, cl, cc, ci] = f_get_candle(i)
        if cl < sl
            break
        if sl < cl and math.min(so, sc) > cl and pl[bar_index - ci] and cl < minp
            lc2 := true
            lx := bar_index[i]
            ly := cl
        if cl < minp
            minp := cl
    
    
    lcs = lc1 and lc2
    
    [hcs, hx, hy, lcs, lx, ly]

//MSB

isMatching(_str, _arr) =>
    if _str == ""
        false
    else
        hhll_join = array.join(_arr, ",")
        _temp0 = str.replace_all(_str, " ", "")
        _temp1 = str.upper(_str)
        _temp2 = str.split(_str, ",")
        array.reverse(_temp2)
        _temp3 = array.join(_temp2, ",")
        str.startswith(hhll_join, _temp3)

addToString(_str, _val) =>
    _temp0 = _str == "" ? _str : _str + "\n"
    _temp0 + _val

add_to_zigzag1(arr, value) =>
    array.unshift(arr, value)
    if array.size(arr) > max_array_size
        array.pop(arr)

update_zigzag1(arr, value, direction, _skip=false) =>
    if array.size(arr) == 0
        add_to_zigzag1(arr, value)
    else
        if _skip
            array.set(arr, 0, value)
        else if direction == "ph" and value > array.get(arr, 0) or direction == "pl" and value < array.get(arr, 0)
            array.set(arr, 0, value)
        0.

//Calculate Pivot

float pivoth = ta.highestbars(high, length) == 0 ? high : na
float pivotl = ta.lowestbars(low, length) == 0 ? low : na

var direction = "na"
iff_ = pivotl and na(pivoth) ? "pl" : direction
direction := pivoth and na(pivotl) ? "ph" : iff_

//ZigZag vars

var zigzag_price = array.new_float(0)
oldzigzag_price = array.copy(zigzag_price)

var zigzag_barindex = array.new_int(0)
oldzigzag_barindex = array.copy(zigzag_barindex)

var zigzag_hhll = array.new_string(0)
oldzigzag_hhll = array.copy(zigzag_hhll)

direction_changed = direction != direction[1] //ta.change(direction)
if pivoth or pivotl
    if direction_changed
        add_to_zigzag1(zigzag_price, direction == "ph" ? pivoth : pivotl)
        add_to_zigzag1(zigzag_barindex, bar_index)

        if array.size(zigzag_price) >= 3
            _text = direction == "ph" ? array.get(zigzag_price, 0) > array.get(zigzag_price, 2) ? 'HH' : 'LH' : array.get(zigzag_price, 0) < array.get(zigzag_price, 2) ? 'LL' : 'HL'
            add_to_zigzag1(zigzag_hhll, _text)
    else
        update_zigzag1(zigzag_price, direction == "ph" ? pivoth : pivotl, direction)
        update_zigzag1(zigzag_barindex, bar_index, direction, true)

        if array.size(zigzag_price) >= 3
            _text = direction == "ph" ? array.get(zigzag_price, 0) > array.get(zigzag_price, 2) ? 'HH' : 'LH' : array.get(zigzag_price, 0) < array.get(zigzag_price, 2) ? 'LL' : 'HL'
            array.set(zigzag_hhll, 0, _text)
        

// Get only symbol
only_symbol(s) => 
    array.get(str.split(s, ":"), 1)

// --------------- Plot ---------------
if array.size(zigzag_price) >= 3 and array.size(zigzag_hhll) >= 3
    var line zzline = na
    var label zzlabel = na
    var label pattern_label_bull = na
    var label pattern_label_bear = na
    
    price0 = array.get(zigzag_price, 0)
    price1 = array.get(zigzag_price, 1)
    price2 = array.get(zigzag_price, 2)
    old_price0 = array.get(oldzigzag_price, 0)
    old_price1 = array.get(oldzigzag_price, 1)

    barindex0 = math.round(array.get(zigzag_barindex, 0))
    barindex1 = math.round(array.get(zigzag_barindex, 1))
    old_barindex0 = math.round(array.get(oldzigzag_barindex, 0))
    old_barindex1 = math.round(array.get(oldzigzag_barindex, 1))

    zigzag0 = array.get(zigzag_hhll, 0)
    zigzag1 = array.get(zigzag_hhll, 1)
    old_zigzag0 = array.get(oldzigzag_hhll, 0)
    old_zigzag1 = array.get(oldzigzag_hhll, 1)
    
    hhlltxt = direction == "ph" ? price0 > price2 ? 'HH' : 'LH' : price0 < price2 ? 'LL' : 'HL'
    txtcol = direction == "ph" ? price0 > price2 ? upcol : dncol : price0 < price2 ? dncol : upcol


    if price0 != old_price0 and barindex0 != old_barindex0
        // Update line and label
        if price1 == old_price1 and barindex1 == old_barindex1
            line.delete(zzline)
            label.delete(zzlabel)
            label.delete(pattern_label_bull)
            label.delete(pattern_label_bear)

        // plot lines
        if showzz1
            zzline := line.new(
                 x1=barindex0, y1=price0,
                 x2=barindex1, y2=price1,
                 color=direction == "ph" ? upcol : dncol,
                 width=zzwidth,
                 style=zzstyle == 'Dashed' ? line.style_dashed : line.style_dotted)
        
        // plot labels
        if showhhll1
            zzlabel := label.new(
                 x=barindex0,
                 y=price0,
                 text=hhlltxt,
                //  color=labelcol,
                 textcolor=txtcol,
                 yloc=direction == "ph" ? yloc.abovebar : yloc.belowbar,
                 style=label.style_none)
        
        // Plot pattern matches
        if showPatternMatch
            bull1Met = bullBool1 ? isMatching(bullCond1, zigzag_hhll) : false
            bull2Met = bullBool2 ? isMatching(bullCond2, zigzag_hhll) : false
            bull3Met = bullBool3 ? isMatching(bullCond3, zigzag_hhll) : false
            bull4Met = bullBool4 ? isMatching(bullCond4, zigzag_hhll) : false
            bull5Met = bullBool5 ? isMatching(bullCond5, zigzag_hhll) : false
            bull6Met = bullBool6 ? isMatching(bullCond6, zigzag_hhll) : false
            bull7Met = bullBool7 ? isMatching(bullCond7, zigzag_hhll) : false

            bear1Met = bearBool1 ? isMatching(bearCond1, zigzag_hhll) : false
            bear2Met = bearBool2 ? isMatching(bearCond2, zigzag_hhll) : false
            bear3Met = bearBool3 ? isMatching(bearCond3, zigzag_hhll) : false
            bear4Met = bearBool4 ? isMatching(bearCond4, zigzag_hhll) : false
            bear5Met = bearBool5 ? isMatching(bearCond5, zigzag_hhll) : false
            bear6Met = bearBool6 ? isMatching(bearCond6, zigzag_hhll) : false
            bear7Met = bearBool7 ? isMatching(bearCond7, zigzag_hhll) : false

            bullPatternText = ""
            bullPatternText := bull1Met ? addToString(bullPatternText, bullTxt1) : bullPatternText
            bullPatternText := bull2Met ? addToString(bullPatternText, bullTxt2) : bullPatternText
            bullPatternText := bull3Met ? addToString(bullPatternText, bullTxt3) : bullPatternText
            bullPatternText := bull4Met ? addToString(bullPatternText, bullTxt4) : bullPatternText
            bullPatternText := bull5Met ? addToString(bullPatternText, bullTxt5) : bullPatternText
            bullPatternText := bull6Met ? addToString(bullPatternText, bullTxt6) : bullPatternText
            bullPatternText := bull7Met ? addToString(bullPatternText, bullTxt7) : bullPatternText

            bearPatternText = ""
            bearPatternText := bear1Met ? addToString(bearPatternText, bearTxt1) : bearPatternText
            bearPatternText := bear2Met ? addToString(bearPatternText, bearTxt2) : bearPatternText
            bearPatternText := bear3Met ? addToString(bearPatternText, bearTxt3) : bearPatternText
            bearPatternText := bear4Met ? addToString(bearPatternText, bearTxt4) : bearPatternText
            bearPatternText := bear5Met ? addToString(bearPatternText, bearTxt5) : bearPatternText
            bearPatternText := bear6Met ? addToString(bearPatternText, bearTxt6) : bearPatternText
            bearPatternText := bear7Met ? addToString(bearPatternText, bearTxt7) : bearPatternText

        
            pattern_label_bull := label.new(
                 barindex0,
                 y=price0,
                 yloc=yloc.belowbar,
                 text=bullPatternText != "" ? bullPatternText : "",
                 color=uplabelcol,
                 textcolor=uplabeltextcol,
                 style=bullPatternText != "" ? label.style_label_up : label.style_none)
        
            pattern_label_bear := label.new(
                 barindex0,
                 y=price0,
                 yloc=yloc.abovebar,
                 text=bearPatternText != "" ? bearPatternText : "",
                 color=dnlabelcol,
                 textcolor=dnlabeltextcol,
                 style=bearPatternText != "" ? label.style_label_down : label.style_none)            
// --------------- Plot ---------------


screener_func(flag) =>
    out_screener_func = array.new_string(5, string(na))
    
    if flag 
        // Price
        price = math.round_to_mintick(close)
        array.set(out_screener_func, 0, str.tostring(price))
        
        // PHL
        bull1Met = bullBool1 ? isMatching(bullCond1, zigzag_hhll) : false
        bull2Met = bullBool2 ? isMatching(bullCond2, zigzag_hhll) : false
        bull3Met = bullBool3 ? isMatching(bullCond3, zigzag_hhll) : false
        bull4Met = bullBool4 ? isMatching(bullCond4, zigzag_hhll) : false
        bull5Met = bullBool5 ? isMatching(bullCond5, zigzag_hhll) : false

        phl = false

        if bull1Met or bull2Met or bull3Met or bull4Met or bull5Met
            phl := true
        array.set(out_screener_func, 1, str.tostring(phl))
        
        //SFP

        [hsfp, hx, hy, lsfp, lx, ly] = f_sfp()
    
        hsfp := is_opposite ? hsfp and open > close : hsfp
        lsfp := is_opposite ? lsfp and open < close : lsfp
        
        sfp = false
        if hsfp
            sfp := true
        if lsfp 
            sfp := true
            
        array.set(out_screener_func, 2, str.tostring(sfp))
    
        //MSB

        bull7Met = bullBool7 ? isMatching(bullCond7, zigzag_hhll) : false
        bear7Met = bearBool7 ? isMatching(bearCond7, zigzag_hhll) : false

        bos = false

        if bull7Met or bear7Met
            bos := true
            
        array.set(out_screener_func, 3, str.tostring(bos))

        //PLH

        bear1Met = bearBool1 ? isMatching(bearCond1, zigzag_hhll) : false
        bear2Met = bearBool2 ? isMatching(bearCond2, zigzag_hhll) : false
        bear3Met = bearBool3 ? isMatching(bearCond3, zigzag_hhll) : false
        bear4Met = bearBool4 ? isMatching(bearCond4, zigzag_hhll) : false
        bear5Met = bearBool5 ? isMatching(bearCond5, zigzag_hhll) : false

        plh = false

        if bear1Met or bear2Met or bear3Met or bear4Met or bear5Met
            plh := true

        array.set(out_screener_func, 4, str.tostring(plh))






        
    out_screener_func


////////////////////////////// ~~~~~~~~~~~~~~~~~~ \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\
//                               Importing Data                               \\
////////////////////////////// ~~~~~~~~~~~~~~~~~~ \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\
s01_TF1 = request.security(ticker.modify(s01, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u01))
s02_TF1 = request.security(ticker.modify(s02, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u02))
s03_TF1 = request.security(ticker.modify(s03, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u03))
s04_TF1 = request.security(ticker.modify(s04, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u04))
s05_TF1 = request.security(ticker.modify(s05, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u05))
s06_TF1 = request.security(ticker.modify(s06, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u06))
s07_TF1 = request.security(ticker.modify(s07, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u07))
s08_TF1 = request.security(ticker.modify(s08, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u08))
s09_TF1 = request.security(ticker.modify(s09, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u09))
s10_TF1 = request.security(ticker.modify(s10, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u10))

s01_TF2 = request.security(ticker.modify(s01, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u01))
s02_TF2 = request.security(ticker.modify(s02, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u02))
s03_TF2 = request.security(ticker.modify(s03, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u03))
s04_TF2 = request.security(ticker.modify(s04, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u04))
s05_TF2 = request.security(ticker.modify(s05, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u05))
s06_TF2 = request.security(ticker.modify(s06, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u06))
s07_TF2 = request.security(ticker.modify(s07, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u07))
s08_TF2 = request.security(ticker.modify(s08, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u08))
s09_TF2 = request.security(ticker.modify(s09, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u09))
s10_TF2 = request.security(ticker.modify(s10, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u10))

s01_MSB = request.security(ticker.modify(s01, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u01))
s02_MSB = request.security(ticker.modify(s02, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u02))
s03_MSB = request.security(ticker.modify(s03, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u03))
s04_MSB = request.security(ticker.modify(s04, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u04))
s05_MSB = request.security(ticker.modify(s05, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u05))
s06_MSB = request.security(ticker.modify(s06, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u06))
s07_MSB = request.security(ticker.modify(s07, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u07))
s08_MSB = request.security(ticker.modify(s08, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u08))
s09_MSB = request.security(ticker.modify(s09, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u09))
s10_MSB = request.security(ticker.modify(s10, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u10))

s01_SFP = request.security(ticker.modify(s01, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u01))
s02_SFP = request.security(ticker.modify(s02, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u02))
s03_SFP = request.security(ticker.modify(s03, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u03))
s04_SFP = request.security(ticker.modify(s04, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u04))
s05_SFP = request.security(ticker.modify(s05, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u05))
s06_SFP = request.security(ticker.modify(s06, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u06))
s07_SFP = request.security(ticker.modify(s07, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u07))
s08_SFP = request.security(ticker.modify(s08, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u08))
s09_SFP = request.security(ticker.modify(s09, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u09))
s10_SFP = request.security(ticker.modify(s10, syminfo.session), TF(tf_period1, tf_multip1), screener_func(u10))

////////////////////////////// ~~~~~~~~~~~~~~~~~~ \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\
//                                   OutPut                                   \\
////////////////////////////// ~~~~~~~~~~~~~~~~~~ \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\
// Set Table
var tbl = table.new(table_pos(in_table_pos),  9,  12, frame_color = #151715, 
         frame_width=1, border_width=1, border_color=color.new(color.white, 100))


// Set Up Screener Matrix
screener_mtx = matrix.new<string>(10,  8, na)


// Fill Up Matrix Cells 
mtx(mtxName, row, symbol, s_TF1, s_TF2, s_MSB, s_SFP)=>
    if not na(array.get(s_TF1, 0))
        matrix.set(mtxName, row,  0, symbol    ) //Symbol
        matrix.set(mtxName, row,  1, array.get(s_TF1, 0)) //Price
        
        matrix.set(mtxName, row,  2, array.get(s_TF1, 1)) //PHL
        matrix.set(mtxName, row,  5, array.get(s_SFP, 2)) //SFP
        matrix.set(mtxName, row,  6, array.get(s_MSB, 3)) //MSB
        matrix.set(mtxName, row,  7, array.get(s_TF2, 4)) //PLH






if barstate.islast 
    mtx(screener_mtx, 0, only_symbol(s01), s01_TF1, s01_TF2, s01_MSB, s01_SFP)
    mtx(screener_mtx, 1, only_symbol(s02), s02_TF1, s02_TF2, s02_MSB, s02_SFP)
    mtx(screener_mtx, 2, only_symbol(s03), s03_TF1, s03_TF2, s03_MSB, s03_SFP)
    mtx(screener_mtx, 3, only_symbol(s04), s04_TF1, s04_TF2, s04_MSB, s04_SFP)
    mtx(screener_mtx, 4, only_symbol(s05), s05_TF1, s05_TF2, s05_MSB, s05_SFP)
    mtx(screener_mtx, 5, only_symbol(s06), s06_TF1, s06_TF2, s06_MSB, s06_SFP)
    mtx(screener_mtx, 6, only_symbol(s07), s07_TF1, s07_TF2, s07_MSB, s07_SFP)
    mtx(screener_mtx, 7, only_symbol(s08), s08_TF1, s08_TF2, s08_MSB, s08_SFP)
    mtx(screener_mtx, 8, only_symbol(s09), s09_TF1, s09_TF2, s09_MSB, s09_SFP)
    mtx(screener_mtx, 9, only_symbol(s10), s10_TF1, s10_TF2, s10_MSB, s10_SFP)
    
    
    //Remove NaN rows
    q= 0 
    while q <= matrix.rows(screener_mtx) - 1 and matrix.rows(screener_mtx) > 1
        if na(matrix.get(screener_mtx, q, 0))
            matrix.remove_row(screener_mtx, q)
            q := q
        else 
            q := q + 1

    // Fill up Table
    // Columns title 

    // Merge Columns title cells
  
    table.cell(tbl,  0, 0, 'Symbol',     bgcolor   = column_title_bgcol, 
                 text_color = cell_txtcol, text_size = table_size(in_table_size))
    table.cell(tbl,  0, 1, '', bgcolor   = column_title_bgcol)
    
    table.cell(tbl,  1, 0, 'Price',      bgcolor   = column_title_bgcol, 
                 text_color = cell_txtcol, text_size = table_size(in_table_size))
    table.cell(tbl,  1, 1, '', bgcolor   = column_title_bgcol)
    
    table.cell(tbl,  3, 0, 'PHL',        bgcolor   = column_title_bgcol, text_color = cell_txtcol, text_size = table_size(in_table_size))
    table.cell(tbl,  3, 1, '', bgcolor   = column_title_bgcol)

    table.cell(tbl,  4, 0, 'SFP',      bgcolor   = column_title_bgcol, 
                 text_color = cell_txtcol, text_size = table_size(in_table_size))
    table.cell(tbl,  4, 1, '', bgcolor   = column_title_bgcol)

    table.cell(tbl,  5, 0, 'BoS',      bgcolor   = column_title_bgcol, 
                 text_color = cell_txtcol, text_size = table_size(in_table_size))
    table.cell(tbl,  5, 1, '', bgcolor   = column_title_bgcol)
    
    table.cell(tbl,  6, 0, 'PLH',      bgcolor   = column_title_bgcol, 
                 text_color = cell_txtcol, text_size = table_size(in_table_size))
    table.cell(tbl,  6, 1, '', bgcolor   = column_title_bgcol)
    




    table.merge_cells(tbl,  0, 0,  0,  1)
    table.merge_cells(tbl,  1, 0,  1,  1)
    table.merge_cells(tbl,  3, 0,  3,  1)
    table.merge_cells(tbl,  4, 0,  4,  1)
    table.merge_cells(tbl,  5, 0,  5,  1)
    table.merge_cells(tbl,  6, 0,  6,  1)




    

    // TimeFrame 

             
    // Output Values
    max_row = 1
    for i = 0 to matrix.rows(screener_mtx) - 1
    
        if not na(matrix.get(screener_mtx, i, 1)) 
            // Symbol 
            table.cell(tbl, 0, i + 2, matrix.get(screener_mtx, i, 0), 
                 text_halign = text.align_center, bgcolor = column_title_bgcol, 
                 text_color = cell_txtcol, text_size = table_size(in_table_size))
        
            // Price
            table.cell(tbl, 1, i + 2, matrix.get(screener_mtx, i, 1), 
                 text_halign = text.align_center, bgcolor = cell_bgcol, 
                 text_color = cell_txtcol, text_size = table_size(in_table_size))
            
            //PHL

            table.cell(tbl, 3, i + 2, matrix.get(screener_mtx, i, 2) == "true" ? "true" : "",
                 text_halign = text.align_center, bgcolor = matrix.get(screener_mtx, i, 2) == "true" ? color.green : matrix.get(screener_mtx, i, 2) == "false" ? color.gray : color.gray, 
                 text_color = cell_txtcol, text_size = table_size(in_table_size))

            //SFP

            table.cell(tbl, 4, i + 2, matrix.get(screener_mtx, i, 5) == "true" ? "true" : "", 
                 text_halign = text.align_center, bgcolor = matrix.get(screener_mtx, i, 5) == "true" ? color.green : matrix.get(screener_mtx, i, 5) == "false" ? color.gray : color.gray,  
                 text_color = cell_txtcol, text_size = table_size(in_table_size))

            //MSB

            table.cell(tbl, 5, i + 2, text = matrix.get(screener_mtx, i, 6) == "true" ? "true" : "", 
                 text_halign = text.align_center, bgcolor = matrix.get(screener_mtx, i, 6) == "true" ? color.green : matrix.get(screener_mtx, i, 6) == "false" ? color.gray : color.gray,  
                 text_color = cell_txtcol, text_size = table_size(in_table_size))

            // PLH

            table.cell(tbl, 6, i + 2, text = matrix.get(screener_mtx, i, 7) == "true" ? "true" : "", 
                 text_halign = text.align_center, bgcolor = matrix.get(screener_mtx, i, 7) == "true" ? color.green : matrix.get(screener_mtx, i, 7) == "false" ? color.gray : color.gray,  
                 text_color = cell_txtcol, text_size = table_size(in_table_size))


        
            max_row := max_row + 1

 // Separator 
    for k = 0 to max_row
        table.cell(tbl, 2, k, "",  bgcolor = column_title_bgcol, text_size = size.tiny)

    table.merge_cells(tbl,  2, 0,  2,  max_row)
    

