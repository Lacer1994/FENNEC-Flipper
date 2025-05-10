function main() {
    var cmp = app.project.activeItem;
    var allDataOut = [];

    function FennecKeyframe(keyframeIndex, keyframeTime, keyframeValue, keyframeInTemporalEase, keyframeOutTemporalEase, keyframeInInterpolationType,keyframeOutInterpolationType,keyframeInSpatialTangent,keyframeOutSpatialTangent){
            this.keyframeIndex = keyframeIndex;
            this.keyframeTime = keyframeTime;
            this.keyframeValue = keyframeValue;
            this.keyframeInTemporalEase = keyframeInTemporalEase;
            this.keyframeOutTemporalEase = keyframeOutTemporalEase;
            this.keyframeInInterpolationType = keyframeInInterpolationType;
            this.keyframeOutInterpolationType = keyframeOutInterpolationType;
            this.keyframeInSpatialTangent = keyframeInSpatialTangent;
            this.keyframeOutSpatialTangent = keyframeOutSpatialTangent;
            this.setkeyframeInSpatialTangent = function(keyframeInSpatialTangent) {
                this.keyframeInSpatialTangent = keyframeInSpatialTangent;
            };
            this.setkeyframeOutSpatialTangent = function(keyframeOutSpatialTangent) {
                    this.keyframeOutSpatialTangent = keyframeOutSpatialTangent;                
            };
        };

    function setFirstKeyframe(definedFirstKeyFrame, valueToCheck){
        if (definedFirstKeyFrame<valueToCheck){
            return definedFirstKeyFrame;
        } else {
            return valueToCheck;
        };
    };

    function setLastKeyframe(definedLastKeyFrame, valueToCheck){
        if (definedLastKeyFrame>valueToCheck){
            return definedLastKeyFrame;
        } else {
            return valueToCheck;
        };
    };

    function getPropertyPath(property) {
        var path = [];
        while (property) {
            path.unshift(property.matchName);
            property = property.parentProperty;
        }
        return path;
    };

    function getPropertyFromPath(layer, path) {
        var currentProperty = layer;
        for (var i = 1; i < path.length; i++) { // Inizia da 1 per saltare il layer
            //alert(path[i]);
            currentProperty = currentProperty.property(path[i]);
        }
        return currentProperty;
    };

    function findFisrtKeyFrame(comp, property){
                var fkf = comp.displayStartTime;
                for (var iFirst = property.numKeys; iFirst >= 1; iFirst-=1) {
                    if (property.keySelected(iFirst) == true) {
                        var timeK = property.keyTime(iFirst);
                        fkf = setFirstKeyframe(fkf,timeK);
                    };
                };
                return fkf;
    };

    function findLastKeyFrame(comp, property){
                var lkf = comp.displayStartTime+comp.duration;
                for (var iFirst = property.numKeys; iFirst >= 1; iFirst-=1) {
                    if (property.keySelected(iFirst) == true) {
                        var timeK = property.keyTime(iFirst);
                        lkf = setLastKeyframe(lkf,timeK);
                    };
                };
                return lkf;
    };

    function storeKeyFrames(property){
                var keyFramesToStore = [];
                for (var ic = property.numKeys; ic >= 1; ic-=1) {
                    if (property.keySelected(ic) == true) {
                        var allKeyFProperties = [];
                        var timeK = property.keyTime(ic);
                        var valueK = property.keyValue(ic);
                        var easingInK = property.keyInTemporalEase(ic);
                        var easingOutK = property.keyOutTemporalEase(ic);
                        var interpolationInK = property.keyInInterpolationType(ic);
                        var interpolationOutK = property.keyOutInterpolationType(ic);
                        var empty = undefined;

                        var fennecKey = new FennecKeyframe (ic,timeK,valueK,easingInK,easingOutK,interpolationInK,interpolationOutK,undefined,undefined);

                        if (property.matchName == "ADBE Position") {
                            if (property.isSpatial) {
                                if (typeof property.keyInSpatialTangent === 'function') {
                                    var inSpatialTangent = property.keyInSpatialTangent(ic);
                                    fennecKey.setkeyframeInSpatialTangent(inSpatialTangent);
                                };
                                if (typeof property.keyOutSpatialTangent === 'function') {
                                    var outSpatialTangent = property.keyOutSpatialTangent(ic);
                                    fennecKey.setkeyframeOutSpatialTangent(outSpatialTangent);
                                };
                            };
                        };
                        keyFramesToStore.push(fennecKey);
                    };
                };
                return keyFramesToStore;
    };

    function storeData (comp){
        var allDataIn = [];
    for (var ia = 0; ia < comp.selectedLayers.length; ia++) {
        var layer = comp.selectedLayers[ia];
        var selectedProperties = layer.selectedProperties;
        var propertiesOfTheLayer = [];
        for (var ib = 0; ib < selectedProperties.length; ib++) {
            var property = selectedProperties[ib];
            if (property.numKeys > 0) {
                var storedKeyframes = storeKeyFrames(property);
                var propertyPath = getPropertyPath(property);
                var propertyAndItsKeysStored = [propertyPath,storedKeyframes];
                propertiesOfTheLayer.push(propertyAndItsKeysStored);
            };
        };
        //alert(propertiesOfTheLayer);
        if (propertiesOfTheLayer[0] != undefined) {
            var newlayerdata = [layer.index,propertiesOfTheLayer];
            allDataIn.push(newlayerdata);
        };
    };
    return allDataIn;
    };
   
   function removeStoredKeyFrame(data, comp){
        //alert("PRINTDATA numb of selected layer: " + data.length)
        for (var ia = 0; ia<data.length; ia++){
            var layarIndex = data[ia][0];
            var layerProperties = data[ia][1];
            for (var ib = 0; ib<layerProperties.length; ib++){
                var propertyPath = layerProperties[ib][0];
                var propertyKeyframes = layerProperties[ib][1];
                for (var ic = 0; ic<propertyKeyframes.length; ic++) {
                    var analyzedKeyframe = propertyKeyframes[ic];
                    //alert("FINO A QUI Keyframe " + analyzedKeyframe.keyframeIndex);
                    var frozenLayer = comp.layer(layarIndex);
                    var frozenProperty = getPropertyFromPath(frozenLayer,propertyPath);
                    frozenProperty.removeKey(analyzedKeyframe.keyframeIndex);
                };
            };
        };
    };

    function setFirstAndLastFrame(data, comp){
        var fkf = comp.displayStartTime+comp.duration;
        var lkf = comp.displayStartTime;
        for (var ia = 0; ia<data.length; ia++){
            layarIndex = data[ia][0];
            layerProperties = data[ia][1];
            for (var ib = 0; ib<layerProperties.length; ib++){
                var propertyPath = layerProperties[ib][0];
                var propertyKeyframes = layerProperties[ib][1];
                var tempTL = propertyKeyframes[0].keyframeTime;
                var tempTF = propertyKeyframes[propertyKeyframes.length-1].keyframeTime;
                fkf = setFirstKeyframe(fkf,tempTF);
                lkf = setLastKeyframe(lkf,tempTL);
            };
        };
        var outputFL = [fkf,lkf];
        return outputFL;
    };

    function flipStoredKeyFrame(data, comp, middle){
            for (var ia = 0; ia<data.length; ia++){
                var layarIndex = data[ia][0];
                var layerProperties = data[ia][1];
                for (var ib = 0; ib<layerProperties.length; ib++){
                    var propertyPath = layerProperties[ib][0];
                    var propertyKeyframes = layerProperties[ib][1];
                    for (var ic = 0; ic<propertyKeyframes.length; ic++) {
                        var analyzedKeyframe = propertyKeyframes[ic];
                        var frozenLayer = comp.layer(layarIndex);
                        var frozenProperty = getPropertyFromPath(frozenLayer,propertyPath);
                        var originalTime = analyzedKeyframe.keyframeTime;
                        var flippedTime;
                        if (originalTime<middle){
                            flippedTime = middle+(middle-originalTime);
                        } else if (originalTime>middle){
                            flippedTime = middle-(originalTime-middle);
                        } else {
                            flippedTime = originalTime;
                        };
                        frozenProperty.setValueAtTime(flippedTime,analyzedKeyframe.keyframeValue);
                        var tempIndex = frozenProperty.nearestKeyIndex(flippedTime);
                        frozenProperty.setTemporalEaseAtKey(tempIndex, analyzedKeyframe.keyframeOutTemporalEase, analyzedKeyframe.keyframeInTemporalEase);
                        frozenProperty.setInterpolationTypeAtKey(tempIndex, analyzedKeyframe.keyframeOutInterpolationType, analyzedKeyframe.keyframeInInterpolationType);
                        if(analyzedKeyframe.keyframeInSpatialTangent != undefined || analyzedKeyframe.keyframeOutSpatialTangent != undefined){
                            frozenProperty.setSpatialTangentsAtKey(tempIndex,analyzedKeyframe.keyframeOutSpatialTangent,analyzedKeyframe.keyframeInSpatialTangent);
                        };
                 
                        //frozenProperty.setInterpolationTypeAtKey(keyIndex, KeyframeInterpolationType.BEZIER, KeyframeInterpolationType.BEZIER);
                        //var tempTest = frozenProperty.keyTime(id+1);
                        /*for (var id = ic; id<frozenProperty.numKeys; id++ ){
                            if (Math.floor(tempTest*100) == Math.floor(flippedTime*100)) {
                            alert("YESSS");
                            } else {
                            alert("NOOOOO!!!");
                            };
                        };*/
                        //alert("TIME to check")
                        //alert(tempIndex);
                        //alert(tempTest);
                        //alert(flippedTime);
                        //alert("step")
                    };
                };
            };
        };

app.beginUndoGroup("FENNEC Flip");
allDataOut = storeData(cmp);
//alert("allData:   " + allDataOut.join("---\n"));
var framesFirstAndLast = setFirstAndLastFrame(allDataOut, cmp); //TEMP
var firstKeyFrame  = framesFirstAndLast[0];
var lastKeyFrame = framesFirstAndLast[1];
var middleTime = firstKeyFrame+((lastKeyFrame-firstKeyFrame)/2);
removeStoredKeyFrame(allDataOut, cmp);
flipStoredKeyFrame(allDataOut, cmp, middleTime);
/* alert("First key frame: " + firstKeyFrame);
alert("Last key frame: " + lastKeyFrame);
alert("Middle time: " + middleTime); */
app.endUndoGroup();
};
main();
