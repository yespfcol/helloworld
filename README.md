# helloworld
a HelloWorld project
package com.yesp.skindemo2;

import android.content.Context;
import android.content.res.ColorStateList;
import android.text.TextUtils;
import android.util.AttributeSet;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.TextView;
import android.widget.Toast;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;

import java.util.List;

public class NotifyBarLayoutView extends LinearLayout implements View.OnClickListener {
    private Context mhg;
    private List<Notify> notifyList;
    private int TYPE_XIALA = 1;
    private int TYPE_SHOUQI = 2;
    private ImageView viewXiaLa;
    private ImageView viewShouqi;

    public NotifyBarLayoutView(Context context) {
        super(context);
        this.mhg = context;
    }

    public NotifyBarLayoutView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    public NotifyBarLayoutView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    public NotifyBarLayoutView(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);
    }

    private int itemColor = -100;
    public void setDataAndColor(List<Notify> dataList, int color){
        this.itemColor = color;
        setData(dataList);
    }
    public void setData(List<Notify> dataList) {
        this.notifyList = dataList;
        if (dataList.isEmpty()) {
            hide();
            return;
        }
        if (this != null && this.getChildCount() > 0) {
            this.removeAllViews();
        }
        for (int i = 0; i < dataList.size(); i++) {
            View child = LayoutInflater.from(getContext()).inflate(R.layout.item_notify, null);
            this.addView(child);
            setChildData(child, i);
        }
        shouqi();
    }

    private void setChildData(@NonNull View child, int position) {
        TextView tvLabel = child.findViewById(R.id.tv_label);
        TextView tvTopic = child.findViewById(R.id.tv_nofity);
        ImageView imgSpeed = child.findViewById(R.id.img_speed);
        if(itemColor != -100){
        tvLabel.setBackgroundTintList(ColorStateList.valueOf(itemColor));
        tvTopic.setTextColor(itemColor);
        imgSpeed.setBackgroundTintList(ColorStateList.valueOf(itemColor));
        }
        Notify notify = notifyList.get(position);
        switch (notify.getType()) {
            case Notify.TYPE_TOP:
                tvLabel.setText("置顶");
                break;
            case Notify.TYPE_ACTIVITY:
                tvLabel.setText("活动");
                break;
            case Notify.TYPE_HOT:
                tvLabel.setText("热门");
                break;
            default:
                break;
        }
        tvTopic.setText(notify.getName());
        if (notifyList.size() == 1 && position == 0) {
            imgSpeed.setVisibility(View.GONE);
            Log.d("yesp", "position one = " + position);
        } else if (notifyList.size() > 1 && position == 0) {
            imgSpeed.setVisibility(View.VISIBLE);
            imgSpeed.setBackgroundResource(R.drawable.ic_xiala);
            imgSpeed.setTag(TYPE_XIALA);
            viewXiaLa = imgSpeed;
            Log.d("yesp", "position one = " + position);
        } else if (notifyList.size() > 1 && position == notifyList.size() - 1) {
            imgSpeed.setVisibility(View.VISIBLE);
            imgSpeed.setBackgroundResource(R.drawable.ic_shouqi);
            imgSpeed.setClickable(true);
            imgSpeed.setTag(TYPE_SHOUQI);
            Log.d("yesp", "position last = " + position);
        } else {
            imgSpeed.setVisibility(View.INVISIBLE);
            Log.d("yesp", "position else = " + position);
//            imgSpeed.setClickable(false);
        }
        Log.d("yesp", "position = " + position);
        imgSpeed.setOnClickListener(this);
        child.setOnClickListener(this);
        child.setTag(notify.getName());
    }

    public void xiala() {
        if (notifyList.isEmpty() || this.getChildCount() < 1) {
            return;
        }
        for (int i = 0; i < getChildCount(); i++) {
            getChildAt(i).setVisibility(View.VISIBLE);
        }
        if (viewXiaLa != null) {
            viewXiaLa.setVisibility(View.INVISIBLE);
            viewXiaLa.setClickable(false);
        }
    }

    public void shouqi() {
        if (notifyList.isEmpty() || this.getChildCount() < 1) {
            return;
        }
        for (int i = 0; i < getChildCount(); i++) {
            int itemShow = i == 0 ? View.VISIBLE : View.GONE;
            getChildAt(i).setVisibility(itemShow);
        }
        if (viewXiaLa != null) {
            viewXiaLa.setVisibility(View.VISIBLE);
            viewXiaLa.setClickable(true);
        }
    }

    public void hide() {
        this.setVisibility(View.GONE);
    }

    public void show() {
        this.setVisibility(View.VISIBLE);
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.item_notify:
                new Toast(getContext()).makeText(getContext(), (String) view.getTag(), Toast.LENGTH_SHORT).show();
                break;
            case R.id.img_speed:
                int imgType = (int) view.getTag();
                if (imgType == TYPE_XIALA) {
                    xiala();
                } else if (imgType == TYPE_SHOUQI) {
                    shouqi();
                }
        }
    }
}
